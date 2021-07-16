---
title: "google-audioset-tutorial"
category: "project"
tags: 
  - "blog"
  - "project"
  - "capstone"
toc: false
---

## google-audioset-tutorial

구글 AudioSet을 써서 sound source classification을 해보려고 한다.

그런데 AudioSet 사이트에서 데이터를 다운받아 보면 csv데이터와 tfrecord들로 되어 있는데, 그게 대체 뭔가 했더니 구글 자체적으로 소리 데이터에서 특징을 뽑아놓은 것을 프레임별로 저장해놓은 게 tfrecords고, 그 특징을 뽑아주는 모델이 vggish라고 하는 것 같다.

목표/문제는 다음과 같디:
- 가능하다면 빠른 처리 가능하게
 현실적으로 처리절차가 좀 많아서 tx2 board 내에서 다 처리할 수 있을 지 미지수다. 만약 tx2 보드에서 처리가 느리다면 통신으로 다른 서버에 보내서 처리하도록 해야 한다.
 -> 소리 데이터 스트리밍 / 빠른 전송 필요


- 규격에 맞게 새 데이터 만들어 학습/테스트
  - tfrecord 읽는 방법
  - tfrecord 읽어서 분류하는 방법 -> 멀티 클래스? 단일 클래스?
  - vggish를 사용하여 tfrecord 만들기. 같은 메타데이터로 직접 vggish로 feature 뽑았을 때, 기존에 존재하던 AudioSet의 feature 데이터와 동일하게/비슷하게 나와야 한다.
  - 


