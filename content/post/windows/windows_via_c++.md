---
title: "Windows_Via_C++"
date: 2023-07-28T14:46:58+08:00
tags: [windows]
---

windows

ctrl-shift-L: 删除当前行
ctrl -: 后退
ctrl-alt-B: list breakpoint

返回类型: BOOL, 失败=0, 成功非0, don't use TRUE to test
HANDLE: INVALID_ HANDLE_VALUE=-1
LONG/DWORD: 无法计数=0/-1

DWORD GetLastError();  // WinError.h
- 有少数变态的函数成功也会改写错误代码. (CreateEvent是个好例子, 得知原因是 new or get)
- Windows98 不使用 GetLastError, 直接看函数返回值, 所以不知道原因
- @err,hr
- Tools -> Error Lookup
- VOID SetLastError(DWORD dwErrCode);
- 错误代码域: 29位=1 (客户, 0-base)

FormatMessage 是个强大的函数

HLOCAL x = LocalAlloc(), 当前进程的上下文中可用

Message Compiler（MC.exe）
Spy++:
	Windows 00020AFC "Error Show" #32770(Dialog)
	其中 Class Name 是 #32770 (Dialog)
	Handle: 00020AFC
	Caption: Error Show
	模式对话框独占

#define WM_COMMAND                      0x0111
#define chHANDLE_DLGMSG(message) HANDLE_##message()
chHANDLE_DLGMSG(WM_COMMAND) 会转换为 HANDLE_WM_COMMAND

Dlg_OnCommand(hwnd, LOWORD(wParam), (HWND)(lParam), HIWORD(wParam));
Dlg_OnCommand(hwnd, id, 			hwndCtl, 		codeNotify)

#include <stdio.h>

HANDLE g_hOutput = 0;
HINSTANCE g_hInstance;

void PrintMsg(const TCHAR* msg) {
    TCHAR szText[256] = { 0 };
    _sntprintf(szText, sizeof(szText)/sizeof(TCHAR), _TEXT("PrintMsg: %s\n"), msg);
    WriteConsole(g_hOutput, szText, _tcslen(szText), NULL, NULL);
}

main() {
	AllocConsole();
	g_hOutput = GetStdHandle(STD_OUTPUT_HANDLE);
	g_hInstance = hIns;
}


# code 到 msg 的映射
# https://wiki.winehq.org/List_Of_Windows_Messages
echo '#include <map>' > winmsg.cpp;
echo 'std::map<int, const TCHAR*> TWM {' >> winmsg.cpp;
sed -n \
	's/\s*\(\w\+\)\s\+\(\w\+\)\s\+\(\w\+\)\s*/    {0x\1, _T("\3")},  \/\/ \2/p' \
	winmsg.txt >> winmsg.cpp;
echo '};' >> winmsg.cpp;


SendMessage 有 Sync 属性
最大化/最小化窗口: WM_SYSCOMMAND
???????? WM_COMMAND + IDCANCEL 是怎么触发的

=====================

DBCS（Double-Byte Character Set，双字节字符集）
_mbslen 处理单双字符串
CharNext, CharPrev, IsDBCSLeadByte


一双看不见的手:
Windows 2000是使用Unicode从头进行开发的,
传ANSI -> 系统换成 Unicode -> 操作系统

少数的Unicode函数可以在 Win98 下运行,
例如: WideCharToMultiByte, MultiByteToWideChar

Windows CE 也支持 Unicode, 
为了精巧, 完全不支持 ANSI Windows 函数

[COM]
32bit, 需要字符串的所有COM接口方法都只能接受 Unicode 字符串

[标准库支持, Windows 98 也可以用]
str->wcs: wcscat, wcscmp, wcscpy, wcslen

[tchar.h]
_UNICODE 控制到底调标准库的哪个函数(C运行期头文件)
_tcscpy

[windows头文件, UNICODE 控制]
包含通用 PTSTR, PCTSTR

[**windows 2000 下] CreateWindowExA -> CreateWindowExW

[DLL]
提供 ANSI 提供内存分配和转换, 然后调用 Unicode 版本

Win98 调 Unicode 函数返回ERROR_CALL_NOT_IMPLEMENTED

[16位兼容]
WinExec, OpenFile
建议使用 CreateProcess, CreateFile 函数

[建议使用操作系统字符函数, ShlWApi.h]
StrCat, StrChr, StrCmp, StrCpy

[advice]
malloc(nChar * sizeof(TCHAR));

[Windows字符串函数, 通用, 前面加l]
lstrcat, lstrcmp, lstrcmpi, lstrcpy, lstrlen;
lstrcmpi, lstrcpy 最终调用了:
int CompareString;
GetThreadLocale;
零宽空格: 显示时不占据宽度，通常表示文本中的断字点或者指示断行位置，长度计算中会被考虑

[C运行库不足, Windows上, 重音都考虑]
CharLower; CharUpper;
既可以转换单个字符，也可以转换以 0结尾的整个字符串;
TCHAR lower = CharLower((PTSTR)szString[0]);  // 低16位字符, 高16位0;
CharLowerBuff; CharUpperBuff;

[得考虑语言]
IsCharAlpha; IsCharAlphaNumeric; IsCharLower; IsCharUpper;

[windows 下特殊的 printf 家族]
- normal: sprintf(szA, "%s", "ANSI Str");
- U2A:	  sprintf(szA, "%S", L"Unicode");
- normal: swprintf(szW, L"%s", L"Unicode");
- A2U:    swprintf(szW, L"%S", "ANSI Str");
总结, 转换的时候都用 %S (大写);


[资源中的字符串总是存储为Unicode, 没有定义UNICODE, 系统内部要转换]

IsTextUnicode; // 采用了统计, 结构可能不准;


================ 内核对象 ===========
[内核对象]
符号, 事件, 文件, 文件映像, I/O完成端口, 作业, 
信箱, 互斥, 管道, 进程, 新表, 线程, 等待计时器等

句柄与进程密切相关;
内核对象的存在时间可以比创建该对象的进程长;

[安全描述符, 通常编写服务器应用程序时使用]
用于描述谁创建了该对象，谁能够访问或使用该对象，谁无权访问该对象。

SECURITY_ATTRIBUTES psa;
[默认安全性]
对象的管理小组的任何成员和对象的创建者都拥有对该对象的全部访问权;
而其他所有人均无权访问该对象;

typedef struct _SECURITY_ATTRIBUTES {
  DWORD  nLength;
  LPVOID lpSecurityDescriptor;
  BOOL   bInheritHandle;
} SECURITY_ATTRIBUTES

[历史问题, p29]
KEY_QUERY_VALUE, KEY_ALL_ACCESS

[用户对象, 图形设备接口对象(GDI)]
菜单, 窗口, 鼠标光标, 刷子, 字体等

[区分内核对象和用户对象]
psa;

[进程的句柄表]
索引\指针\mask\flag

[Windows 2000]
返回的值用于标识放入进程的句柄表的该对象的字节数，而不是索引号本身

[句柄失败返回]
0, 少数为 -1(INVALID_HANDLE_VALUE) 例如(CreateFile)

[CloseHandle]
无效句柄, 如果进程正在排除错误，系统将通知调试程序，以便能排除它的错误。

[虽然内核对象句柄具有继承性，但是内核对象本身不具备继承性]

[WaitForInputIdle]
[GetEnvironmentVariable]

BOOL SetHandleInformation(HANDLE hObject, DWORD dwMask, DWORD dwFlags);
HANDLE_FLAG_INHERIT
HANDLE_FLAG_PROTECT_CLOSE: 该句柄不应该被关闭
打开继承
SetHandleInformation(hobj, HANDLE_FLAG_INHERIT, HANDLE_FLAG_INHERIT);
关闭继承
SetHandleInformation(hobj, HANDLE_FLAG_INHERIT, 0);

[句柄是否可被继承]
DWORD dwFlags;
GetHandleInformation(hObj, &dwFlags);
BOOL fIsInheritable = (0 != (dwFlags & HANDLE_FLAG_INHERIT));

[命名对象, MAX_PATH pszName, 对象都共享单个名空间]
CreateMutex, CreateEvent, CreateSemaphore, 
CreateWaitableTimer, CreateFileMapping, CreateJobObject;

DuplicateHandle;

[共用内核对象, 句柄值可能不同]
名字-> 类型-> 安全检查;
对已经存在的内核对象, 
CreateMutexW(lpMutexAttributes, bInitialOwner, lpName)
的前两个参数被忽略;
GetLastError() == ERROR_ALREADY_EXISTS;

[命名对象, OpenXXX 系列函数]

GUID

[终端服务器名字空间]
终端服务器拥有内核对象的多个名字空间(Local\\MyName);
客户程序会话都有它自己的名字空间(不同会话, 防干扰);
"Gobal\\MyName";
Global和Local, Session视为保留关键字;

- 继承
- 命名对象
- 复制对象(使用窗口消息或某种别的 IPC机制通知 TargetProcess)

[DuplicateHandle]
dwOptions: 
- DUPLICATE_SAME_ACCESS, 会忽略 dwDesiredAccess
- DUPLICATE_CLOSE_SOURCE: 传递

GetCurrentProcess();

UNREFERENCED_PARAMETER(XXX);
StringCchPrintf(szMutexName, _countof(szMutexName), ...);
StringCchPrintf;  // #include <strsafe.h>

TCHAR* CharToTchar(const char *str, BOOL *needFree) {
#ifdef _UNICODE
    // include null
    int cwStr = MultiByteToWideChar(CP_ACP, 0, str, -1, NULL, 0);
    PWSTR pWStr = (PWSTR)HeapAlloc(GetProcessHeap(), 0, cwStr * sizeof(WCHAR));
    if (pWStr == NULL) {
        *needFree = FALSE;
        return NULL;
    }
    MultiByteToWideChar(CP_ACP, 0, str, -1, pWStr, cwStr);
    *needFree = TRUE;
    return pWStr;
#else
    needFree = FALSE;
    return str;
#endif
}

[WinBuiltinAdministratorsSid]
通常具有最高的权限级别，拥有对系统的完全控制权;
包括系统默认的 Administrator 账户以及任何添加到该组的其他用户或用户组;
ConvertStringSecurityDescriptorToSecurityDescriptor;
????? DACL, SACL; TEXT("D:(A;;GA;;;BA)")
ClosePrivateNamespace(g_hNamespace, PRIVATE_NAMESPACE_FLAG_DESTROY);

=========== 进程 ===========
Win2000 可以利用多核, 但是 Win98 不可以;
控制台用户界面(CUI): /SUBSYSTEM:CONSOLE;
GUI: SUBSYSTEM:WINDOWS;

WINAPI, __cdecl;

[w][WinMain|main]CRTStartup
启动函数 -> C/C++运行库进行初始化(malloc/free, 全局对象);
Link下的 System 设置为 Not Set;

[启动函数干了哪些事情: p47]
- 检索命令行指针
- 检索环境变量指针
- 运行期的全局变量进行初始化
- 内存单元分配函 & 内存栈进行初始化
- 全局和静态 C++ 类对象调用构造函数
	int APIENTRY _tWinMain(
	HINSTANCE hInstance, // exe or dll 独一无二的实例句柄(基本地址空间的值)
	 HINSTANCE hPrevInstance,  // 16位
	 LPTSTR    lpCmdLine,  // 不包含执行文件的名字, 可写但是不建议
	 int       nCmdShow);

GetStartupInfo(&StartupInfo);
int ret = WinMain(GetModuleHandle(NULL), NULL, pszCmdLine,
	(StartupInfo.dwFlags & STARTF_USESHOWWINDOW) ? StartupInfo.wShowWindow : SW_SHOWDEFAULT);
exit(ret);

[exit]
- _onexit 注册的函数被调用
- 全局的和静态的C++类对象调用析构函数
- 操作系统的ExitProcess(ret);

[全局变量]
_osver;
_winmajor, _winminor, 
_winver: (_winmajor<<8)+_winminor;
_argc;
_argv, _wargv;
_environ, _wenviron; [OK]
_pgmptr, _wpgmptr; [OK]

[HMODULE, 16位的时候, HMODULE和HINSTANCE用于标识不同的东西]

链接程序 /BASE:address;

[只查看调用进程的地址空间,且NULL时, 返回`可执行文件`的基地址]
HMODULE GetModuleHandleA(
  [in, optional] LPCSTR lpModuleName
);

PTSTR GetCommandLine();
GetModuleFileName(NULL, szFileName, _countof(szFileName));

// CString: afx.h;  // strPath.GetBufferSetLength(MAX_PATH + 1)

PWSTR *ppArgv = CommandLineToArgvW(GetCommandLineW(), &nNumArgs);
if (*ppArgv[1] == L'x') {
}
HeapFree(GetProcessHeap(), 0, ppArgv);

[环境变量]
按字母排序, 变量名可以包含空格;
Win98 修改 AutoExec.bat, SET Name=Value
win2000:
- 系统: HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Session Manager\Environment
- 用户: HKEY_CURRENT_USER\Environment
修改后, 用户必须退出系统，然后再次登录;
WM_SETTINGCHANGE;
SendMessage(HWND_BROADCAST, WM_SETTINGCHANGE, 0, (LPARAM)TEXT("Environment"));
父进程能控制子进程继承什么环境变量.

DWORD GetEnvironmentVariable(PCTSTR pszName, PTSTR pszValue, DWORD cchValue);
DWORD ExpandEnvironmentStrings(PCSTR pszSrc, PSTR pszDst, DWORD nSize);
BOOL SetEnvironmentVariable(, NULL) 是删除;

[错误模式]
UINT setErrorMode(UINT fuErrorMode);
SEM_NOGPFAULTERRORBOX;
CREATE_DEFAULT_ERROR_MODE;

[进程目录]
GetCurrentDirectory;
SetCurentDirectory;

[只读驱动器名的环境变量, 总是在开始处]
系统将对进程的当前驱动器和目录保持跟踪，但是它不跟踪每个驱动器的当前目录;
_chdir 修改环境变量, 并调用SetCurentDirectory, 不同驱动器的当前目录就可以保留;
=D:=D:\Program Files;
读取 D:ReadMe.Txt 实际打开 D:\Program Files\ReadMe.Txt;

[]
子进程的环境块不会自动继承父进程的当前目录, 默认为每个驱动器的根目录;
要想被继承, 父得创建这些驱动器名的环境变量;

GetFullPathName() 获取指定文件的长文件名;
GetModuleFileName() 获取当前模块的全路径
GetCurrentDirectory() 获取当前模块所在目录

VerifyVersionInfo;

[进程内核对象, 线程内核对象]
CreateProcess 返回TURE, 然后进程初始化,没有找到DLL也会失败;

BOOL CreateProcess(
  [in, optional]      LPCSTR                lpApplicationName,
  [in, out, optional] LPSTR                 lpCommandLine,  // 不是 const
  [in, optional]      LPSECURITY_ATTRIBUTES lpProcessAttributes,  // 控制是否被继承
  [in, optional]      LPSECURITY_ATTRIBUTES lpThreadAttributes,  // 控制是否被继承
  [in]                BOOL                  bInheritHandles,  // 继不继承父类的句柄
  [in]                DWORD                 dwCreationFlags,
  [in, optional]      LPVOID                lpEnvironment,
  [in, optional]      LPCSTR                lpCurrentDirectory,
  [in]                LPSTARTUPINFOA        lpStartupInfo,
  [out]               LPPROCESS_INFORMATION lpProcessInformation
);

[Visual C++ 编译器开关]
/Gf
/GF 共享字符串
/ZI

Win2000 的 CreateProcess lpCommandLine 即使是 const 也没关系.

[搜索目录]
本 exe 所在目录;
curdir;
sys dir;
windows 目录
PATH;

[fdwCreate]
CREATE_SUSPENDED;
DETACHED_PROCESS;
CREATE_NO_WINDOW;
CREATE_NEW_PROCESS_GROUP;
CREATE_DEFAULT_ERROR_MODE;
CREATE_UNICODE_ENVIRONMENT;
也可以指定优先级;

POVID GetEnvironmentStrings();
BOOL FreeEnvironmentStrings(PTSTR pszEnvironmentBlock);

VOID GetStartupInfo(LPSTARTUPINFO);

ToolHelp 函数 PROCESSENTRY32 有父进程ID;

自己 ExitProcess, 或让别人 TerminateProcess, 是异步的, 可以用 WaitForSingleObject等待;
ExitThread 只终止主线程;

进程内核对象的状态变成收到通知的状态;
BOOL GetExitCodeProcess;  // STILL_ACTIVE = 0x103;

进程间通信: 动态数据交换(DDE), OLE, 管道, 邮箱;



=== 作业 ===

CreateProcess(..., CREATE_SUSPENDED, ...);   // CREATE_BREAKAWAY_FROM_JOB

AssignProcessToJobObject(hjob, pi.hProcess); 

ResumeThread(pi.hThread);

CloseHandle(pi.hThread);



CloseHandle(hjob);  // 会让 name 与 job 失去联系



SetInformationJobObject:

- 基本
  - JOB_OBJECT_BREAKAWAY_OK 新生成的进程可以在作业外部运行
  - JOB_OBJECT_SILENT_BREAKAWAY_OK, 重点在 SILENT, 没有其他 flags 依赖
  - JOB_OBJECT_LIMIT_PRESERVE_JOB_TIME
- 扩展
- UI
- 安全

SchedulingClass 是在 PriorityClass 相等下才进行比较, 越大越容易调度

BOOL UserHandleGrantAccess(hUserObj, hjob, fGrant) 打破 UI 限制;  // 不能自己给自己赋能.

安全限制无法取消;

QueryInformationJobObject(NULL, ...)  // 本进程所在的作业

CREATE_BREAKAWAY_FROM_JOB标志调用CreateProcess函数，但该作业并没有打
开**JOB_OBJECT_LIMIT_BREAKAWAY_OK** 标志，那么CreateProcess函数运行失败;

BOOL TerminateJobObject(HANDLE hJob, UNIT uEixtCode); // TerminateProcess for every process;

更加通用的 BOOL GetProcessIoCounters(HANDLE hProcess, PIO_COUNTERS pIoCounters);

_alloca()  // malloca.h 

Performance Data Helper (PDH.dll)

Microsoft Management Console(MMC) / Performance Monitor Snap-In;

- mmc -> 文件 perfmon.msc -> 性能监视器 -> 添加计数器

只能为已经赋予名字的作业获取性能计数器信息.

SetInformationJobObject函数，使作业对象恢复未通知状态，并为作业赋予更多的 CPU时间;

 I/O 完成端口内核对象，并将作业对象或多个作业对象与完成端口关联起来

```
JOBOBJECT_ASSOCIATE_COMPLETION_PORT joacp;
joacp.CompletionKey = 1; // Unique ID
joacp.CompletionPort = hIOCP;  //  CreateIoCompletionPort
SetInfomationJobObject(hJob, JobObjectAssociateCompletionPortInformation, &joacp, sizeof(joacp));
```

QueryInformationJobObject 检索`完成关键字`和`完成端口`机会很少

```
BOOL GetQueuedCompletionStatus(
  [in]  HANDLE       CompletionPort,
        LPDWORD      lpNumberOfBytesTransferred,  // 发生的事件
  [out] PULONG_PTR   lpCompletionKey,  // ID
  [out] LPOVERLAPPED *lpOverlapped,  // PID
  [in]  DWORD        dwMilliseconds
);
```

通知JOB时间到期

```
JOBOBJECT_END_OF_JOB_TIME_INFORMATION joeojti;
joeojti.EndOfJobTimeAction = JOB_OBJECT_POST_AT_END_OF_JOB;
SetInformationJobObject(hJob, JobObjectEndOfJobTimeInformation, &joeojti, sizeof(joeojti));
```

DebugBreak();

CopyMemory(...);

_beginthreadex();

PtrToUlong();

__int64: %I64u, %I64d;

chMB;



===== 线程的基础知识 ====

句柄表依赖于每个进程而不是每个线程存在;

线程只有一个内核对象和一个堆栈;

有时在不同的线程上创建不同的窗口是有用的，不过这种情况确实非常少见:

- Windows Explorer为每个文件夹窗口创建了一个独立的线程

CreateThread 是 Windows 的函数, 写 C/C++ 还是用 _beginthreadex(`_endthreadex`);

[cbStack]

- `/STACK:[reserve][,commit]`  // link, 虚拟/物理

新线程和调用 C r e a t e T h r e a d的线程可以同时执行:

```
int x;
DWORD dwThreadID;
HANDLE hThread = CreateThread(NULL, 0, SecondThread, (PVOID)&x, 0, &dwThread);
CloseHandle(hThread);
// BUG
return 0;
```

[fdwCreat]

- CREATE_SUSPENDED

[pdwThreadID]

- Win95, Win98 不能为 NULL

ExitThread;

- C++资源（如 C++类对象）将不被撤消

TeminateThread;

- 异步, 使用 WaitForSingleObject
- 拥有线程的进程终止运行之前，系统不撤消该线程的堆栈(贴心的微软)
- DLL 不接收通知

系统将线程的退出代码（在线程的内核对象中维护）设置为线程函数的返回值

[线程终止运行时发生的操作]

- 撤销窗口, 卸载挂钩
- STILL_ACTIVE -> exitCode
- 线程内核对象的状态变为已通知

GetExitcodeThread 获取退出码;

[线程内核对象]

- IP: VOID BaseThreadStart();

  - 在线程函数中建立一个结构化异常处理（SEH）帧，这样，在线程执行时产生的任何异常情
    况都会得到系统的某种默认处理

- ```
  VOID BaseThreadStart(PTHREAD_START_ROUTINE pfnStarAddr, PVOID pvParam) {
    __try {
      ExitThread((pfnStartAddr)(pvParam));
    }
    __except(UnhandledExceptionFilter(GetExceptionInformation())) {
      ExitProcess(GetExceptionCode());
    }
  }
  ```

  

结构化异常处理（SEH）帧

[进程的`主线程`]

```
VOID BaseProcessStart(PROCESS_START_ROUTINE pfnStartAddr) {
  __try {
    ExitThread((pfnStartAddr)());
  }
  __except(UnhandledExceptionFilter(GetExceptionInformation())) {
    ExitProcess(GetExceptionCode());
  }
}
```

[c/c++运行期库]

- LibC.lib: single + static (default)
- LibCD.lib: + debug
- LibCMt.lib: + multi
- LibCMtD.lib: + debug
- MSVCRt.lib: 动态链接MSVCRt.dll库的发行版的输入库
- MSVCRtD.lib: MSVCRtD.dll, 同时支持单线程应用程序和多线程应用程序

[没有考虑多线程]

```
errno、_doserrno、strtok、_wcstok、strerror、
_strerror、tmpnam、tmpfile、asctime、_wasctime、gmtime、_ecvt和_fcvt
```

若要创建一个新线程，绝对不要调用操作系统的 CreateThread函
数，必须调用C/C++运行期库函数_beginthreadex;

[p132]

_beginthreadex函数只存在于 C/C++运行期库的多线程版本中;

[线程的运行]

BaseThreadStart -> _threadstartex -> 初始 _piddata 并关联

-> SEH;



TlsSetValue;

SEH 是指结构化异常处理（Structured Exception Handling）

避免直接调用 ExitThread, 是因为 tiddata 得不到释放(直到整个进程终止).

建议使用 _endthreadex;

[编译命令]

/MT, /MD 会让编译器定义 _MT 标识符

// 伪句柄

HANDLE GetCurrentProcess();

HANDLE GetCurrentThread();

DWORD GetCurrentProcessId();

DWORD GetCurrentThread();

伪句柄变成实句柄(线程句柄, 进程句柄都可以)

DuplicateHandle(

​	GetCurrentProcess(),

​	GetCurrentThread(),

​    GetCurrentProcess(),

​    &hThreadParent,

   0, FALSE, DUPLICATE_SAME_ACCESS

)

CloseHandle(hThreadParent);

