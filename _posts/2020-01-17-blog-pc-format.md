---
title: "blog-pc-format"
template: "post"
category: "blog"
tags: 
  - "blog"
toc: false
---

## pc 포맷

롤이 실행이 안 돼서 결국 포맷했다. 재설정 절차

1. scoop의 경로 설정 후 Paths에 scoop shims 폴더 추가 후 `scoop update` 실행하면 알아서 설치된 모든 앱 다시 인식함.
2. 롤 설치 및 directx 설치
3. 파이어폭스 동기화
4. 포토샵 설치
5. settings sync로 visual studio code 환경 설정 동기화
6. font 설치 : Fira Code, D2coding

Fira Code를 scoop으로도 설치 가능하다!:

```
scoop bucket add nerd-fonts
scoop install FiraCode
```

아마 mingw, gnu c++등도 재설치해야 할 듯.


7. 앱이 scoop에서 인식되지만 shim이 어떤 이유로 연결되지 않는 경우가 있는 것 같다. `scoop reset *` 명령으로 커맨드들을 다시 연결하려 했는데, 파이썬 부분에서 렉이 심해져 그냥 그때그때 하기로 했다.

현재 `node-gyp`를 쓰는 프로젝트를 고치지 못하고 있음.

robotjs, @tensorflow/tfjs-node

windows-build-tools 를 다시 설치하고 아래 명령으로
node-gyp도 업데이트하여 설치는 성공했다.
npm install -g node-gyp@latest

돌렸더니 깔지도 않은 webgl관련 typescript type 에러가 나서 찾아보다가, tensorflowjs쓰려면 `tsconfig.json`에서 `skipLibCheck:true`옵션을 주어야 한다고 해서 그렇게 했더니 실행이 되었다.
비록 실행 도중 도메인 에러가 났지만.

그 외에도 anaconda 등 에러가 나는 패키지들의 경우 `scoop update`, `scoop reset`으로 고침