---
title: "ros2-install-turtlebot3-on-aws-arm64"
date: "2020-01-16"
template: "post"
category: "blog"
tags: 
  - "cloud"
  - "blog"
  - "arch/arm"
  - "ros"
toc: true
---

## turtlebot3 설치

ros2 설치했던 폴더에 같이 [여기](http://emanual.robotis.com/docs/en/platform/turtlebot3/pc_setup/#install-dependent-ros-packages) 나온 절차대로 합친 후 `colcon build --symlink-install`을 하니 되었는데, 별로 좋은 방법은 아닌 것 같다. 따로 폴더를 해서 하는 게 나을 듯.

따로 폴더를 해서 설치 시도했을 때 `CMake`에서 소스 경로가 불일치한다는 식으로 에러가 떠서 이름을 같게 하기 위해 `turtlebot_ws` 폴더 이름을 `ros_ws`로 바꾸고, `ros_ws/src/turtlebot3`아래에 있는 폴더들을 한 칸 위로 옮겼더니 `colcon build --symlink-install`이 성공했다.

그리고 이제 [시뮬레이션](http://emanual.robotis.com/docs/en/platform/turtlebot3/simulation/#turtlebot3-simulation-using-gazebo)을 돌려보려고 `ros2 launch turtlebot3_gazebo turtlebot3_world.launch`를 실행했는데 launch파일을 찾을 수 없다며 실패했다.

혹시 빌드할 때 `--symlink-install` 옵션을 주어서 그런가 싶어 다시 `colcon build`를 하고 시도해 보니 실행은 되는데 월드에 아무런 맵이나 로봇도 뜨지 않았다.


```log

[gazebo-1] [Err] [Plugin.hh:187] Failed to load plugin libgazebo_ros_init.so: libgazebo_ros_init.so: cannot open shared object file: No such file or directory
[gazebo-1] [Msg] Waiting for master.
[gazebo-1] [Msg] Connected to gazebo master @ http://127.0.0.1:11345

[gazebo-1] [Wrn] [ModelDatabase.cc:340] Getting models from[http://models.gazebosim.org/]. This may take a few seconds.
[gazebo-1] [Wrn] [SystemPaths.cc:459] File or path does not exist [""] [model://turtlebot3_world]
[gazebo-1] [Wrn] [SystemPaths.cc:459] File or path does not exist [""] [model://turtlebot3_waffle_pi]
[gazebo-1] Error Code 11 Msg: Unable to find uri[model://turtlebot3_world]
[gazebo-1] Error Code 11 Msg: Unable to find uri[model://turtlebot3_waffle_pi]
```

결국 또 어디선가 설치가 잘못된 것인가...