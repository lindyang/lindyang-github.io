---
title: "Wtforms Analysis"
date: 2023-07-14T16:57:32+08:00
tags: [python]
---

后端经常使用 Form 作表单验证, 但是你知道
- 表单中的键值是怎么绑定到 Field 上的?
- Field 中的 validators, filters, default 调用顺序是什么?

要想回答以上问题, 就需要从源码说起.

### 先上伪源码为敬
```
class UnboundField(object):
    ⑨def bind(self, form, name, ...):
        return self.field_class(*self.args, **kw)

class DefaultMeta(object):
    ⑧def bind_field(self, form, unbound_field, options):
        return unbound_field.bind(form=form, **options)

class Field(object):
    def __new__(cls, *args, **kwargs):
        ⑩if '_form' in kwargs and '_name' in kwargs:
            return super(Field, cls).__new__(cls)
        ①else:  # 扫描到 name = StringField
            return UnboundField(cls, *args, **kwargs)

    ⑪def __init__(self, *args, **kwargs):
        pass

    def process(self, formdata, data=unset_value):
        if data is unset_value:
            设置 default
        if formdata is not None:
            有 key 时, default 所做的工作付诸东流
        self.data = filter(self.data)

    def validate(self, form, extra_validators=tuple()):
        itertools.chain(self.validators, extra_validators)


class FormMeta(type):
    ②def __init__(cls, name, bases, attrs):  # DemoForm 定义完毕
        type.__init__(cls, name, bases, attrs)
        cls._unbound_fields = None
        cls._wtforms_meta = None

    ③def __call__(cls, *args, **kwargs):  # DemoForm(...)
        设置 cls._unbound_fields
        设置 cls._wtforms_meta


class BaseForm(object):
    ⑥def __init__(self, ...):
        for:
            ⑦field = meta.bind_field(self, unbound_field, options)

    def process(self, formdata=None, obj=None, data=None, **kwargs):
        obj 和 (data, kwargs) 不含 name 时调用 field.process(formdata)

    def validate(self, extra_validators=None):
        field.validate(self, extra)


def with_metaclass(meta, base=object):
    return meta("NewBase", (base,), {})


class Form(with_metaclass(FormMeta, BaseForm)):
    ④def __init__(self, formdata=None, ...):  # 初始化 DemoForm(args)
        ⑤super(Form, self).__init__(self._unbound_fields, meta=meta_obj, prefix=prefix)
        self.process(formdata, obj, data=data, **kwargs)

    def validate(self):
        收集 extra
        return super(Form, self).validate(extra)
```

### 一言不合举例子
```
class DemoForm(Form):
    name = StringField("name", validators=[DataRequired()], filters=(), default=None)

form = DemoForm(args)
form.validate()
```

1. 扫描到 name = StringField, 调用 Field.`__new__`
2. DemoForm 定义完毕, 调用[元类](https://blog.ionelmc.ro/2015/02/09/understanding-python-metaclasses/) `FormMeta.__init__`
3. 遇到 form = DemoForm(args) 时:
  - 先调用 元类 `FormMeta.__call__`
  - 再调用 `Form.__init__`, 继而触发每个 `Field.process`
4. 运行 `form.validate()` 最终会调用到每个 `Field.validate`


