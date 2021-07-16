---
template: "post"
category: "project"
tags: 
  - "blog"
  - "project/vocabo"
  - "react"
banner: "/assets/bg/3.jpg"
---

## vocabo 프로젝트 되살리기

1. `npm start`로 실행해봄. -> 엄청난 security warning과 함께 `react-devtools-extension`이 없다고 나옴. `package.json`을 찾아보니 이미 설치되어 있음.
2. `npm update`로 패키지 업데이트. 여전이 보안 경고가 있음.
3. `npm audit fix`로 보안 문제 해결 시도. 85개 문제 중 9개만 해결되고 나머지는 수동으로 해결해야 한다고 뜸. 그러나 `npm audit`을 실행한 결과 심각한 문제는 대부분 해결되어 그냥 냅두기로 함.
4. 다시 `npm start` 실행. 
5. 여전히 그 에러가 뜬다. 찾아보니 포맷 후 현재 사용중인 브라우저인 파이어폭스에 대한 redux-devtools를 설치하지 않음. 파이어폭스 redux-devtools 브라우저 확장을 설치하니 문제 해결됨.
6. 실행은 되었으나 서버에 쿼리가 안 됨. 서버 문제인지 클라이언트 url 설정 문제인지는 확실하지 않음. 
7. 생각해보니, aws serverless를 쓰는데도 구글 콘솔에서 따로 서버를 또 썼던 이유가, elasticsearch 노드를 실행해야 하기 때문이었다. 그런데 지금은 가능하면 돈을 들이지 않고 싶다.
방법:
  1. aws크레딧이나 한양계정 free-tier를 쓴다.
  2. 단어 베이스 전체를 클라이언트가 받게 해서 autocomplete를 쓴다. 단어장 용량이 11.2Mb정도로 불가능하진 않으나 좋은 방법은 아니다.
  3. lambda만으로 하고, aws내에 단어장을 저장하여 lambda가 쿼리하도록 하여 elasticsearch를 쓰지 않도록 만든다.

3번에 가장 나아 보이지만 현재 상태에서 elasticsearch를 쓰지 않게 하려면 어떻게 해야 하나? jaso-analyzer같은 기능 및 빠른 검색이 지원되는 라이브러리가 있을까?

fuse.js로 충분히 빠르게 검색이 될까?? 해본 결과 꽤 느린 것 같다. flexSearch라는 것이 있는데, 충분히 빠를지는 모르겠다. 만약 빠르다 해도 lambda를 돌릴 때 단어장 전체를 메모리에 두고 돌려도 괜찮을지 약간 신경쓰인다.

8. 현재 flexsearch라는 자바스크립트 FTS 라이브러리를 활용하려고 시도중. 한글 tokenizer 지원이 따로 안 되어서 직접 설정이 필요하다.

9. tokenizer 관련 시도를 해보다가, `Hangul.js`라는 간단하면서 좋은 라이브러리를 찾아서 그냥 이걸 쓰기로 함.


