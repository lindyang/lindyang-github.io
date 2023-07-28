---
title: "Vscode"
date: 2023-07-28T14:52:27+08:00
tags: [misc]
---


### 下载
- C/C++
- C/C++ Compile Run
- Code Runner

### 创建工程

### 配置
- ctrl+,
- Show modified settings
- RunInTerminal 选择
> Whether to run code in Integrated Terminal
```
文件->首选项->设置->扩展->Run Code configuration, 勾选Run In Terminal
```

### launch.json
- Debug
- Create a launch.json file
- C++（GDB/LLDB)
```
{
    // Use IntelliSense to learn about possible attributes.
    // Hover to view descriptions of existing attributes.
    // For more information, visit: https://go.microsoft.com/fwlink/?linkid=830387
    "version": "0.2.0",
    "configurations": [
        {
            "name": "(gdb) 启动",
            "type": "cppdbg",
            "request": "launch",
            "program": "${workspaceFolder}/${fileBasenameNoExtension}",
            "args": [],
            "stopAtEntry": false,
            "cwd": "${workspaceFolder}",
            "environment": [],
            "externalConsole": false,
            "miDebuggerPath": "/home/oem/bin/gdb",
            "MIMode": "gdb",
            "preLaunchTask": "chmod",
            "setupCommands": [
                {
                    "description": "为 gdb 启用整齐打印",
                    "text": "-enable-pretty-printing",
                    "ignoreFailures": true
                }
            ]
        }
    ]
}
```

### 添加 tasks.json
- ctrl+shift+p
- Tasks: Run task
- Create tasks.json file from template
- Others

```
{
    // See https://go.microsoft.com/fwlink/?LinkId=733558
    // for the documentation about the tasks.json format
    "version": "2.0.0",
    "tasks": [
        {
            "label": "chmod",
            "type": "shell",
            "command": "sudo",
            "args": ["chmod", "u+s", "${fileBasenameNoExtension}"],
            "dependsOn": ["chown"]
        },
        {
            "label": "chown",
            "type": "shell",
            "command": "sudo",
            "args": ["chown", "root:root", "${fileBasenameNoExtension}"],
            "dependsOn": ["build"]
        },
        {
            "label": "build",
            "type": "shell",
            "command": "gcc",
            "args": ["-g", "-Wall", "-o", "${fileBasenameNoExtension}", "${file}", "-lnetfilter_queue"]
        },
        {
            "label": "all",
            "dependsOn": [
                "chmod",
                "chown",
                "build"
            ]
        }
    ]
}
```

### 参考
- sudo gdb 调试的问题
参考
https://stackoverflow.com/questions/40033311/how-to-debug-programs-with-sudo-in-vscode

- 设置
https://blog.csdn.net/weixin_43374723/article/details/84064644

