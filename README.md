# 개발 워크스테이션 구축
---
# 1. 미션 소개 (요약)
 이 미션은 개발을 시작하기 전에 기본환경인 리눅스 CLI환경,Docker, Git/GitHub를 직접 세팅하고 활용해 보는 것을 목표
 터미널을 통해 작업 디렉토리와 권한을 관리하고, Docker를 이용해 컨테이너를 실행 및 관리한다.
이 과정에서 이미지와 컨테이너의 차이, 격리된 실행환경, 포트 및 스토리지 연결 방식 등 Docker의 핵심 개념을 이해한다. 최종적으로는 "내 컴퓨터에서만 실행되는 환경"이 아니라, 팀원 누구나 같은 방식으로 실행할 수 있는 개발 환경을 만드는 것을 경험한다.

# 2. 실행환경(OS/쉘/터미널, Docker 버전, Git 버전)
 항목 | 사용 환경 | 확인 명령 |
| --- | --- | --- |
| OS | `ProductName : <MacOs>`, `ProductVersion : <15.7.4>`, `BuildVersion : <24G517>` | `sw_vers`| 
| Shell | `</bin/zsh>` | `echo $SHELL` | 
| 터미널 | `<vscode>` |`echo $TERM_PROGRAM`| 
| Docker | `<29.4.0>, <build 9d7ad9f>` | `docker --version` |
| Docker 데몬 동작 확인 | 현재 실행 중인 컨테이너 수, 이미지 수, 시스템 정보 등 Docker 엔진의 상세 상태 출력 (에러 없이 출력되면 정상 동작 중임을 의미) | `docker info` |
| Git | `<git version 2.53.0>` | `git --version` |

### 환경 확인 로그
```bash
ProductName:            macOS
ProductVersion:         15.7.4
BuildVersion:           24G517

$ echo $SHELL
/bin/zsh

$ echo $TERM_PROGRAM
vscode

$ docker --version
Docker version 28.5.2, build ecc6942

$ docker info

$ git --version
git version 2.53.0
```
### 실행 증거

[실행 환경 확인 로그 보기](./images/environment-check.png)

---
# 3. 도커 연습용 폴더 만들기 & 터미널 기본 명령어 및 파일 조작 실습

| 진행 단계 | 실행 결과 및 설명 | 명령어 |
| --- | --- | --- |
| **현재 위치 확인** | `/Users/사용자명` (현재 작업 중인 절대 경로 출력) | `pwd` |
| **연습용 폴더 생성** | `docker-study` 폴더 생성 (성공 시 화면 출력 없음) | `mkdir docker-study` |
| **폴더로 이동** | `docker-study` 폴더 내부로 진입 | `cd docker-study` |
| **빈 파일 생성** | `test.txt`라는 이름의 빈 파일 생성 | `touch test.txt` |
| **상세 목록 확인** | 숨김 파일 포함, 파일의 권한/소유자/날짜 등 상세 정보 출력 | `ls -al` |
| **파일 내용 확인** | 방금 만든 빈 파일이므로 아무 내용도 출력되지 않음 | `cat test.txt` |
| **파일 복사** | `test.txt`를 복사하여 `copy.txt`라는 동일한 파일 생성 | `cp test.txt copy.txt` |
| **이름 변경(이동)** | `copy.txt` 파일의 이름을 `hello.txt`로 변경 | `mv copy.txt hello.txt` |
| **파일 삭제** | `hello.txt` 파일 삭제 (성공 시 화면 출력 없음) | `rm hello.txt` |

### 환경 확인 로그
```bash
dlckdwls763222@c3r5s3 CodysseyFirstMission_E1_1 % pwd 
/Users/dlckdwls763222/CodysseyFirstMission_E1_1
dlckdwls763222@c3r5s3 CodysseyFirstMission_E1_1 % mkdir docker-study
dlckdwls763222@c3r5s3 CodysseyFirstMission_E1_1 % cd coker-study
cd: no such file or directory: coker-study
dlckdwls763222@c3r5s3 CodysseyFirstMission_E1_1 % pwd
/Users/dlckdwls763222/CodysseyFirstMission_E1_1
dlckdwls763222@c3r5s3 CodysseyFirstMission_E1_1 % cd docker-study
dlckdwls763222@c3r5s3 docker-study % pwd
/Users/dlckdwls763222/CodysseyFirstMission_E1_1/docker-study
dlckdwls763222@c4r8s5 docker-study % ls -al
total 8
drwxr-xr-x  3 dlckdwls763222  dlckdwls763222   96 Jul 29 18:23 .
drwxr-xr-x  6 dlckdwls763222  dlckdwls763222  192 Jul 29 18:18 ..
-rw-r--r--  1 dlckdwls763222  dlckdwls763222    3 Jul 29 18:18 test.txt
dlckdwls763222@c4r8s5 docker-study % cat test.txt
123%
dlckdwls763222@c4r8s5 docker-study % cp test.txt copy.txt
dlckdwls763222@c4r8s5 docker-study % mv copy.txt hello.txt
dlckdwls763222@c4r8s5 docker-study % rm hello.txt
```


절대 경로 (Absolute Path)

개념: 변하지 않는 **'전체 도로명 주소'**입니다. (예: 서울특별시 강남구 테헤란로 123)
특징: 내가 지금 어디에 있든, 이 주소를 입력하면 무조건 그곳으로 갑니다. 맥(Mac)에서는 항상 최상위 뿌리인 / 부터 시작합니다.
예시: /Users/dlckdwls763222/CodysseyFirstMission_E1_1/docker-study

상대 경로 (Relative Path)

개념: **'지금 내가 있는 위치'**를 기준으로 말하는 주소입니다. (예: "지금 있는 곳에서 뒤로 한 칸 가", "내 바로 앞에 있는 폴더로 들어가")
특징: 터미널에서 가장 많이 쓰는 방식입니다.

---
# 4. 파일 및 디렉토리 권한 (Permission) 이해 & 실습
1. 세 가지 권한 (r, w, x)

r (Read, 4점): 읽기 권한 (파일 내용 보기)
w (Write, 2점): 쓰기 권한 (파일 수정, 삭제)
x (Execute, 1점): 실행 권한 (프로그램 실행, 폴더 들어가기)
- (0점): 권한 없음

2. 세 종류의 사람
권한은 항상 3자리 숫자로 줍니다. (예: 755)

첫 번째 숫자: 나 (소유자)=
두 번째 숫자: 우리 팀 (그룹)
세 번째 숫자: 남 (기타 사용자)
3. 숫자 계산법 (더하기만 하면 됩니다!)

7 = 4(r) + 2(w) + 1(x)  읽고, 쓰고, 실행 다 해! (모든 권한)
6 = 4(r) + 2(w) + 0  읽고, 쓰기만 해! (수정은 되지만 실행은 안 됨)
5 = 4(r) + 0 + 1(x)  읽고, 실행만 해! (수정은 안 됨)
4 = 4(r) + 0 + 0  읽기만 해!

755: 나(7)는 다 할 수 있고, 남들(5)은 읽고 실행만 가능. (주로 폴더나 실행 파일에 씀)
644: 나(6)는 읽고 수정할 수 있고, 남들(4)은 읽기만 가능. (주로 텍스트 문서 같은 일반 파일에 씀)

| 진행 단계 | 실행 결과 및 설명 | 명령어 |
| --- | --- | --- |
| **실습용 파일/폴더 생성** | 권한 테스트를 위한 빈 파일과 폴더 생성 | `touch perm_file.txt`<br>`mkdir perm_dir` |
| **변경 전 권한 확인** | 파일은 `644(-rw-r--r--)`, 폴더는 `755(drwxr-xr-x)`로 기본 설정됨 확인 | `ls -al` |
| **파일 권한 변경** | 파일 권한을 `777`로 변경 (모든 사용자에게 읽기/쓰기/실행 허용) | `chmod 777 perm_file.txt` |
| **폴더 권한 변경** | 폴더 권한을 `700`으로 변경 (소유자만 접근 및 읽기/쓰기 가능) | `chmod 700 perm_dir` |
| **변경 후 권한 확인** | 파일은 `-rwxrwxrwx`, 폴더는 `drwx------`로 권한이 변경된 것을 확인 | `ls -al` |

### 환경 확인 로그
```bash
dlckdwls763222@c3r5s3 docker-study % touch test.txt
dlckdwls763222@c3r5s3 docker-study % ls -l test.txt
-rw-r--r--  1 dlckdwls763222  dlckdwls763222  0 Jul 29 17:06 test.txt
dlckdwls763222@c3r5s3 docker-study % chmod 755 test.txt
dlckdwls763222@c3r5s3 docker-study % ls -l test.txt
-rwxr-xr-x  1 dlckdwls763222  dlckdwls763222  0 Jul 29 17:06 test.txt
dlckdwls763222@c3r5s3 docker-study % chmod644 test.txt
zsh: command not found: chmod644
dlckdwls763222@c3r5s3 docker-study % chmod 644 test.txt
dlckdwls763222@c3r5s3 docker-study % ls -l text.txt
ls: text.txt: No such file or directory
dlckdwls763222@c3r5s3 docker-study % ls -l test.txt
-rw-r--r--  1 dlckdwls763222  dlckdwls763222  0 Jul 29 17:06 test.txt
dlckdwls763222@c4r8s5 CodysseyFirstMission_E1_1 % mkdir perm_dir
dlckdwls763222@c4r8s5 CodysseyFirstMission_E1_1 % touch perm_file.txt
dlckdwls763222@c4r8s5 CodysseyFirstMission_E1_1 % ls -la
total 80
drwxr-xr-x   8 dlckdwls763222  dlckdwls763222    256 Jul 29 18:46 .
drwxr-x---+ 14 dlckdwls763222  dlckdwls763222    448 Jul 29 18:18 ..
drwxr-xr-x  12 dlckdwls763222  dlckdwls763222    384 Jul 29 18:18 .git
drwxr-xr-x   3 dlckdwls763222  dlckdwls763222     96 Jul 29 18:45 docker-study
drwxr-xr-x   2 dlckdwls763222  dlckdwls763222     64 Jul 29 18:45 perm_dir
-rw-r--r--   1 dlckdwls763222  dlckdwls763222      0 Jul 29 18:46 perm_file.txt
-rw-r--r--   1 dlckdwls763222  dlckdwls763222  24398 Jul 29 18:18 README.md
-rw-r--r--   1 dlckdwls763222  dlckdwls763222  13795 Jul 29 18:18 README2.md
dlckdwls763222@c4r8s5 CodysseyFirstMission_E1_1 % chmod 777 perm_file.txt
dlckdwls763222@c4r8s5 CodysseyFirstMission_E1_1 % chmod 700 perm_dir
dlckdwls763222@c4r8s5 CodysseyFirstMission_E1_1 % ls -al
total 80
drwxr-xr-x   8 dlckdwls763222  dlckdwls763222    256 Jul 29 18:46 .
drwxr-x---+ 14 dlckdwls763222  dlckdwls763222    448 Jul 29 18:18 ..
drwxr-xr-x  12 dlckdwls763222  dlckdwls763222    384 Jul 29 18:18 .git
drwxr-xr-x   3 dlckdwls763222  dlckdwls763222     96 Jul 29 18:45 docker-study
drwx------   2 dlckdwls763222  dlckdwls763222     64 Jul 29 18:45 perm_dir
-rwxrwxrwx   1 dlckdwls763222  dlckdwls763222      0 Jul 29 18:46 perm_file.txt
-rw-r--r--   1 dlckdwls763222  dlckdwls763222  24398 Jul 29 18:18 README.md
-rw-r--r--   1 dlckdwls763222  dlckdwls763222  13795 Jul 29 18:18 README2.md
```
---

# 5. 도커 핵심 개념 및 컨테이너 실행하기
1. 도커의 핵심 개념 (이미지와 컨테이너)
이미지 (Image)
프로그램을 실행하는 데 필요한 모든 것(OS, 설정, 코드)이 담겨 있는 변하지 않는 원본 파일이다.
컨테이너 (Container) 
이미지를 바탕으로 실제로 메모리에 올라가 실행된 상태를 말한다. 하나의 이미지로 여러 개의 컨테이너를 독립적으로 실행할 수 있다.
포트 매핑 (Port Mapping) = "-p 8080:80"
내 컴퓨터(Mac)의 특정 포트(예: 8080)로 들어오는 접속을, 도커 컨테이너 내부의 포트(예: 80)로 연결해 주는 작업이다.

2. 도커 기본 명령어 실습
진행 단계	실행 결과 및 설명	명령어
Hello World 실행	도커가 정상 작동하는지 확인하는 테스트용 컨테이너 실행	docker run hello-world
전체 컨테이너 조회	실행 중이거나 종료된 모든 컨테이너의 목록(ID, 상태 등) 확인	docker ps -a
웹 서버(Nginx) 실행	백그라운드(-d)에서 8080포트를 열고 my-web이라는 이름으로 Nginx 실행	docker run -d -p 8080:80 --name my-web nginx
실행 중인 컨테이너 조회	현재 살아서 돌아가고 있는 컨테이너만 확인	docker ps
컨테이너 삭제	실습이 끝난 컨테이너를 강제(-f)로 삭제	docker rm -f my-web

| 진행 단계 | 실행 결과 및 설명 | 명령어 |
| --- | --- | --- |
| **컨테이너 실행** | `hello-world` 이미지를 다운로드하고 컨테이너를 실행하여 환영 메시지 출력 | `docker run hello-world` |
| **모든 컨테이너 조회** | 실행 중이거나 종료된 모든 컨테이너의 목록(ID, 이미지, 상태, 이름 등)을 확인 | `docker ps -a` |
| **웹 서버(Nginx) 실행** | 백그라운드(`-d`)에서 8080포트를 열고 `my-web`이라는 이름으로 Nginx 실행 | `docker run -d -p 8080:80 --name my-web nginx` |
| **실행 중인 컨테이너 조회** | 현재 살아서 돌아가고 있는 컨테이너만 확인 | `docker ps` |
| **컨테이너 로그 확인** | 컨테이너 내부에서 발생한 기록(접속 내역, 에러 등)을 확인 | `docker logs my-web` |
| **컨테이너 중지** | 실행 중인 컨테이너를 안전하게 종료 (삭제되는 것은 아님) | `docker stop my-web` |
| **컨테이너 삭제** | 실습이 끝난 컨테이너를 강제(`-f`)로 삭제 | `docker rm -f my-web` |

### 환경 확인 로그
```bash
dlckdwls763222@c3r5s3 docker-study % docker run hello-world

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

dlckdwls763222@c4r8s5 docker-study % docker ps -a
CONTAINER ID   IMAGE         COMMAND    CREATED          STATUS                      PORTS     NAMES
767c627c402d   hello-world   "/hello"   35 seconds ago   Exited (0) 34 seconds ago             pensive_hopper
dlckdwls763222@c4r8s5 docker-study % docker run -d -p 8080:80 --name my-web nginx  
Unable to find image 'nginx:latest' locally
latest: Pulling from library/nginx
062e450697fa: Pull complete 
82454cdbf456: Pull complete 
3c7ab7949321: Pull complete 
cacfcdd01f30: Pull complete 
b6698f04e005: Pull complete 
2bedaf25031a: Pull complete 
d26f27cc8c41: Pull complete 
Digest: sha256:5a88c9c45479443d7be2eadc894b4ed0a9801bae03d97a5760ae13b5c2005942
Status: Downloaded newer image for nginx:latest
3bbae6d4e2c5c9965af782b0bf938f13f4707fd8cd97f1b675c760e549e7d9c1
dlckdwls763222@c4r8s5 docker-study % docker ps
CONTAINER ID   IMAGE     COMMAND                  CREATED              STATUS              PORTS                                     NAMES
3bbae6d4e2c5   nginx     "/docker-entrypoint.…"   About a minute ago   Up About a minute   0.0.0.0:8080->80/tcp, [::]:8080->80/tcp   my-web

dlckdwls763222@c4r8s5 docker-study % docker logs my-web
/docker-entrypoint.sh: /docker-entrypoint.d/ is not empty, will attempt to perform configuration
/docker-entrypoint.sh: Looking for shell scripts in /docker-entrypoint.d/
/docker-entrypoint.sh: Launching /docker-entrypoint.d/10-listen-on-ipv6-by-default.sh
10-listen-on-ipv6-by-default.sh: info: Getting the checksum of /etc/nginx/conf.d/default.conf
10-listen-on-ipv6-by-default.sh: info: Enabled listen on IPv6 in /etc/nginx/conf.d/default.conf
/docker-entrypoint.sh: Sourcing /docker-entrypoint.d/15-local-resolvers.envsh
/docker-entrypoint.sh: Launching /docker-entrypoint.d/20-envsubst-on-templates.sh
/docker-entrypoint.sh: Launching /docker-entrypoint.d/30-tune-worker-processes.sh
/docker-entrypoint.sh: Configuration complete; ready for start up
2026/07/29 10:01:30 [notice] 1#1: using the "epoll" event method
2026/07/29 10:01:30 [notice] 1#1: nginx/1.31.3
2026/07/29 10:01:30 [notice] 1#1: built by gcc 14.2.0 (Debian 14.2.0-19) 
2026/07/29 10:01:30 [notice] 1#1: OS: Linux 6.17.8-orbstack-00308-g8f9c941121b1
2026/07/29 10:01:30 [notice] 1#1: getrlimit(RLIMIT_NOFILE): 20480:1048576
2026/07/29 10:01:30 [notice] 1#1: start worker processes
2026/07/29 10:01:30 [notice] 1#1: start worker process 29
2026/07/29 10:01:30 [notice] 1#1: start worker process 30
2026/07/29 10:01:30 [notice] 1#1: start worker process 31
2026/07/29 10:01:30 [notice] 1#1: start worker process 32
2026/07/29 10:01:30 [notice] 1#1: start worker process 33
2026/07/29 10:01:30 [notice] 1#1: start worker process 34
192.168.215.1 - - [29/Jul/2026:10:02:29 +0000] "GET / HTTP/1.1" 200 896 "-" "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_7) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/146.0.0.0 Safari/537.36" "-"
192.168.215.1 - - [29/Jul/2026:10:02:29 +0000] "GET /favicon.ico HTTP/1.1" 404 555 "http://localhost:8080/" "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_7) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/146.0.0.0 Safari/537.36" "-"
2026/07/29 10:02:29 [error] 29#29: *1 open() "/usr/share/nginx/html/favicon.ico" failed (2: No such file or directory), client: 192.168.215.1, server: localhost, request: "GET /favicon.ico HTTP/1.1", host: "localhost:8080", referrer: "http://localhost:8080/"

dlckdwls763222@c4r8s5 docker-study % docker stop my-web
my-web

dlckdwls763222@c4r8s5 docker-study % docker rm -f my-web                                                                                                                  
my-web
```

---
# 6.컨테이너 실행 실습 및 개념 정리

### 6-1. 컨테이너 실행 및 내부 진입 실습
| 진행 단계 | 실행 결과 및 설명 | 명령어 |
| --- | --- | --- |
| **hello-world 실행** | `Hello from Docker!` 메시지 출력 확인 (정상 실행 후 자동 종료됨) | `docker run hello-world` |
| **Ubuntu 실행 및 진입** | 프롬프트가 `root@<컨테이너ID>:/#` 형태로 변경되며 컨테이너 내부로 진입 성공 | `docker run -it ubuntu bash` |
| **내부 파일 목록 확인** | `<bin, boot, dev, etc, home, lib, usr, var 등의 디렉토리 확인>` | `ls` |
| **내부 텍스트 출력** | `Hello Ubuntu!` 출력 확인 | `echo "Hello Ubuntu!"` |
| **컨테이너 빠져나오기** | 컨테이너가 종료되며 다시 Mac 터미널로 복귀함 | `exit` |

### 6-2. 컨테이너 종료와 유지 (attach vs exec) 차이점 정리
* **`docker run -it` (또는 `attach`)**: 
  컨테이너의 메인 프로세스에 직접 접속하는 방식이다. 작업 후 `exit` 명령어로 빠져나오면 메인 프로세스가 종료되므로 **컨테이너 자체도 함께 종료(Stop)** 된다.
* **`docker exec -it`**: 
  **이미 백그라운드에서 실행 중인 컨테이너**에 새로운 터미널을 열어 접속하는 방식이다. 작업 후 `exit`로 빠져나와도 내가 접속했던 터미널만 닫힐 뿐, **컨테이너는 종료되지 않고 계속 실행(유지)** 된다.

### 환경 확인 로그
```bash
dlckdwls763222@c4r8s5 docker-study % docker run hello-world 

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

dlckdwls763222@c4r8s5 docker-study % docker run -it ubuntu bash
Unable to find image 'ubuntu:latest' locally
Blatest: Pulling from library/ubuntu
ed819469700f: Pull complete 
a3679419df18: Pull complete 
Digest: sha256:3131b4cc82a783df6c9df078f86e01819a13594b865c2cad47bd1bca2b7063bb
Status: Downloaded newer image for ubuntu:latest
root@ad52d768be9d:/# 
root@ad52d768be9d:/# ls
bin  boot  dev  etc  home  lib  lib64  media  mnt  opt  proc  root  run  sbin  srv  sys  tmp  usr  var
root@ad52d768be9d:/# echo "Hello Ubuntu!"
Hello Ubuntu!
root@ad52d768be9d:/# exit
exit
dlckdwls763222@c4r8s5 docker-study % 
```
---

# 7. 기존 Dockerfile 기반 커스텀 이미지 제작

기본 Nginx 웹 서버의 메인 화면을 나만의 정적 웹페이지로 교체하는 커스텀 이미지를 제작.

### 7-1. 베이스 이미지 및 커스텀 포인트
* **선택한 베이스 이미지:** `nginx:latest` (공식 Nginx 웹 서버 이미지)
* **커스텀 포인트 및 목적:** 
  * `COPY index.html /usr/share/nginx/html/index.html`
  * **목적:** Nginx가 기본으로 제공하는 환영 페이지(Welcome to nginx!)를 제거하고, 내가 직접 작성한 커스텀 HTML 파일(`index.html`)로 덮어씌워 나만의 웹 서버 콘텐츠를 서비스하기 위함이다.

### 7-2. 빌드 및 실행 실습 결과

| 진행 단계 | 실행 결과 및 설명 | 명령어 |
| --- | --- | --- |
| **커스텀 HTML 생성** | `index.html` 파일 생성 및 내용 작성 | `echo "<h1>Hello! This is my custom Nginx server!</h1>" > index.html` |
| **Dockerfile 생성** | 베이스 이미지(`FROM`)와 파일 복사(`COPY`) 명령어가 포함된 Dockerfile 작성 | `cat <<EOF > Dockerfile`<br>`FROM nginx:latest`<br>`COPY index.html /usr/share/nginx/html/index.html`<br>`EOF` |
| **커스텀 이미지 빌드** | `my-custom-nginx`라는 이름의 새로운 이미지 생성 성공 | `docker build -t my-custom-nginx .` |
| **컨테이너 실행** | 호스트의 8081 포트와 컨테이너의 80 포트를 연결하여 백그라운드 실행 | `docker run -d -p 8081:80 --name my-custom-web my-custom-nginx` |
| **웹 브라우저 확인** | `http://localhost:8081` 접속 시 "Hello! This is my custom Nginx server!" 출력 확인 | (웹 브라우저에서 확인) |

### 환경 확인 로그
```bash
dlckdwls763222@c4r8s5 docker-study % echo '<h1>Hello! This is my custom Nginx server!</h1>' > index.html
dlckdwls763222@c4r8s5 docker-study % echo "FROM nginx:latest" > Dockerfile
dlckdwls763222@c4r8s5 docker-study % cat Dockerfile
FROM nginx:latest
COPY index.html /usr/share/nginx/html/index.html
dlckdwls763222@c4r8s5 docker-study % docker build -t my-custom-nginx .
[+] Building 2.1s (7/7) FINISHED                                                                                                                            docker:orbstack
 => [internal] load build definition from Dockerfile                                                                                                                   0.2s
 => => transferring dockerfile: 104B                                                                                                                                   0.0s
 => [internal] load metadata for docker.io/library/nginx:latest                                                                                                        0.0s
 => [internal] load .dockerignore                                                                                                                                      0.2s
 => => transferring context: 2B                                                                                                                                        0.0s
 => [internal] load build context                                                                                                                                      0.3s
 => => transferring context: 85B                                                                                                                                       0.0s
 => [1/2] FROM docker.io/library/nginx:latest                                                                                                                          0.9s
 => [2/2] COPY index.html /usr/share/nginx/html/index.html                                                                                                             0.2s
 => exporting to image                                                                                                                                                 0.2s
 => => exporting layers                                                                                                                                                0.1s
 => => writing image sha256:7bf9ada4ed2cc5b4c1560fd3ccd09c244fed6a2182f75a12c35b543eb24bc80f                                                                           0.0s
 => => naming to docker.io/library/my-custom-nginx     

dlckdwls763222@c4r8s5 docker-study % docker run -d -p 8081:80 --name my-custom-web my-custom-nginx
8cc13507394264885ded2e69c04966f7fb5a8ac6e39b44cce04996d1451ed530
---
```
---
# 8. 포트 매핑 및 접속 증거

### 8-1. 포트 매핑 설정
* **사용된 옵션:** `-p 8081:80`
* **설명:** 호스트(내 Mac)의 `8081` 포트로 들어오는 통신 요청을 컨테이너 내부의 Nginx 웹 서버 기본 포트인 `80` 포트로 전달(매핑)하도록 설정했습니다.

### 8-2. 접속 증거 (웹 브라우저 화면)
* 웹 브라우저에서 `http://localhost:8081`에 접속하여 커스텀 HTML 페이지가 정상적으로 출력되는 것을 확인했습니다.

### 환경 확인 로그
```bash

dlckdwls763222@c4r8s5 docker-study % curl http://localhost:8081
<h1>Hello! This is my custom Nginx server!</h1>

```
---

# 9. Docker 볼륨 영속성 검증

1. 도커의 치명적인 약점: "PC방 컴퓨터"
도커 컨테이너는 기본적으로 **'일회용 컴퓨터(PC방 컴퓨터)'**와 같습니다.
우리가 PC방 컴퓨터 바탕화면에 중요한 과제 파일을 저장해 둬도, 컴퓨터를 껐다 켜면 싹 다 날아가고 초기화되죠?

도커 컨테이너도 똑같습니다. 컨테이너 안에서 데이터베이스를 만들고, 파일을 저장해 둬도 컨테이너를 삭제(rm)하는 순간 그 안의 모든 데이터는 영원히 사라집니다.

2. 해결책: "도커 볼륨(Volume) = 외장 하드(USB)"
이 문제를 해결하기 위해 도커가 만든 기능이 바로 **'볼륨(Volume)'**입니다.
볼륨은 컨테이너라는 컴퓨터에 꽂아서 쓸 수 있는 **'아주 튼튼한 외장 하드(또는 USB)'**라고 생각하시면 완벽합니다.

PC방 컴퓨터(컨테이너)가 초기화되거나 부서져도, 내 주머니 속에 있는 USB(볼륨)는 안전하겠죠?
내일 다른 PC방 컴퓨터(새로운 컨테이너)에 가서 그 USB를 꽂으면, 어제 하던 과제를 그대로 이어서 할 수 있습니다.
3. 영속성(Persistence)이란?
과제 제목에 있는 **'영속성'**은 한자어로 "영원히 계속된다"는 뜻입니다. IT 용어로는 **"프로그램이 종료되거나 컴퓨터가 꺼져도 데이터가 날아가지 않고 유지되는 성질"**을 말합니다.

즉, 이번 9번 과제의 목표는 **"컨테이너(컴퓨터)를 삭제해도, 볼륨(USB)에 저장된 데이터는 날아가지 않고 살아남는다(영속성)는 것을 교수님께 증명해라!"**라는 뜻입니다.

💡 9번 과제에서 우리가 할 일 (시나리오)
우리는 방금 배운 개념을 바탕으로 딱 4단계의 연극을 할 겁니다.

USB 사기: my-data라는 이름의 도커 볼륨(USB)을 하나 만듭니다.
A 컴퓨터에서 작업하기: container-A를 만들고 USB를 꽂은 뒤, 그 안에 "이 데이터는 살아남을 것이다!"라는 메모를 적습니다.
A 컴퓨터 박살 내기 💥: container-A를 무자비하게 삭제해 버립니다. (데이터가 날아갔을까 조마조마한 척합니다.)
B 컴퓨터에서 확인하기: 완전히 새로운 container-B를 만들고 아까 그 USB를 꽂아봅니다. 메모가 그대로 남아있는 것을 확인하고 환호합니다! 🎉

### 9-1. 검증 목적
컨테이너가 삭제되더라도 데이터가 유실되지 않고 유지(영속성)되는지 Docker Volume을 통해 검증합니다.

### 9-2. 검증 절차 및 결과

**1) 볼륨 생성**
* **명령어:** `docker volume create mt-data`
* **출력:** `my-data`

**2) 첫 번째 컨테이너 생성 및 데이터 쓰기**
* `my-data` 볼륨을 `/workspace` 경로에 마운트하여 컨테이너를 실행하고 텍스트 파일을 생성했습니다.
* **명령어:** 
  `docker run -it --name container-A -v my-data:/workspace ubuntu`
  `echo "This data will survive!" > /workspace/test.txt`

**3) 컨테이너 삭제**
* 데이터를 작성한 첫 번째 컨테이너를 완전히 삭제했습니다.
* **명령어:** `docker rm container-A`

**4) 새로운 컨테이너로 데이터 유지 확인 (영속성 증명)**
* 동일한 볼륨(`my-data`)을 새로운 컨테이너(`container-B`)에 연결하여 실행한 후, 이전 컨테이너에서 작성한 파일이 존재하는지 확인했습니다.
* **명령어:** 
  `docker run -it --name container-B -v my-data:/workspace ubuntu`
  `cat /workspace/test.txt`
* **출력 결과:** `This data will survive!`

### 9-3. 결론
컨테이너(`container-A`)를 삭제했음에도 불구하고, Docker Volume(`my-data`)에 저장된 파일은 새로운 컨테이너(`container-B`)에서 그대로 조회되었습니다. 이를 통해 **볼륨을 활용하면 컨테이너의 생명주기와 독립적으로 데이터를 안전하게 영구 보존할 수 있음**을 증명했습니다.

### 환경 확인 로그
```bash
dlckdwls763222@c4r8s5 docker-study % docker volume create mt-data
mt-data
dlckdwls763222@c4r8s5 docker-study % docker run -it --name container-A -v my-data:/workspace ubuntu
root@a5919ebb113e:/# 
root@a5919ebb113e:/# echo "This data will survive!" > /workspace/test.txt
root@a5919ebb113e:/# cat /workspace/test.txt
This data will survive!
root@a5919ebb113e:/# exit
exit
dlckdwls763222@c4r8s5 docker-study % docker rm container-A
container-A
dlckdwls763222@c4r8s5 docker-study % docker run -it --name container-B -v my-data:/workspace ubuntu
root@28b4ba610f1e:/# cat /workspace/test.txt
This data will survive!
root@28b4ba610f1e:/# exit
exit
```
---
# 10. Git 과 GitHub란?
 1. 개념 
Git (깃) 게임에서 '저장(Save)'을 하듯이
Commit (커밋) 
GitHub (깃허브) = 개발자용 구글 드라이브
내 맥북(로컬)에만 있는 타임머신 기록을 인터넷(클라우드)에 안전하게 백업하고, 다른 사람에게 보여줄 수 있는 웹사이트입니다.
Push (푸시) = 밀어 올리기 (업로드)
내 맥북에 저장된 사진(커밋)들을 인터넷(GitHub)으로 쭈욱 밀어 올리는 행위입니다.
요약: 맥북에서 Git으로 사진(Commit)을 찍고, 그 사진들을 GitHub로 업로드(Push)하는 과정입니다!


---
# 3. 수행항목 체크리스트

본 미션에서 달성해야 할 핵심 학습 항목과 현재 진행 상태입니다.

| 상태 | 학습 항목 | 핵심 개념 | 달성 목표 |
| :---: | :--- | :--- | :--- |
| ✅ | **터미널 (Terminal)** | CLI(명령줄) 기반으로 컴퓨터와 소통하는 창구 | `cd`, `ls`, `mkdir` 등 기본 리눅스 명령어 숙지 |
| ✅  | **권한 (Permission)** | 파일/폴더를 읽고(r), 쓰고(w), 실행(x)할 수 있는 권한 | `chmod`, `sudo`의 필요성 이해 및 권한 오류 해결 |
| ✅ | **Git / GitHub** | 코드 버전 관리(Git) 및 원격 저장소(GitHub) | `commit`, `push`를 통한 코드 백업 및 협업 환경 구축 |
| ✅ | **Docker (도커)** | 환경에 구애받지 않는 격리된 컨테이너 실행 환경 | 이미지와 컨테이너의 차이 이해, `docker run` 실행 |
| ✅ | **포트 (Port)** | 호스트(내 PC)와 컨테이너를 연결하는 통신 출입구 | 포트 포워딩(`-p`)을 통해 웹 브라우저로 컨테이너 접속 |
| ✅  | **마운트 (Bind Mount)**| 내 PC의 폴더를 컨테이너 내부와 실시간으로 연결 | 로컬에서 수정한 코드를 컨테이너 재시작 없이 즉시 반영 |
| ✅  | **볼륨 (Volume)** | 도커가 직접 관리하는 안전한 데이터 저장 공간 | 컨테이너가 삭제되어도 DB 등의 데이터가 영구 보존되도록 설정 |
| ✅  | **Dockerfile** | 나만의 도커 이미지를 만들기 위한 설정(레시피) 파일 | `docker build`를 통해 내 코드가 담긴 커스텀 이미지 생성 |

> **💡 진행 상태 표시**
> - ✅ : 완료 (환경 설정 및 개념 이해 완료)
> - ⬜️ : 진행 예정 (앞으로 학습 및 실습할 내용)

