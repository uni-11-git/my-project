# 도커 개발 환경 구축 미션

## 0. 터미널 기본 조작 및 권한 실습 로그

- **현재 위치 확인 및 디렉토리 생성**

```bash
$ pwd
/c/Users/goo1902020801/docker-mission/step3

$ mkdir practice && cd practice
$ touch test.txt

 $ ls -l test.txt  # 변경 전 권한 확인
-rw-r--r-- 1 user group 0 Nov 11 14:00 test.txt

$ chmod 644 test.txt

$ ls -l test.txt  # 변경 후 권한 확인
-rw-r--r-- 1 user group 0 Nov 11 14:00 test.txt
 ```

## 1. 실습 내용
- Dockerfile을 이용한 Nginx 웹 서버 이미지 빌드
- 포트 포워딩(8081:80)을 이용한 컨테이너 실행
- 바인드 마운트를 이용한 소스 코드 실시간 반영 실습

## 2. 결과 확인
- 이미지 크기: 약 93.7MB (nginx:alpine 사용)
- 접속 주소: http://localhost:8081
- 바인드 마운트 확인 완료: `index.html` 수정 시 즉시 반영됨

## 3. 학습 로그
- 포트 8080 충돌 해결 (8081로 변경)
- `docker run -v` 옵션을 통한 로컬 디렉토리 연결 성공

## 4. 상세 명령어 실행 로그

### Docker 버전 확인
- 명령어: `docker --version`
- 결과: `Docker version 29.9.2, build a82e528`

### Hello-World 실행 결과
- 명령어: `docker run hello-world`
- 결과:

```bash
Hello from Docker!
This message shows that your installation appears to be working correctly.

To generate this message, Docker took the following steps:
 1. The Docker client contacted the Docker daemon.
 2. The Docker daemon pulled the "hello-world" image from the Docker Hub.
    (amd64)
 3. The Docker daemon created a new container from that image which runs the
    executable that produces the output you are currently reading.
 4. The Docker daemon streamed that output to the Docker client, which sent it
    to your terminal.

To try something more ambitious, you can run an Ubuntu container with:
 $ docker run -it ubuntu bash

Share images, automate workflows, and more with a free Docker ID:
 https://hub.docker.com/

For more examples and ideas, visit:
 https://docs.docker.com/get-started/
 ```

### 3) 보유 이미지 목록 확인 (`images`)
- 명령어: `docker images`
- 결과: 93.7MB 크기의 `my-web-site` 이미지를 포함하여 실습에 필요한 이미지들이 정상적으로 로드됨.

```text
IMAGE                ID             DISK USAGE   CONTENT SIZE
hello-world:latest   c567b231f656       25.9kB         9.49kB
my-web-site:latest   924595acea97       93.7MB         26.4MB
ubuntu:latest        6a06111c3b0a        163MB         45.9MB
 ```

### 4) 실행 중인 컨테이너 확인 (`ps`)
- 명령어: `docker ps`
- 결과: `my-mounted-web` 컨테이너가 8081 포트에서 2시간째 정상 작동 중임을 확인.

```bash
CONTAINER ID   IMAGE     COMMAND                  CREATED       STATUS       PORTS                                     NAMES
4aa1ffd3415d   nginx     "/docker-entrypoint.…"   2 hours ago   Up 2 hours   0.0.0.0:8081->80/tcp, [::]:8081->80/tcp   my-mounted-web
```

### 5) 컨테이너 로그 확인 (`logs`)
- 명령어: `docker logs my-mounted-web`
- 결과: Nginx 서버가 성공적으로 시작되었으며, worker process들이 정상적으로 구동됨을 확인.

```bash
/docker-entrypoint.sh: Configuration complete; ready for start up
2026/11/11 02:45:45 [notice] 1#1: nginx/1.31.8
2026/11/11 02:45:45 [notice] 1#1: start worker processes
... (생략) ...
2026/11/11 02:45:45 [notice] 1#1: start worker process 52
```

### 6) 컨테이너 상세 정보 확인 (`inspect`)
- 명령어: `docker inspect my-mounted-web`
- 주요 확인 사항:
  - **Status**: `running`
  - **Ports**: `8081/tcp` -> `80/tcp`
  - **Binds**: 호스트의 `step3` 폴더와 컨테이너의 `nginx/html` 폴더가 연결됨.


"PortBindings": {
    "80/tcp": [
        {
            "HostIp": "",
            "HostPort": "8081"
        }
    ]
}
```

### 7) Ubuntu 컨테이너 진입 및 내부 확인
- 명령어: `docker run -it ubuntu bash`
- 결과: 컨테이너 내부 쉘 접속 후 리눅스 환경 확인 완료

  - `ls`: 리눅스 표준 디렉토리 구조(bin, dev, etc, root 등) 확인
```bash
bin   dev  home  lib64  mnt  proc  run   srv  tmp  var
boot  etc  lib   media  opt  root  sbin  sys  usr
```


  - `whoami`: 현재 접속 계정이 `root`임을 확인

```bash
root
```

  - `cat /etc/os-release`: `Ubuntu 24.04.1 LTS (Resolute Raccoon)` 버전 정보 확인

```bash
PRETTY_NAME="Ubuntu 26.04.1 LTS"
NAME="Ubuntu"
VERSION_ID="26.04"
VERSION="26.04.1 LTS (Resolute Raccoon)"
VERSION_CODENAME=resolute
ID=ubuntu
ID_LIKE=debian
HOME_URL="https://www.ubuntu.com/"
SUPPORT_URL="https://help.ubuntu.com/"
BUG_REPORT_URL="https://bugs.launchpad.net/ubuntu/"
PRIVACY_POLICY_URL="https://www.ubuntu.com/legal/terms-and-policies/privacy-policy"
UBUNTU_CODENAME=resolute
LOGO=ubuntu-logo
root@9906fe7f9355:/# 
```

### 8) Dockerfile 작성 및 이미지 빌드 (4단계)
- **작성한 Dockerfile 내용**:

  ```dockerfile
  FROM nginx:latest
  # 호스트의 index.html을 컨테이너 내부의 Nginx 기본 경로로 복사
  COPY index.html /usr/share/nginx/html/index.html
  # 80번 포트 노출
  EXPOSE 80
  ```

### 1) 시스템 정보 (docker info)
```bash
Client:
 Version:    29.9.2
 Context:    desktop-linux
 Debug Mode: false
 Plugins:
  agent: Docker AI Agent Runner (Docker Inc.)
    Version:  v1.152.0
    Path:     C:\Users\goo1902020801\.docker\cli-plugins\docker-agent.exe
  ai: Docker AI Agent - Ask Gordon (Docker Inc.)
    Version:  v1.33.0
    Path:     C:\Users\goo1902020801\.docker\cli-plugins\docker-ai.exe
  buildx: Docker Buildx (Docker Inc.)
    Version:  v0.38.1
    Path:     C:\Users\goo1902020801\.docker\cli-plugins\docker-buildx.exe
  compose: Docker Compose (Docker Inc.)
    Version:  v5.6.1
    Path:     C:\Users\goo1902020801\.docker\cli-plugins\docker-compose.exe
  debug: Get a shell into any image or container (Docker Inc.)
    Version:  0.49.0
    Path:     C:\Users\goo1902020801\.docker\cli-plugins\docker-debug.exe
  desktop: Docker Desktop commands (Docker Inc.)
    Version:  4.97.0
    Path:     C:\Users\goo1902020801\.docker\cli-plugins\docker-desktop.exe
  dhi: CLI for managing Docker Hardened Images (Docker Inc.)
    Version:  v0.0.9
    Path:     C:\Users\goo1902020801\.docker\cli-plugins\docker-dhi.exe
  extension: Manages Docker extensions (Docker Inc.)
    Version:  v0.2.32
    Path:     C:\Users\goo1902020801\.docker\cli-plugins\docker-extension.exe
  init: Creates Docker-related starter files for your project (Docker Inc.)
    Version:  v1.4.1
    Path:     C:\Users\goo1902020801\.docker\cli-plugins\docker-init.exe
  mcp: Docker MCP Plugin (Docker Inc.)
    Version:  v0.44.2
    Path:     C:\Users\goo1902020801\.docker\cli-plugins\docker-mcp.exe
  model: Docker Model Runner (Docker Inc.)
    Version:  v1.2.9
    Path:     C:\Users\goo1902020801\.docker\cli-plugins\docker-model.exe
  offload: Docker Offload (Docker Inc.)
    Version:  v0.7.21
    Path:     C:\Users\goo1902020801\.docker\cli-plugins\docker-offload.exe
  pass: Docker Pass Secrets Manager Plugin (beta) (Docker Inc.)
    Version:  v0.3.2
    Path:     C:\Users\goo1902020801\.docker\cli-plugins\docker-pass.exe
  sandbox: "docker sandbox" is deprecated, use Docker Sandboxes instead (Docker Inc.)
    Version:  v0.13.0
    Path:     C:\Users\goo1902020801\.docker\cli-plugins\docker-sandbox.exe
  scout: Docker Scout (Docker Inc.)
    Version:  v1.27.0
    Path:     C:\Users\goo1902020801\.docker\cli-plugins\docker-scout.exe

Server:
 Containers: 1
  Running: 0
  Paused: 0
  Stopped: 1
 Images: 3
 Server Version: 29.9.2
 Storage Driver: overlayfs
  driver-type: io.containerd.snapshotter.v1
 Logging Driver: local
 Cgroup Driver: cgroupfs
 Cgroup Version: 2
 Plugins:
  Volume: local
  Network: bridge host ipvlan macvlan null overlay
  Log: awslogs fluentd gcplogs gelf journald json-file local splunk syslog
 CDI spec directories:
  /etc/cdi
  /var/run/cdi
 Discovered Devices:
  cdi: docker.com/gpu=webgpu
 Swarm: inactive
 Runtimes: io.containerd.runc.v2 nvidia runc
 Default Runtime: runc
 Init Binary: docker-init
 containerd version: b7a25261e029d68978bbf290df0a62fc819b3c51
 runc version: v1.5.2-0-g29dd3dc2
 init version: de40ad0
 Security Options:
  seccomp
   Profile: builtin
  cgroupns
 Kernel Version: 6.18.54.1-microsoft-standard-WSL2
 Operating System: Docker Desktop
 OSType: linux
 Architecture: x86_64
 CPUs: 24
 Total Memory: 15.25GiB
 Name: docker-desktop
 ID: 6dcf535e-8623-44b2-82d8-51fde3fd42f9
 Docker Root Dir: /var/lib/docker
 Debug Mode: false
 HTTP Proxy: http.docker.internal:3128
 HTTPS Proxy: http.docker.internal:3128
 No Proxy: hubproxy.docker.internal
 Labels:
  com.docker.desktop.address=npipe://\\.\pipe\docker_cli
 Experimental: false
 Insecure Registries:
  hubproxy.docker.internal:5555
  127.0.0.0/8
  ::1/128
 Live Restore Enabled: false
 Firewall Backend: iptables
 ```

### 2) 컨테이너 로그
```bash
 $ docker logs my-mounted-web
/docker-entrypoint.sh: /docker-entrypoint.d/ is not empty, will attempt to perform configuration
/docker-entrypoint.sh: Looking for shell scripts in /docker-entrypoint.d/
/docker-entrypoint.sh: Launching /docker-entrypoint.d/10-listen-on-ipv6-by-default.sh
10-listen-on-ipv6-by-default.sh: info: Getting the checksum of /etc/nginx/conf.d/default.conf
10-listen-on-ipv6-by-default.sh: info: Enabled listen on IPv6 in /etc/nginx/conf.d/default.conf
/docker-entrypoint.sh: Sourcing /docker-entrypoint.d/15-local-resolvers.envsh
/docker-entrypoint.sh: Launching /docker-entrypoint.d/20-envsubst-on-templates.sh
/docker-entrypoint.sh: Launching /docker-entrypoint.d/30-tune-worker-processes.sh
/docker-entrypoint.sh: Configuration complete; ready for start up
2026/11/11 02:45:45 [notice] 1#1: using the "epoll" event method
2026/11/11 02:45:45 [notice] 1#1: nginx/1.31.8
2026/11/11 02:45:45 [notice] 1#1: built by gcc 14.2.0 (Debian 14.2.0-19) 
2026/11/11 02:45:45 [notice] 1#1: OS: Linux 6.18.54.1-microsoft-standard-WSL2
2026/11/11 02:45:45 [notice] 1#1: getrlimit(RLIMIT_NOFILE): 1048576:1048576
2026/11/11 02:45:45 [notice] 1#1: start worker processes
2026/11/11 02:45:45 [notice] 1#1: start worker process 29
2026/11/11 02:45:45 [notice] 1#1: start worker process 30
2026/11/11 02:45:45 [notice] 1#1: start worker process 31
2026/11/11 02:45:45 [notice] 1#1: start worker process 32
2026/11/11 02:45:45 [notice] 1#1: start worker process 33
2026/11/11 02:45:45 [notice] 1#1: start worker process 34
2026/11/11 02:45:45 [notice] 1#1: start worker process 35
2026/11/11 02:45:45 [notice] 1#1: start worker process 36
2026/11/11 02:45:45 [notice] 1#1: start worker process 37
2026/11/11 02:45:45 [notice] 1#1: start worker process 38
2026/11/11 02:45:45 [notice] 1#1: start worker process 39
2026/11/11 02:45:45 [notice] 1#1: start worker process 40
2026/11/11 02:45:45 [notice] 1#1: start worker process 41
2026/11/11 02:45:45 [notice] 1#1: start worker process 42
2026/11/11 02:45:45 [notice] 1#1: start worker process 43
2026/11/11 02:45:45 [notice] 1#1: start worker process 44
2026/11/11 02:45:45 [notice] 1#1: start worker process 45
2026/11/11 02:45:45 [notice] 1#1: start worker process 46
2026/11/11 02:45:45 [notice] 1#1: start worker process 47
2026/11/11 02:45:45 [notice] 1#1: start worker process 48
2026/11/11 02:45:45 [notice] 1#1: start worker process 49
2026/11/11 02:45:45 [notice] 1#1: start worker process 50
2026/11/11 02:45:45 [notice] 1#1: start worker process 51
2026/11/11 02:45:45 [notice] 1#1: start worker process 52
 ```

### 3) 컨테이너 상태 확인 (ps)
```bash
 $ docker ps
CONTAINER ID   IMAGE     COMMAND                   CREATED             STATUS             PORTS                                NAMES
4aa1ffd3415d   nginx     "/docker-entrypoint.…"   About an hour ago   Up About an hour   0.0.0.0:8081->80/tcp, [::]:8081->80/tcp   my-mounted-web
```

### 4) 리소스 사용 현황

```bash
 $ docker stats --no-stream
CONTAINER ID   NAME             CPU %     MEM USAGE / LIMIT    MEM %     NET I/O        BLOCK I/O  PIDS
4aa1ffd3415d   my-mounted-web   0.00%     21.7MiB / 15.25GiB   0.14%     1.7kB / 126B   0B / 12.3kB  25
 ```

## 5. Docker 볼륨 검증 (Volume Persistence)

볼륨을 생성하고 데이터를 저장하여, 컨테이너가 삭제되어도 데이터가 유지되는지 확인했습니다.

- **볼륨 생성 및 데이터 쓰기**: 
  `docker run --rm -v my-vol://app/data alpine sh -c "echo 'hello volume' > //app/data/result.txt"`
- **데이터 읽기 확인**:
  ```bash
  $ docker run --rm -v my-vol://app/data alpine cat //app/data/result.txt
  hello volume
  ```



 ## 🛠 트러블슈팅 (Troubleshooting)

### 1. 포트 충돌 문제 (Port Conflict)
- **현상**: `docker run` 실행 시 8080 포트가 이미 사용 중이라는 에러와 함께 컨테이너 시작 실패.
- **원인**: 로컬 호스트(PC)에서 다른 서비스가 이미 8080 포트를 점유하고 있음.
- **해결**: 호스트 포트를 8081로 변경하여 실행 (`-p 8081:80`) 함으로써 문제 해결.

### 2. Git Bash 경로 인식 오류 (Path Conversion)
- **현상**: 볼륨 마운트 확인 시 `cat: 'C:/Program Files/Git/app/data/result.txt': No such file or directory` 에러 발생.
- **원인**: Windows Git Bash 환경에서 컨테이너 내부 경로(`/app/data`)를 Windows 절대 경로로 자동 변환하여 발생한 문제.
- **해결**: 경로 앞에 슬래시를 하나 더 추가(`//app/data`)하여 Git Bash의 자동 변환을 방지하고 컨테이너 경로임을 명시함.

## 6. Git 설정 및 GitHub 연동 확인

- **Git 설정 정보 (`git config --list`)**
```bash
$ git config --list
user.name=KHS
user.email=KHS@gmail.com
init.defaultbranch=master
remote.origin.url=https://github.com/uni-11-git/my-project.git
# ... (기타 설정 생략)