---
title: "Unicode"
date: 2023-07-14T16:26:24+08:00
tags: [windows]
---


_mbslen ,它可以用来操作多字节（既包括单字节也包括双字节）字符串
wchar_t <string.h>
ANSI C 后来添加了 wcschr, wcscpy, wcscmp, wcslen,
只需用前缀wcs来取代ANSI字符串函数的前缀str即可.

Microsoft 提供的c运行时库和 ANSI 是一致的，即使在 Win98 上也支持 Unicode.

<tchar.h> 比 <string.h> 更佳, <tchar.h> 受 _UNICODE 影响

_tcscpy = strcpy/wcscpy
TCHAR = char/wchar_t

TCHAR *szError = "Error";  // 有 #ifdef _UNICODE 则会报错
L ## x 是告诉编译器该字符串应该作为 Unicode 字符串来编译

_TEXT, TEXT

<winnt.h>:
    typedef char        CHAR;
    typedef CHAR        *PSTR;
    typedef CONST CHAR  *PCSTR;

    typedef wchar_t     WCHAR;
    typedef WCHAR       *PWSTR;
    typedef CONST WCHAR *PCWSTR;

    PTSTR, PCTSTR 是 ASCII/UNICODE 字符指针

_UNICODE宏用于C运行期头文件，而UNICODE宏则用于Windows头文件


在Windows2000下，Microsoft的CreateWindowExA源代码只不过是一个形实替换程序层或
翻译层，用于分配内存，以便将ANSI字符串转换成Unicode字符串。该代码然后调用Create
WindowExW，并传递转换后的字符串。当CreateWindowExW返回时，CreateWindowExA便释
放它的内存缓存，并将窗口句柄返回给你。

在Windows98下，Microsoft的CreateWindowExA源代码是执行操作的函数。Windows98
提供了接受Unicode参数的所有Windows函数的进入点，但是这些函数并不将Unicode字符串转
换成ANSI字符串，它们只返回运行失败的消息。
调用GetLastError将返回ERROR_CALL_NOT_IMPLEMENTED。

摒弃16位的仅支持ASCII的 WinExec 和 OpenFile，
拥抱 CreateProcess 和 CreateFile。

Windows 字符串函数
    <ShlWApi.h>, 是操作系统的一部分
        StrCat, StrChr, StrCmp, StrCpy
        StrCatA, StrCatW
    <WinBase.h>, 既可以ANSI，也可以UNICODE
        lstrcat
        lstrcmp
        lstrcmpi
        lstrcpy
        lstrlen
    C运行时没有为Unicode提供很好的支持，tolower，toupper只支持ANSI，
    建议使用 Windows 函数 CharLower, CharUpper, CharLowerBuffer, CharUpperBuff.
    考虑用户使用的语言 IsCharAlpha, IsCharAlphaNumric, IsCharLower, IsCharUpper.

    Microsoft公司已经给C运行期的printf函数家族增加了一些特殊的域类型.

规范
    TCHAR/PTSTR
    BYTE/PBYTE
    TEXT 字符串常量
    传递缓存大小: sizeof(szBuffer)/sizeof(TCHAR)
    malloc(nCharacters * sizeof(TCHAR))

资源是 Unicode

