---
title: "[ROS2] Jazzy Jalisco on Apple M1 with Devcontainer and Docker"
categories: 
  - ros2
---
# Requirements

1. M1 Mac
2. 10~20GB Storage 여유 공간
3. Docker

docker가 존재한다면 문제 없이 버전 정보가 나올 것이다.

```bash
docker version
```

# Devcontainer

```bash
mkdir .devcontainer
```

```txt
  orb_slam3_ws
  └── .devcontainer/
      ├── Dockerfile
      └── devcontainer.json
```

1. Ctrl + Shift + P
2. .devcontainer 폴더와 내부 파일 구성
3. Dev Containers: Rebuild and Reopen in Container

# Docker

>We currently support macOS Mojave (10.14).

https://docs.ros.org/en/jazzy/Installation/Alternatives/macOS-Development-Setup.html

-> M1에서 ROS2 Jazzy Jalisco를 사용하기 위해서는 Docker를 사용할 수밖에 없다.

다음은 Official ROS Docker Image이다. https://hub.docker.com/_/ros

```
jazzy-ros-core, jazzy-ros-core-noble⁠

jazzy-ros-base, jazzy-ros-base-noble, jazzy, latest⁠

jazzy-perception, jazzy-perception-noble⁠
```

각각은 포함된 ROS package 수준이 다르다.

| 이미지 이름              | 포함 패키지 수준            | 포함된 도구                              | 용도 설명                          |
|--------------------------|-----------------------------|------------------------------------------|------------------------------------|
| `ros:jazzy-ros-core`     | 최소 구성 (runtime 중심)     | ROS 핵심 통신 구조만                     | 초경량, pub/sub 테스트용           |
| `ros:jazzy-ros-base`     | 표준 구성 (개발에 적합)      | ROS CLI, `colcon`, 실행 도구 포함        | ✅ 일반 개발용                     |
| `ros:jazzy-perception`   | 고급 구성 (센서/비전 포함)   | `cv_bridge`, 센서/이미지 처리 패키지 포함 | 카메라, LiDAR, 이미지 처리 프로젝트용 |

*Docker Container 실행 명령어*

```bash
docker run [options] [image_name]

docker run --platform linux/amd64 -it ros:jazzy-ros-base
```

`--platform linux/amd64` docker container를 AMD64 architecture (Intel/AMD 기반)로 실행한다. M1 Mac은 ARM64 architecture인데 ROS official docker images은 기본적으로 AMD64용으로 build된다. M1에서 AMD64 image를 돌리기 위해 이 option을 줘야 한다. 그렇지 않으면 M1에서 실행 시 architecture 불일치로 error가 나거나 image가 없다고 뜬다.

`-it` interactive, pseudo-TTY 이 두 옵션은 terminal에서 container 내부 shell에 접속할 수 있게 해준다. 즉, container 안에서 bash 같은 shell을 사용할 수 있다.

```bash
kevinliam@Kevins-MacBook-Air orb_slam3_ws % docker run --platform linux/amd64 -it --rm ros:jazzy-ros-base
```

```bash
Unable to find image 'ros:jazzy-ros-base' locally
jazzy-ros-base: Pulling from library/ros
0622fac788ed: Pull complete 
34f63233f789: Pull complete 
2f6b9c42df52: Pull complete 
5176cb754ce9: Pull complete 
b6c5e46a5a33: Pull complete 
247a03c6dd3d: Pull complete 
9db6612a791b: Pull complete 
5443d20d5093: Pull complete 
941b252d0546: Pull complete 
1b35756b3bec: Pull complete 
b5f3983963a9: Pull complete 
Digest: sha256:4629fd3705b39eef374f61ad6a03a2e07f3a441dfa118190dd959ff7472a112e
Status: Downloaded newer image for ros:jazzy-ros-base
```

제대로 설치되었다면 이후 terminal이 다음과 같이 바뀐다.

```bash
root@abcdef123456:/# 
```

현재 경로를 확인한다.

```bash
root@b044689eb3c8:/# ls
```

```bash
bin   dev  home  lib64  media  opt   root               run   srv  tmp  var
boot  etc  lib   log    mnt    proc  ros_entrypoint.sh  sbin  sys  usr
```

# Getting Started with ROS2

root 경로에 workspace를 만든다. 초기에는 root 경로에 어떤 파일괖 폴더도 존재하지 않을 것이다.

```bash
root@b044689eb3c8:/# cd root
```

folder structure 만들기

```bash
mkdir -p /root/[workspace_name]/src
cd /root/[workspace_name]
colcon build --symlink-install
```
