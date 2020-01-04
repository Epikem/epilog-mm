---
title: "visual studio code에서 mingw gdb로 c++ 디버깅"
date: "2020-01-04"
template: "post"
category: "blog"
slug: "posts/visual-studio-code에서-mingw-gdb로-c++-디버깅"
tags: 
  - "cpp"
  - "algorithm"
  - "vscode"
  - "blog"
banner: "/assets/bg/1.jpg"
---

## visual studio code에서 mingw gdb로 c++ 디버깅

vscode cpp with mingw configuration guide

예전에 msvc나 gcc로 시도해봤지만 느리거나 문제가 있어서 실패하고, 그냥 파워셸 스크립트에서 호출하는 식으로 쓰고 있었는데, 이런 방식으로는 런타임 에러가 났을 때 출력이 나오질 않기도 하고 디버깅도 불편해서 결국 다시 vscode로 세팅을 시도했고 이번엔 제대로 성공했다.

기본적으로 대부분의 스텝은 [공식 문서](https://code.visualstudio.com/docs/cpp/config-mingw)를 참조하면 된다.

이번에 다르게 한 부분은 다음과 같다:
- 먼저 msvc로 시도했으나, integrated terminal인 powershell에 cl.exe가 없어서 path에 추가했더니 다른 문제가 또 발생해서 gcc로 시도해보다가 성공해서 gcc로 하기로 했다.
- mingw를 그냥 win32로 설치했는데, 이번엔 온라인 설치 관리자로 다시 설치했고, posix, seh옵션을 선택했다. 이게 정확히 뭘 하는지는 잘 모르겠다. posix는 병렬 처리 스레딩의 구현관련, seh는 예외 처리와 관련이 있는 것으로 보인다.
- `bits/stdc++.h`부분에서 에러가 나서 include를 했다. 그런데도 에러가 계속되서 `c_cpp_properties.json`에서 `"limitSymbolsToIncludedHeaders": true,`로 설정했다. 그러나 여전히 고쳐지진 않았다. 다시 `.vscode/settings.json`에서 `C_Cpp.intelliSenseEngineFallback: Enabled`로 설정하니 에러 대신 경고로 바뀌어서 실행은 가능해졌다. 어떤 블로그에서는 msvc쪽 include폴더에 mingw의 bits폴더를 복붙해 넣으면 된다고도 하는데, 왜 그렇게 해야 하는지 이해가 안 되어서 해보지는 않았다. 그러다 어떤 글에서 `bits`는 `mingw`내부에서 사용하기 위한 헤더파일이고 되도록 쓰지 않는 것이 좋다고 해서 그냥 일단 bits를 쓰지 않기로 했다.
- 알고리즘 문제풀이에서 사용할 목적이었기 때문에 cpp 파일 내에서 실행 매개변수로 받은 파일 이름을 이용해서 파일입출력을 활용하려고 했는데, `sstream`을 include했음에도 `ifstream`에서 에러가 나서 다른 방법을 찾던 도중 `tasks.json`에서 `<input.txt`이렇게 argument를 주면 된다는 글을 보고 적용하니 성공했다. https://github.com/Microsoft/vscode-cpptools/issues/254


### 설정 결과

기본적으로 세 파일을 설정해주어야 한다:
- `c_cpp_properties.json` : cpp intellisense 설정
- `tasks.json` : 빌드 task 설정
- `launch.json` : 디버깅 실행 설정

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
      "compilerPath": "D:/Program Files/mingw-w64/x86_64-8.1.0-posix-seh-rt_v6-rev0/mingw64/bin/g++.exe",
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

> 📂 `launch.json`
```json
{
  "version": "0.2.0",
  "configurations": [
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
        // {
        //   "name": "_DEBUG",
        //   "value":"true",
        // }
      ],
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
  ]
}
```