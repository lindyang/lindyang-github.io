---
title: "Cpython"
date: 2023-07-28T16:05:42+08:00
tags: [python]
---


1. 必须引入 <Python.h>
> before any standard headers are included
2. PyArg_ParseTuple
> python tuple => c values
2. Errors and Exceptions
> - type: 异常类型
> - associated value
> - stack traceback
3. 三种设置异常的方式(🙅~~Py_INCREF~~)
> - PyErr_SetString(PyObject *type, const char *message)
> > PyExc_ZeroDivisionError
> - PyErr_SetFromErrno(PyObject *type)
> > errno
> - PyErr_SetObject(PyObject *type, PyObject *value)
4. 检查异常
> PyErr_Occurred
> 通常可以从返回值判断是否发生异常, 如果发生异常, 则 return an error value (usually a NULL pointer)
5. PyErr_Clear 可以显示忽略异常
6. `直接`调用 malloc 类的函数出现异常, 需要调用 PyErr_NoMemory.
7. 小心使用垃圾回收相关的 Py_XDECREF 和 Py_DECREF
8. 定义模块内的异常
```
static PyObject *SpamError;

PyMODINIT_FUNC PyInit_spam(void) {
    PyObject *m;

    m = PyModule_Create(&spammodule);
    if (m == NULL)
        return NULL;

    SpamError = PyErr_NewException("spam.error", NULL, NULL);
    Py_INCREF(SpamError);
    PyModule_AddObject(m, "error", SpamError);
    return m;
}
```
9. Py_RETURN_NONE
```
Py_INCREF(Py_None);
return Py_None;
```
10. METH_VARARGS | METH_KEYWORDS
> PyArg_ParseTupleAndKeywords

```
#include <Python.h>


static PyObject *SpamError;


static PyObject * spam_system(PyObject *self, PyObject *args) {
    const char *command;
    int sts;

    if (!PyArg_ParseTuple(args, "s", &command))
        return NULL;
    sts = system(command);
    if (sts < 0) {
        PyErr_SetString(SpamError, "System command failed");
        return NULL;
    }
    return PyLong_FromLong(sts);
}


static PyMethodDef SpamMethods[] = {
    {"system",  spam_system, METH_VARARGS, "Execute a shell command."},
    {NULL, NULL, 0, NULL}        /* Sentinel */
};


static struct PyModuleDef spammodule = {
    PyModuleDef_HEAD_INIT,
    "spam",   /* name of module */
    NULL, /* module documentation, may be NULL */
    -1,       /* size of per-interpreter state of the module,
                 or -1 if the module keeps state in global variables. */
    SpamMethods
};


PyMODINIT_FUNC PyInit_spam(void) {  // non-static
    PyObject *m;

    m = PyModule_Create(&spammodule);
    if (m == NULL)
        return NULL;

    SpamError = PyErr_NewException("spam.error", NULL, NULL);
    Py_INCREF(SpamError);
    PyModule_AddObject(m, "error", SpamError);
    return m;
}


int main(int argc, char *argv[]) {
    wchar_t *program = Py_DecodeLocale(argv[0], NULL);
    if (program == NULL) {
        fprintf(stderr, "Fatal error: cannot decode argv[0]\n");
        exit(1);
    }

    /* Add a built-in module, before Py_Initialize */
    PyImport_AppendInittab("spam", PyInit_spam);

    /* Pass argv[0] to the Python interpreter */
    Py_SetProgramName(program);

    /* Initialize the Python interpreter.  Required. */
    Py_Initialize();

    /* Optionally import the module; alternatively,
       import can be deferred until the embedded script
       imports it. */
    PyImport_ImportModule("spam");

    PyMem_RawFree(program);
    return 0;
}
```

- setup.py
```
from distutils.core import setup, Extension


module = Extension('spam', sources = ['spammoudle.c'])


setup (
    name = 'PackageName',
    version = '1.0',
    description = 'This is a demo package',
    ext_modules = [module],
)

```

