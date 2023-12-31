---
title: "Metaclass"
date: 2023-07-28T16:31:03+08:00
tags: [python]
---


class Meta(type):
    @classmethod
    def __prepare__(mcs, name, bases, **kwargs):
        print('  Meta.__prepare__(mcs=%s, name=%r, bases=%s, **%s)' % (mcs, name, bases, kwargs))
        return {}

    def __new__(mcs, name, bases, attrs, **kwargs):
        print('  Meta.__new__(mcs=%s, name=%r, bases=%s, attrs=[%s], **%s)' % (mcs, name, bases, ','.join(attrs), kwargs))
        return super().__new__(mcs, name, bases, attrs)

    def __init__(cls, name, bases, attrs, **kwargs):
        print('  Meta.__init__(cls=%s, name=%r, bases=%s, attrs=[%s], **%s)' % (cls, name, bases, ', '.join(attrs), kwargs))
        return super().__init__(name, bases, attrs)

    def __call__(cls, *args, **kwargs):
        print('  Meta.__call__(cls=%s, args=%s, kwargs=%s)' % (cls, args, kwargs))
        return super().__call__(*args, **kwargs)


class Class(metaclass=Meta, extra=1):
    def __new__(cls, myarg):
        print('  Class.__new__(cls=%s, myarg=%s)' % (cls, myarg))
        return super().__new__(cls)

    def __init__(self, myarg):
        print('  Class.__init__(self=%s, myarg=%s)' % (self, myarg))
        self.myarg = myarg
        return super().__init__()

    def __str__(self):
        return "<instance of Class; myargs=%s>" % (getattr(self, 'myarg', 'MISSING'),)



print("==============")
print(Class(1))


