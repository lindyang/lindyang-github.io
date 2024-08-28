---
title: "Form"
date: 2023-07-28T14:43:04+08:00
tags: [python]
---


```
# coding: utf-8
import re

from sqlalchemy.orm.attributes import InstrumentedAttribute
from wtforms import Form, IntegerField, StringField
from wtforms.compat import string_types
from wtforms.validators import InputRequired, StopValidation, Optional, ValidationError, NumberRange

from app.config import conf
from app.db import to_list


class NonBlankRequired(InputRequired):
    def __call__(self, form, field):
        if not field.raw_data or \
                isinstance(field.raw_data[0], string_types) and \
                not field.raw_data[0].strip():
            if self.message is None:
                message = field.gettext('This field is required.')
            else:
                message = self.message

            field.errors[:] = []
            raise StopValidation(message)


class KeyOptional(Optional):
    def __call__(self, form, field):
        if field.raw_data == []:
            field.errors[:] = []
            raise StopValidation()


def _strip_filter(value):
    if value and hasattr(value, 'strip'):
        return value.strip()
    return value


class BaseForm(Form):
    class Meta(object):
        locales = ['zh_CN']

        def bind_field(self, form, unbound_field, options):
            if unbound_field.field_class.__name__ in ["FieldList", "PasswordField"]:
                return unbound_field.bind(form=form, **options)
            filters = unbound_field.kwargs.get('filters', [])
            filters.append(_strip_filter)
            return unbound_field.bind(form=form, filters=filters, **options)

    @property
    def errors(self):
        # f.label.text 获得汉字
        return {label: f.errors[0] for label, f in self._fields.items() if f.errors}  # pylint: disable=no-member


class AnyMixin:
    def non_exists(self):
        return not any(f.raw_data != [] for f in self._fields.values() if f.name != 'id')

    @property
    def data(self):
        return dict((name, f.data) for name, f in self._fields.items() if f.raw_data != [])


class PaginateForm(BaseForm):
    page = IntegerField('page', [KeyOptional(), NumberRange(min=1)], default=1)
    per_page = IntegerField('per_page', [KeyOptional(), NumberRange(min=1)], default=10)
    order_by = StringField('order_by')

    def __init__(self, *args, **kwargs):
        super(PaginateForm, self).__init__(*args, **kwargs)
        self.max_per_page = kwargs.get('max_per_page', conf.get('MAX_PER_PAGE', 1024))

    def validate_page(self, field):
        if field.data < 1:
            raise ValidationError('{} should >= 1'.format(field.label.text))

    def validate_order_by(self, field):
        if field.data:
            re_ = re.match(r'(?P<by>[a-z]\w*)(-(?P<order>ASC|DESC))?', field.data, re.I)
            if re_:
                self.order_by.data = re_.groupdict(default='DESC')  # {'by': 'id', 'order': 'DESC'}
            else:
                raise ValidationError('{}: `<column>[-(DESC|ASC)]`]'.format(field.label.text))

    def validate_per_page(self, field):
        if field.data < 1:
            raise ValidationError('{} should >= 1'.format(field.label.text))
        elif field.data > self.max_per_page:
            raise ValidationError('{} should <= {}'.format(field.label.text, self.max_per_page))

    def order_stmt(self, model):
        order_by = self.order_by.data
        if isinstance(order_by, dict):
            column = getattr(model, order_by['by'], None)
            if isinstance(column, InstrumentedAttribute):
                if order_by['order'].upper() == 'DESC':
                    return column.desc()
                return column.asc()
        return getattr(model, 'id').desc()

    @classmethod
    def to_dict(cls, pagination, excludes=None):
        return {
            'total': pagination.total,
            'page': pagination.page,
            'items': to_list(pagination.items, excludes),
            'per_page': pagination.per_page
        }
```
