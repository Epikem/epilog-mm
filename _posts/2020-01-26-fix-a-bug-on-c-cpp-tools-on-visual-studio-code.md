---
template: "post"
category: "blog"
tags: 
  - "cpp"
  - "tip"
  - "vscode"
  - "blog"
banner: "/assets/bg/1.jpg"
---

## visual studio code c++툴 수정 기여하기

알아낸 내용에 대해서 생략하고 수정한 것만 리퀘스트한 결과 리뷰어로부터 내가 어떤 것들을 시도했는지에 대한 요청을 받았다.

> 📂 `launch.json`
```json
...
    {
      "name": "(gdb) Launch",
      "type": "cppdbg",
      "request": "launch",
      "preLaunchTask": "buildcpp",
      "program": "${fileDirname}/${fileBasenameNoExtension}.exe",
      "args": [
        "<${fileDirname}/input.txt"
      ],
      "stopAtEntry": false,
      "cwd": "${workspaceFolder}",
      "environment": [
      ],
      "logging":{
        "engineLogging": true,
        "trace":true
      },
      "externalConsole": false,
      "MIMode": "gdb",
      "miDebuggerPath": "D:/Program Files/mingw-w64/x86_64-8.1.0-posix-seh-rt_v6-rev0/mingw64/bin/gdb.exe",
      "setupCommands": [
        {
          "description": "Enable pretty-printing for gdb",
          "text": "-enable-pretty-printing",
          "ignoreFailures": true,
        }
      ]
    }
...
```

> 📂 `c_cpp_properties.json`
```json
{
    "configurations": [
        {
            "name": "gcc",
            "includePath": [
                "${workspaceFolder}/**",
                "D:/Program Files/mingw-w64/x86_64-8.1.0-posix-seh-rt_v6-rev0/mingw64/include",
                "D:/Program Files/mingw-w64/x86_64-8.1.0-posix-seh-rt_v6-rev0/mingw64/lib/gcc/x86_64-w64-mingw32/8.1.0/include/**"
            ],
            "defines": [
                "_DEBUG",
                "UNICODE",
                "_UNICODE"
            ],
            "compilerPath": "\"D:/Program Files/mingw-w64/x86_64-8.1.0-posix-seh-rt_v6-rev0/mingw64/bin/g++.exe\"",
            "cStandard": "c11",
            "cppStandard": "c++17",
            "intelliSenseMode": "gcc-x64",
            "browse": {
                "path": [
                    "${workspaceRoot}",
                    "D:/Program Files/mingw-w64/x86_64-8.1.0-posix-seh-rt_v6-rev0/mingw64/include",
                    "D:/Program Files/mingw-w64/x86_64-8.1.0-posix-seh-rt_v6-rev0/mingw64/lib/gcc/x86_64-w64-mingw32/8.1.0/include"
                ],
                "limitSymbolsToIncludedHeaders": false,
                "databaseFilename": ""
            },
            "compilerArgs": [
                "-g"
            ]
        }
    ],
    "version": 4
}
```

> 📂 `tasks.json`
```json
{
  "version": "2.0.0",
  "tasks": [
    {
      "label": "msvc build",
      "type": "shell",
      "command": "cl.exe",
      "args": [
        "/EHsc",
        "/Zi",
        "/Fe:",
        "./sport/cpp/main.exe",
        "./sport/cpp/main.cpp"
      ],
      "group": "build",
      "presentation": {
        "reveal": "always"
      },
      "problemMatcher": "$msCompile"
    },
    {
      "label": "buildcpp",
      "command": "g++",
      "args": [
        "${file}",
        "-g",
        "-o",
        "${fileDirname}/${fileBasenameNoExtension}"
      ],
      "group": {
        "kind": "build",
        "isDefault": true
      },
      "problemMatcher": {
        "fileLocation": [
          "relative",
          "${workspaceRoot}"
        ],
        "pattern": {
          "regexp": "^(.*):(\\d+):(\\d+):\\s+(warning error):\\s+(.*)$",
          "file": 1,
          "line": 2,
          "column": 3,
          "severity": 4,
          "message": 5
        }
      }
    }
  ]
}
```



My Environment:
- Visual Studio Community 2019 for MIEngine build.  
- Visual Studio Code 1.41.1
- Visual Studio Code C/C++ extension 0.26.3
- Powershell core 6.2.3
- MinGW-w64 8.1.0 posix-seh

At first I didn't know why debugging with powershell not works but cmd or with external console work. So I checked L

>@Epikem Thank you for your submission. Can you tell me what you tested so know what else needs to be tested?

I tested this fix by replacing content in vscode cpp-tools' debugAdapters folder (`C:\Users\Administrator\.vscode\extensions\ms-vscode.cpptools-0.26.3\debugAdapters`) and launching vscode debug after that.

I checked [the error(timeout) happened here](https://github.com/microsoft/vscode-cpptools/issues/2889) does not appear with increased delay.
When I launch debug, it opens new powershell terminal which usually fails by connection failure. But the connection failure does not occur when I don't close newly launched terminal and reuse it by stopping running command (`WindowsDebugLauncher.exe`) using ctrl+c, or if I remove all of the content in my powershell profile.
And I checked same error happens even after increase delay(20s) when I add additional 20 second delay in powershell profile.


This is vscode debug console log when timeout happened:

```log
1: (98) LaunchOptions<LocalLaunchOptions xmlns='http://schemas.microsoft.com/vstudio/MDDDebuggerOptions/2014'
1: (106) LaunchOptions  ExePath='c:\Users\...\dev\logs\sport\cpp\main.exe'
1: (107) LaunchOptions  WorkingDirectory='C:\Users\...\dev\logs'
1: (107) LaunchOptions  ExeArguments='&lt;c:\Users\...\dev\logs\sport\cpp/input.txt 2&gt;CON 1&gt;CON'
1: (107) LaunchOptions  MIMode='gdb'
1: (107) LaunchOptions  MIDebuggerPath='D:/Program Files/mingw-w64/x86_64-8.1.0-posix-seh-rt_v6-rev0/mingw64/bin/gdb.exe'
1: (107) LaunchOptions  WaitDynamicLibLoad='false'
1: (107) LaunchOptions>
1: (107) LaunchOptions    <SetupCommands>
1: (107) LaunchOptions        <Command IgnoreFailures='true' Description='Enable pretty-printing for gdb'>-enable-pretty-printing</Command>
1: (107) LaunchOptions    </SetupCommands>
1: (107) LaunchOptions</LocalLaunchOptions>
1: (176) Wait for connection completion.


1: (5208) Send Event AD7MessageEvent
```

And This is when timeout does not happen (after increase delay):

```log
1: (116) LaunchOptions<LocalLaunchOptions xmlns='http://schemas.microsoft.com/vstudio/MDDDebuggerOptions/2014'
1: (140) LaunchOptions  ExePath='c:\Users\...\sport\cpp\main.exe'
1: (140) LaunchOptions  WorkingDirectory='C:\Users\...'
1: (141) LaunchOptions  ExeArguments='&lt;c:\Users\...\sport\cpp/input.txt 2&gt;CON 1&gt;CON'
1: (141) LaunchOptions  MIMode='gdb'
1: (141) LaunchOptions  MIDebuggerPath='D:/Program Files/mingw-w64/x86_64-8.1.0-posix-seh-rt_v6-rev0/mingw64/bin/gdb.exe'
1: (141) LaunchOptions  WaitDynamicLibLoad='false'
1: (141) LaunchOptions>
1: (141) LaunchOptions    <SetupCommands>
1: (141) LaunchOptions        <Command IgnoreFailures='true' Description='Enable pretty-printing for gdb'>-enable-pretty-printing</Command>
1: (141) LaunchOptions    </SetupCommands>
1: (141) LaunchOptions</LocalLaunchOptions>
1: (202) Wait for connection completion.
1: (6977) ->=thread-group-added,id="i1"
1: (6978) ->~"GNU gdb (GDB) 8.1\n"
1: (6979) ->~"Copyright (C) 2018 Free Software Foundation, Inc.\n"
1: (6980) ->~"License GPLv3+: GNU GPL version 3 or later <http://gnu.org/licenses/gpl.html>\nThis is free software: you are free to change and redistribute it.\nThere is NO WARRANTY, to the extent permitted by law.  Type \"show copying\"\nand \"show warranty\" for details.\n"
1: (6981) ->~"This GDB was configured as \"x86_64-w64-mingw32\".\nType \"show configuration\" for configuration details."
1: (6981) ->~"\nFor bug reporting instructions, please see:\n"
1: (6982) ->~"<http://www.gnu.org/software/gdb/bugs/>.\n"
1: (6982) ->~"Find the GDB manual and other documentation resources online at:\n<http://www.gnu.org/software/gdb/documentation/>.\n"
1: (6982) ->~"For help, type \"help\".\n"
1: (6982) ->~"Type \"apropos word\" to search for commands related to \"word\".\n"
1: (6983) ->(gdb)
1: (6985) <-1001-gdb-set target-async on
1: (7000) ->1001^done
1: (7000) ->(gdb)

...(many messages)

1: (8562) ->(gdb)
<--   C (scopes-12): {"command":"scopes","arguments":{"frameId":1000},"type":"request","seq":12}
1: (9008) <-1031-stack-select-frame 0
1: (9013) ->1031^done
1: (9013) 1031: elapsed time 5
1: (9014) <-1032-stack-list-variables 0
1: (9014) ->(gdb)
1: (9014) ->&"\n"
1: (9014) ->^done
1: (9015) ->(gdb)
1: (9028) ->1032^done,variables=[]
1: (9028) 1032: elapsed time 14
1: (9028) ->(gdb)
1: (9029) ->&"\n"
1: (9029) ->^done
1: (9030) ->(gdb)
```

