---
title: "install-visual-studio-code-on-aws-arm64"
template: "post"
category: "blog"
tags: 
  - "cloud"
  - "blog"
  - "arch/arm"
  - "util"
  - "tip"
toc: false
---

## install-visual-studio-code-on-aws-arm64

성공. 아래 링크에서 받아서 deb 패키지를 설치하면 된다.

https://github.com/headmelted/codebuilds/releases

그런데 이후에 apt로 패키지를 설치할 때 퍼블릭 키가 없다는 NO_PUBKEY 에러가 나서, 다음 링크를 참고하여 해쉬값을 이용해 키를 추가해서 해결했다.

https://askubuntu.com/questions/13065/how-do-i-fix-the-gpg-error-no-pubkey

