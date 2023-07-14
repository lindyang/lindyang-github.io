---
title: "Sketch of WTForms"
date: 2023-07-14T15:54:27+08:00
tags: [python]
---


```python
import logging
import itertools
from collections import OrderedDict

from paramiko.client import SSHClient, AutoAddPolicy
from paramiko.channel import ChannelFile, ChannelStderrFile


logger = logging


class UnsetValue:
    pass


unset_value = UnsetValue()


class StopValidation(Exception):
    pass


class UnboundField:
    _formfield = True
    creation_counter = 0

    def __init__(self, field_class, *args, **kwargs):
        UnboundField.creation_counter += 1
        self.field_class = field_class
        self.args = args
        self.kwargs = kwargs
        self.creation_counter = UnboundField.creation_counter

    def bind(self, form, field_label):
        return self.field_class(*self.args, _form=form, _name=field_label, **self.kwargs)

    def __repe__(self):
        return '<UnboundField(%s, %r, %r)>' % (self.field_class.__name__, self.args, self.kwargs)


class Field(object):
    raw_data = None
    validators = tuple()
    _formfield = True

    def __new__(cls, *args, **kwargs):
        if '_form' in kwargs and '_name' in kwargs:
            return super().__new__(cls)
        else:
            return UnboundField(cls, *args, **kwargs)

    def __init__(self, label, cmd, filters=tuple(), validators=None, default=None, _form=None, _name=None, **kwargs):
        """
        default: 是在cmd命令执行retcode非0(失败)
        """
        self.default = default
        self.filters = filters
        self.name = _name
        self.type = type(self).__name__

        self.validators = validators or self.validators

        self.id = id or self.name
        self.label = label
        self.cmd = cmd
        self.data = unset_value  # 没有一个合法的值之前, 设置为未知
        self.get_pty = kwargs.get('get_pty', False)

    def process_data(self, retcode, stdout, stderr):
        return NotImplementedError

    @staticmethod
    def _strip_filter(value):
        if value and hasattr(value, 'strip'):
            return value.strip()
        return value

    def process(self, retcode, stdout: ChannelFile, stderr: ChannelStderrFile):
        self.process_data(retcode=retcode, stdout=stdout, stderr=stderr)
        if self.data is not unset_value:
            filter_chain = itertools.chain((self._strip_filter,), self.filters)
            for filter in filter_chain:
                self.data = filter(self.data)

    def gettext(self, string):
        return string

    def validate(self, form, extra_cleaners):
        if self.data is not unset_value:
            chain = itertools.chain(self.validators, extra_cleaners)
            self._run_validation_chain(form, chain)
        elif self.default is not None:
            try:
                self.data = self.default
            except TypeError:
                self.data = self.default()

    def _run_validation_chain(self, form, validators):
        for validator in validators:
            try:
                validator(form, self)
            except StopValidation as e:
                if e.args and e.args[0]:
                    pass
                return True
            except ValueError:
                pass
        # ENF FOR
        return False


class StringField(Field):
    def process_data(self, stdout: ChannelFile, **kwargs):
        _stdout = stdout.read().decode().strip()
        if _stdout:
            self.data = _stdout


class JoinStringField(Field):
    sep = ';'

    def __init__(self, label=None, cmd=None, sep=None, **kwargs):
        super().__init__(label, cmd, **kwargs)
        if sep is not None:
            self.sep = sep

    def process_data(self, stdout: ChannelFile, **kwargs):
        _stdout = stdout.read().decode().strip()
        if _stdout:
            self.data = _stdout.replace('\n', self.sep)


class RetCodeField(Field):
    true_values = (0,)  # bash一般将返回码0作为成功

    def __init__(self, label=None, cmd=None, true_values=None, **kwargs):
        super().__init__(label, cmd, **kwargs)
        if true_values is not None:
            self.true_values = true_values

    def process_data(self, retcode, **kwargs):
        self.data = retcode in self.true_values


class IntegerField(Field):
    def __init__(self, label=None, cmd=None, sep=None, **kwargs):
        super().__init__(label, cmd, **kwargs)
        self.sep = sep

    def process_data(self, stdout, **kwargs):
        _stdout = stdout.read().decode().strip()
        if _stdout:
            value = _stdout.split(self.sep)[-1]
            try:
                self.data = int(value)
            except (ValueError, TypeError):
                logger.error('%s', self.gettext("Not a valid integer value"))


class NumberRange(object):
    def __init__(self, min=None, max=None, message=None):
        self.min = min
        self.max = max
        self.message = message

    def __call__(self, form, field):
        data = field.data
        if data is unset_value:
            return
        if (self.min is not None and data < self.min) or (self.max is not None and data > self.max):
            message = self.message
            if message is None:
                if self.max is None:
                    message = field.gettext('Number must be at least %(min)s.')
                elif self.min is None:
                    message = field.gettext('Number must be at most %(max)s.')
                else:
                    message = field.gettext('Number must be between %(min)s and %(max)s.')
                # logger.warn('%s', message % dict(min=self.min, max=self.max))
                # raise ValidationError(message % dict(min=self.min, max=self.max))
            field.data = False
            return
        # end if
        field.data = True


def get_group_name(string):
    if string.endswith('Form'):
        string = string[:-4]
    return string.lower()


CHECK_GROUP_TO_ARR = {}


class FormMeta(type):
    def __new__(cls, name, bases, attrs, label=None):
        return super().__new__(cls, name, bases, attrs)

    def __init__(cls, name, bases, attrs, label=None):
        type.__init__(cls, name, bases, attrs)
        if name == 'Form':
            return
        group_name_en = get_group_name(name)
        check_arr = CHECK_GROUP_TO_ARR.setdefault(group_name_en, [])
        fields = (x for x in dir(cls) if not x.startswith('_') and hasattr(getattr(cls, x), '_formfield'))
        for xname in fields:
            # if not xname.startswith('_'):
            #     unbound_field = getattr(cls, xname)
            #     if hasattr(unbound_field, '_formfield'):
            unbound_field = getattr(cls, xname)
            check_arr.append({
                "index": len(check_arr),
                "group_name_cn": label,
                "group_name_en": group_name_en,
                "key": xname,
                "name": unbound_field.args[0],
            })
        cls.label_en = group_name_en  # 英文名
        cls.label = label  # 中文名
        cls._unbound_fields = None
        cls._wtforms_meta = None

    def __call__(cls, *args, **kwargs):
        if cls._unbound_fields is None:
            fields = []
            for name in dir(cls):
                if not name.startswith('_'):
                    unbound_field = getattr(cls, name)
                    if hasattr(unbound_field, '_formfield'):
                        fields.append((name, unbound_field))
            # We keep the name as the second element of the sort
            # to ensure a stable sort.
            fields.sort(key=lambda x: (x[1].creation_counter, x[0]))
            cls._unbound_fields = fields
        # Create a subclass of the 'class Meta' using all the ancestors.
        if cls._wtforms_meta is None:
            bases = []
            for mro_class in cls.__mro__:
                if 'Meta' in mro_class.__dict__:
                    bases.append(mro_class.Meta)
            # end for
            cls._wtforms_meta = type('Meta', tuple(bases), {})
        return type.__call__(cls, *args, **kwargs)


class DefaultMeta(object):
    def bind_field(self, form, unbound_field: UnboundField, field_label):
        return unbound_field.bind(form=form, field_label=field_label)


class BaseForm(object):
    def __init__(self, fields, meta: DefaultMeta):
        self.meta = meta
        self._fields = OrderedDict()

        for name, unbound_field in fields:
            field = meta.bind_field(self, unbound_field, field_label=name)  # 做了一些奇怪的操作
            self._fields[name] = field

    def process(self, ssh: SSHClient, conf: str):
        for name, field, in self._fields.items():
            field = field  # type: Field
            try:
                field.cmd = field.cmd % (conf,)
            except TypeError:
                field.cmd = field.cmd
            # logger.debug('%s: %s', field.label, field.cmd)
            stdin, stdout, stderr = ssh.exec_command(field.cmd)
            field.process(
                retcode=stdout.channel.recv_exit_status(),
                stdout=stdout,
                stderr=stderr,
            )

    def validate(self, extra_validators):
        success = True
        for name, field in self._fields.items():
            extra = extra_validators.get(name, tuple())
            field = field  # type: Field
            if not field.validate(self, extra):
                success = False
        return success

    @property
    def data(self):
        # return dict((name, f.data) for name, f in self._fields.items())
        return dict((f.label, f.data) for name, f in self._fields.items() if f.data is not unset_value)


class Form(BaseForm, metaclass=FormMeta):
    Meta = DefaultMeta  # 这里有一个Mate

    def __init__(self, ssh, conf=''):
        meta_obj = self._wtforms_meta()
        super().__init__(self._unbound_fields, meta=meta_obj)
        self.ssh = ssh
        for name, field in self._fields.items():
            setattr(self, name, field)
        self.process(self.ssh, conf)

    def validate(self):
        extra = {}
        for name in self._fields:
            inline = getattr(self.__class__, 'validate_%s' % name, None)
            if inline is not None:
                extra.setdefault(name, []).append(inline)
        return super().validate(extra)


class AccountSecureForm(Form, label='账户安全策略'):
    open_sudo = RetCodeField('启用sudo命令', cmd='[ -f /etc/sudoers ]')
    pass_warn_age = IntegerField(
        '密码过期前警告天数>=7',
        cmd="grep -oE '^[[:blank:]]*PASS_WARN_AGE[[:blank:]]+[[:digit:]]+' /etc/login.defs",
        validators=[NumberRange(min=7)])
    login_timeout_lock = IntegerField(
        '登录超时锁定时间>=300',
        cmd="bash -lc 'echo $TMOUT'",
        validators=[NumberRange(min=300)],
        default=False,
    )
    permission_777 = JoinStringField(
        '权限为777的文件夹',
        cmd="bash -lc 'IFS=:; find -H $PATH -maxdepth 0 -perm 775 2>/dev/null'")


def conn(hostname):
    client = SSHClient()
    client.set_missing_host_key_policy(AutoAddPolicy())
    client.connect(hostname=hostname, port=22, username="root", password=" ")
    return client


def run():
    ssh = conn("192.168.1.12")
    form = AccountSecureForm(ssh)
    form.validate()
    print(form.data)


if __name__ == '__main__':
    run()
```

