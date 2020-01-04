---
date: "2019-05-17"
title: "powershell grep and cut"
category: "TIL"
slug: "posts/2019-05-17-til"
tags: ["dev/til", "powershell", "windows"]
banner: "/assets/bg/1.jpg"
---
## 파워셸 grep cut?

s3 버킷중에 안 쓰는 codestar 버킷이 남아있는게 있었는데, `aws s3 ls`로 하면 필요없는 정보까지 포함되어서 어떻게 제거하지 하다 `grep`, `cut`같은 기능을 파워셸에서도 할 수 있을까 하고 찾아봤는데 `grep`은 `Select-String`과 비슷한데 cut은 딱히 못찾고 유저가 만든 스크립트를 썼다.
