---
template: "post"
category: "blog"
tags: 
  - "cpp"
  - "tips"
  - "vscode"
  - "blog"
banner: "/assets/bg/1.jpg"
---

## visual studio code c++ 빌드 및 디버깅하기

c++ 확장을 깔고 안내대로 하면 c++ 디버깅이 되어야 한다.

그런데 node-gyp에서 여러 문제가 발생했다.

처음엔 빌드 툴을 찾을 수 없다고 나와서 visual studio community 2019의 MSBuild로 msbuild 경로를 설정하였다. 그러나 이번엔 특정 패키지가 msbuild 2015에 대해 설정되어 있다는 식으로 에러가 나서 결국 msbuild를 쓰지 않고 npm의 windows-build-tools를 쓰도록 했다.

(이 아래는 명확하진 않음)
`npm config list`, `yarn config list`로 설정 확인 후 `npm config set msvs_version='2015'`로 빌드 툴 버전 설정. 
`npm -g install --production windows-build-tools --msvs_version=2015`로 빌드 툴 설치.

