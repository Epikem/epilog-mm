---
title: "ros2-setup-on-aws-arm64"
slug: "posts/ros2-setup-on-aws-arm64"
date: "2020-01-14"
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

# ros2-setup-on-aws-arm64

(2020-01-15 기준)

## 1. netspectrum ubuntu desktop arm 64 bit a1xlarge instance를 실행
## 2. 퍼블릭 도메인 접속해서 instance ID로 인스턴스에 접속

Netspectrum에서 vnc 서버 세팅을 다 해놓았기 때문에 접속하면 데스크탑처럼 쓸 수 있다. 되도록 가까운 aws region을 선택해야 반응이 빠르다. 아쉽게도 aws 기본 이미지는 아니므로 아주 저렴한 스팟 인스턴스를 사용하지는 못한다. 

브라우저에 퍼블릭 도메인 주소를 복붙해 넣으면, 패스워드를 넣으라고 뜬다. 초기 비밀번호는 해당 ec2 인스턴스의 인스턴스 id이다.

## 3. sudo apt update / sudo apt upgrade 실행

만약 안 되면 (dpkg 관련 에러 뜰 수 있음) `sudo reboot`후 다시 시도.
중간에 cloud config가 바뀌었다는 게 뜨는데, `Y`를 선택했다.


## 4. ros2 설치 시작

-> 요약: [이 링크의 절차](https://index.ros.org/doc/ros2/Installation/Foxy/Linux-Development-Setup/#system-requirements)만 잘 따라가면 된다.

https://index.ros.org/doc/ros2/Tutorials/Cross-compilation/#cross-compiling-examples-for-arm

https://github.com/ros-tooling/cross_compile

https://index.ros.org/doc/ros2/Installation/Foxy/Linux-Development-Setup/#system-requirements

여기를 참조하였다.

현재 [ros2 cross-compilation 문서](https://index.ros.org/doc/ros2/Tutorials/Cross-compilation/)에 따르면 automated-cross-compilation이 가능하다고 하지만, 시도해보면 처음부터 파일이 없다고 나온다. 그래서 관련된 깃헙 주소인 https://github.com/ros-tooling/cross_compile 여기로 들어가서 확인해보니 그런 파일은 없고 다른 방식으로 cross-compile을 하는 절차가 적혀 있다. 이 저장소에서 `Dockerfile_ros`라는 파일을 찾았는데, 위에서 찾았던 자동 설치 파일이 이름이 바뀐걸까? 하고 확인해 봤지만 파일 안에 다음과 같은 내용이 있는 것으로 보아 틀린 것 같다.

```dockerfile
# Assumptions: ros_ws/src and qemu-user-static directories are present in ${ROS_WORKSPACE}
...
```

cross-compile 튜토리얼들을 찾아보면 sysroot, qemu같은 걸 설정하는 부분이 많은데, 이게 대체 뭘 하는건지 잘 이해가 안 된다.


일단 `ros-tooling/cross-compile` 저장소에서 시키는 대로, 먼저 ros2 dependencies를 설치하기 위해 세 번째 링크에 해당하는 절차들을 실행했다. 그런데, ... 그냥 ros2 설치가 되었다.

알고보니 ros2는 arm은 1티어로 지원하는 툴이기 때문에 그냥 기본 방법만 잘 따라해도 설치가 되는 것이었다. cross-compile은 그냥 개발자가 특수 상황이거나 해서 자기 패키지/특정 패키지를 컴파일 해야 할 때 쓰는 것이다..

그래서 그냥 세 번째 링크에 있는 절차만 따라가면 된다. 중간에 symlink install할 때 `--merge-install` 옵션을 주면 ros 패키지들을 한 폴더로 합칠 수 있는 모양이다. 그걸 쓰지 않으니 모든 ros 기본 패키지들이 각각의 폴더에 설치되었다.
