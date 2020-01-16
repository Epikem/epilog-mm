---
title: "ros2-gazebo-install"
slug: "posts/ros2-gazebo-install"
date: "2020-01-15"
template: "post"
category: "blog"
tags: 
  - "cloud"
  - "blog"
  - "arch/arm"
  - "ros"
banner: "/assets/bg/1.jpg"
toc: true
---

# ros2-gazebo-install

(2020-01-15 기준)

http://gazebosim.org/tutorials?tut=ros2_installing&cat=connect_ros#TestingGazeboandROS2integration

위 링크의 설명대로 해보려 했으나 `libgazebo9-dev` 패키지가 종속성 문제를 가지고 있다며 설치가 실패했다.

## 1. 버전 강제로 바꾸어 gazebo9 설치

https://askubuntu.com/questions/433312/broken-packages-from-versions-mismatch

이 링크를 참조하여 `/var/lib/dpkg/status`파일에서 gazebo9의 버전을 강제로 `9.11.0-1~bionic`에서 `9.8.0-1~bionic`으로 수정하고 나서 다시 `sudo apt install gazebo9`을 시도하니 성공했다.

## 2. gazebo-ros-pkgs 설치

gazebo는 꼼수로 설치하고, ros2는 gazebo에서 지원된다고 되어있지도 않은 최신 foxy로 설치되어있지만, 일단 편한 방법을 시도해보려고 [gazebo 설치 설명 페이지](http://gazebosim.org/tutorials?tut=ros2_installing&cat=connect_ros#TestingGazeboandROS2integration)에 나온 대로 `sudo apt install ros-crystal-gazebo-ros-pkgs`명령을 실행했는데, 그냥 설치 성공했다.

이후 뒤에 나오는 통합 테스트도 경고가 하나 뜨긴 했지만 되는 걸 보면 써도 괜찮을 것 같다.

gpu가 없는 aws a1 인스턴스라 그런지 프레임이 절망적이다..



# (삽질기록)

## 1. 위 링크대로 설치 시도
## 2. apt로 gazebo10 설치

gazebo9 설치가 종속성 문제로 실패해서 gazebo10은 설치가 되는지 시험해 보았는데 설치가 되었다.

`sudo apt install gazebo10 libgazebo10-dev`
(뭔가 더 설치해야 할 수 있음)

## 2. 실패 후 gazebo-dev 패키지에서 gazebo 타겟 버전 수정

먼저 설명 절차대로 `vcs import`부분까지 실행한 후, `ws/src/.../gazebo-dev/package.xml`파일에 두 개의 태그가 9 버전 가제보를 쓰도록 세팅되어 있는데, 그 부분을 10으로 바꿔주었다.

## 3. 다시 설치 시도

그리고 다시 `rosdep install`부분부터 실행해봤지만 `libgazebo10-dev`에 대한 rosdep definition을 찾을 수 없다고 나와서 일단 무시하고 다시 `colcon build --symlink-install`을 실행했다.

그러나 안타깝게도 머신이 죽고 말았다. 다시 실행해도 죽어서 결국 포기했다.

## 4. 다시 돌아와서 gazebo9 설치

https://askubuntu.com/questions/433312/broken-packages-from-versions-mismatch

이 링크를 참조하여 `/var/lib/dpkg/status`파일에서 gazebo9의 버전을 강제로 `9.11.0-1~bionic`에서 `9.8.0-1~bionic`으로 수정하고 나서 다시 `sudo apt install gazebo9`을 시도하니 성공했다.

gazebo는 꼼수로 설치하고, ros2는 gazebo에서 지원된다고 되어있지도 않은 최신 foxy로 설치되어있지만, 일단 편한 방법을 시도해보려고 [gazebo 설치 설명 페이지](http://gazebosim.org/tutorials?tut=ros2_installing&cat=connect_ros#TestingGazeboandROS2integration)에 나온 대로 `sudo apt install ros-crystal-gazebo-ros-pkgs`명령을 실행했는데, 그냥 설치 성공했다.

이후 뒤에 나오는 통합 테스트도 경고가 하나 뜨긴 했지만 되는 걸 보면 써도 괜찮을 것 같다.

