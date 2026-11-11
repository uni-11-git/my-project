# 도커 개발 환경 구축 미션

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

### 3) 리소스 사용 현황

```bash
 $ docker stats --no-stream
CONTAINER ID   NAME             CPU %     MEM USAGE / LIMIT    MEM %     NET I/O        BLOCK I/O  PIDS
4aa1ffd3415d   my-mounted-web   0.00%     21.7MiB / 15.25GiB   0.14%     1.7kB / 126B   0B / 12.3kB  25
 ```