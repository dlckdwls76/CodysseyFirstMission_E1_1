# 개발 워크스테이션 구축
---

# 1. 미션 소개 (요약)
 이 미션은 개발을 시작하기 전에 기본환경인 리눅스 CLI환경,Docker, Git/GitHub를 직접 세팅하고 활용해 보는 것을 목표
 터미널을 통해 작업 디렉토리와 권한을 관리하고, Docker를 이용해 컨테이너를 실행 및 관리한다.
이 과정에서 이미지와 컨테이너의 차이, 격리된 실행환경, 포트 및 스토리지 연결 방식 등 Docker의 핵심 개념을 이해한다. 최종적으로는 "내 컴퓨터에서만 실행되는 환경"이 아니라, 팀원 누구나 같은 방식으로 실행할 수 있는 개발 환경을 만드는 것을 경험한다.

---
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

[실행 환경 확인 로그 보기](./images/environment-check2.png)

---

# 3. 연습용 폴더 만들기 & 터미널 기본 명령어 및 파일 조작 실습

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
$ CodysseyFirstMission_E1_1 % pwd
/Users/dlckdwls763222/CodysseyFirstMission_E1_1
$ CodysseyFirstMission_E1_1 % mkdir docker-study
$ CodysseyFirstMission_E1_1 % cd docker-study
$ docker-study % ls -al
total 0
drwxr-xr-x  2 dlckdwls763222  dlckdwls763222   64  7 30 18:16 .
drwxr-xr-x  7 dlckdwls763222  dlckdwls763222  224  7 30 18:16 ..
$ docker-study % touch test.txt
$ docker-study % cat test.txt
코디세이 2기생 이창진%                                                                                                                
$ docker-study % cp test.txt copy.txt
$ docker-study % mv copy.txt hello.txt

```
### 실행 증거
[실행 환경 확인 로그 보기](./images/study-terminal.png)

절대 경로 (Absolute Path)
개념: 변하지 않는 **'전체 도로명 주소'**입니다.
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
$ % mkdir perm_dir
$ % touch perm_file.txt
$ % ls -al
total 56
drwxr-xr-x   9 dlckdwls763222  dlckdwls763222    288  7 30 18:30 .
drwxr-x---+ 21 dlckdwls763222  dlckdwls763222    672  7 30 18:00 ..
drwxr-xr-x  16 dlckdwls763222  dlckdwls763222    512  7 30 18:16 .git
drwxr-xr-x   4 dlckdwls763222  dlckdwls763222    128  7 30 18:29 docker-study
drwxr-xr-x   5 dlckdwls763222  dlckdwls763222    160  7 30 18:19 images
drwxr-xr-x   4 dlckdwls763222  dlckdwls763222    128  7 30 15:34 missionscreenshots
drwxr-xr-x   2 dlckdwls763222  dlckdwls763222     64  7 30 18:30 perm_dir
-rw-r--r--   1 dlckdwls763222  dlckdwls763222      0  7 30 18:30 perm_file.txt
-rw-r--r--   1 dlckdwls763222  dlckdwls763222  28256  7 30 18:28 README.md
$ CodysseyFirstMission_E1_1 % chmod 777 perm_file.txt
$ CodysseyFirstMission_E1_1 % chmod 700 perm_dir
$ CodysseyFirstMission_E1_1 % ls -al 
total 56
drwxr-xr-x   9 dlckdwls763222  dlckdwls763222    288  7 30 18:30 .
drwxr-x---+ 21 dlckdwls763222  dlckdwls763222    672  7 30 18:00 ..
drwxr-xr-x  16 dlckdwls763222  dlckdwls763222    512  7 30 18:16 .git
drwxr-xr-x   4 dlckdwls763222  dlckdwls763222    128  7 30 18:29 docker-study
drwxr-xr-x   5 dlckdwls763222  dlckdwls763222    160  7 30 18:19 images
drwxr-xr-x   4 dlckdwls763222  dlckdwls763222    128  7 30 15:34 missionscreenshots
drwx------   2 dlckdwls763222  dlckdwls763222     64  7 30 18:30 perm_dir
-rwxrwxrwx   1 dlckdwls763222  dlckdwls763222      0  7 30 18:30 perm_file.txt
-rw-r--r--   1 dlckdwls763222  dlckdwls763222  28256  7 30 18:28 README.md
```
### 실행 증거
[실행 환경 확인 로그 보기](./images/permission-check.png)

---

# 5. Docker 설치 및 기본 운영 명령 실습
5-1. 도커의 핵심 개념 (이미지와 컨테이너)
이미지 (Image)
프로그램을 실행하는 데 필요한 모든 것(OS, 설정, 코드)이 담겨 있는 변하지 않는 원본 파일

컨테이너 (Container) 
이미지를 바탕으로 실제로 메모리에 올라가 실행된 상태를 말한다. 하나의 이미지로 여러 개의 컨테이너를 독립적으로 실행할 수 있음

포트 매핑 (Port Mapping) = "-p 8080:80"
내 컴퓨터(Mac)의 특정 포트(예: 8080)로 들어오는 접속을, 도커 컨테이너 내부의 포트(예: 80)로 연결해주는 작업

5-2. Docker 설치 및 기본 점검
| 진행 단계 | 실행 결과 및 설명 | 명령어 |
| --- | --- | --- |
| **Docker 버전 확인** | 설치된 Docker 버전과 빌드 정보 확인 | `docker --version` |
| **Docker 데몬 확인** | Docker 클라이언트, 서버, 이미지 및 컨테이너 상태 확인 | `docker info` |
| **이미지 다운로드** | Docker Hub에서 Nginx Alpine 이미지 다운로드 | `docker pull nginx:alpine` |
| **이미지 목록 확인** | 내려받은 이미지의 이름, 태그, ID 및 크기 확인 | `docker images` |
| **컨테이너 실행** | Nginx 컨테이너를 백그라운드로 실행하고 `8080:80` 포트 연결 | `docker run -d --name docker-ops-test -p 8080:80 nginx:alpine` |
| **실행 목록 확인** | 현재 실행 중인 컨테이너 확인 | `docker ps` |
| **컨테이너 로그 확인** | Nginx 컨테이너가 실행되면서 남긴 시스템 로그 및 접속 기록 확인| `docker logs docker-ops-test`|
| **컨테이너 리소스 확인** | 컨테이너가 사용 중인 CPU, 메모리(MEM USAGE), 네트워크 I/O 실시간 확인 | `docker stats --no-stream` |
| **컨테이너 중지** | 실행 중인 컨테이너 중지 | `docker stop docker-ops-test` |
| **전체 목록 확인** | 종료된 컨테이너를 포함한 전체 목록 확인 | `docker ps -a` |

docker.io/library/nginx:alpine
각 부분의 의미는:
docker.io: Docker Hub
library/nginx: Docker 공식 Nginx 이미지
alpine: Alpine Linux 기반 버전

Docker Hub → Docker 엔진 → Docker 전용 내부 저장공간

Dockerfile 작성
      ↓ docker build
이미지 생성
      ↓ docker run
컨테이너 실행
      ↓
로그·상태·리소스 확인

### 환경 확인 로그
```bash
$ % docker --version
Docker version 28.5.2, build ecc6942
$ % docker info
$ % docker pull nginx:alpine
$ % docker images
$ % docker run -d --name docker-ops-test -p 8080:80 nginx:alpine
$ % docker ps
$ % docker logs my-nginx
$ % docker stats --no-stream
$ % docker stop my-nginx
$ % docker ps -a
 
```
### 실행 증거
[실행 환경 확인 로그 보기](./images/docker-version.png)

[실행 환경 확인 로그 보기](./images/docker-info.png)

[실행 환경 확인 로그 보기](./images/docker-pull-nginx.png)

[실행 환경 확인 로그 보기](./images/docker-images.png)

[실행 환경 확인 로그 보기](./images/docker-run.png)

[실행 환경 확인 로그 보기](./images/docker-ps.png)

[실행 환경 확인 로그 보기](./images/docker-my-nginx.png)

[실행 환경 확인 로그 보기](./images/docker-no-stream.png)

[실행 환경 확인 로그 보기](./images/docker-stop.png)

[실행 환경 확인 로그 보기](./images/docker-ps-a.png)

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


### 환경 확인 로그
```bash
$ % docker run hello-world 
$ % docker run -it ubuntu bash  # it은 사용자가 터미널을 통해 컨테이너와 상호작용 할 수 있도록 함.
$ % root@230e3f2c8909:/# ls
$ % root@230e3f2c8909:/# ehco "Hello Ubuntu"
$ % root@230e3f2c8909:/# exit
```
[실행 환경 확인 로그 보기](./images/docker-run-helloworld.png)

[실행 환경 확인 로그 보기](./images/docker-run-it-ubuntu-bash.png)

[실행 환경 확인 로그 보기](./images/ls-echo-exit.png)

### 6-2. 컨테이너 종료와 유지 (attach vs exec) 차이점 정리
* **`docker run -it` (또는 `attach`)**: 
  컨테이너의 메인 프로세스에 직접 접속하는 방식이다. 작업 후 `exit` 명령어로 빠져나오면 메인 프로세스가 종료되므로 **컨테이너 자체도 함께 종료(Stop)** 된다.
* **`docker exec -it`**: 
  **이미 백그라운드에서 실행 중인 컨테이너**에 새로운 터미널을 열어 접속하는 방식이다. 작업 후 `exit`로 빠져나와도 내가 접속했던 터미널만 닫힐 뿐, **컨테이너는 종료되지 않고 계속 실행(유지)** 된다.

---

# 7. 기존 Dockerfile 기반 커스텀 이미지 제작

기본 Nginx 웹 서버의 메인 화면을 나만의 정적 웹페이지로 교체하는 커스텀 이미지를 제작.

### 7-1. 빌드 및 실행 실습 결과

| **커스텀 HTML 생성** | `index.html` 파일 생성 및 내용 작성 | `touch index.html` (또는 VS Code 사용) |
| **Dockerfile 생성** | 베이스 이미지(`FROM`)와 파일 복사(`COPY`) 명령어가 포함된 Dockerfile 작성 | `touch Dockerfile` (또는 VS Code 사용) |
| **커스텀 이미지 빌드** | `my-custom-nginx`라는 이름의 새로운 이미지 생성 성공 | `docker build -t my-custom-nginx .` |
| **컨테이너 실행** | 호스트의 8081 포트와 컨테이너의 80 포트를 연결하여 백그라운드 실행 | `docker run -d -p 8081:80 --name my-custom-web my-custom-nginx` |
| **웹 브라우저 확인** | `http://localhost:8081` 접속 시 커스텀 페이지 출력 확인 | (웹 브라우저에서 접속) |미지가 실행되었습니다!" 출력 확인 | (웹 브라우저에서 확인) |

### 환경 확인 로그
**[Dockerfile 작성 내용]**
```dockerfile
FROM nginx:alpine
COPY index.html /usr/share/nginx/html/index.html

```
[실행 환경 확인 로그 보기](./images/touch-Dokerfile.png)

[실행 환경 확인 로그 보기](./images/docker-build.png)

[실행 환경 확인 로그 보기](./images/docker8081custom-web.png)

[실행 환경 확인 로그 보기](./images/localhost8081.png)
---
# 8. 포트 매핑 및 접속 증거

### 8-1. 접속 증거 (웹 브라우저 화면)
* 웹 브라우저에서 `http://localhost:8081`에 접속하여 커스텀 HTML 페이지가 정상적으로 출력되는 것을 확인했습니다.

### 환경 확인 로그
```bash
$ % curl http://localhost:8081
```
[실행 환경 확인 로그 보기](./images/curl-domain.png)

[실행 환경 확인 로그 보기](./images/localhost8081.png)

---

# 9. Docker 볼륨 영속성 검증

Docker 볼륨을 생성하여 컨테이너에 연결하고, 컨테이너가 삭제되더라도 데이터가 유지되는지(영속성)를 검증합니다.

### 9-1. 볼륨 생성 및 데이터 영속성 검증 절차

| 진행 단계 | 실행 결과 및 설명 | 명령어 |
| --- | --- | --- |
| **볼륨 생성** | `my-data-vol` 이라는 이름의 도커 볼륨 생성 | `docker volume create my-data-vol` |
| **컨테이너 A 실행** | 볼륨을 `/app` 경로에 마운트하여 컨테이너 실행 | `docker run -d --name container-A -v my-data-vol:/app ubuntu sleep 1000` |
| **데이터 생성** | 컨테이너 A의 `/app/test.txt` 경로에 테스트 문자열 작성 | `docker exec container-A sh -c "echo 'Hello, Docker Volume!' > /app/test.txt"` |
| **컨테이너 A 삭제** | 데이터를 작성한 컨테이너 A를 강제 삭제 | `docker rm -f container-A` |
| **컨테이너 B 실행** | 동일한 볼륨을 새로운 컨테이너 B의 `/app` 경로에 마운트 | `docker run -d --name container-B -v my-data-vol:/app ubuntu sleep 1000` |
| **데이터 검증** | 컨테이너 B에서 `test.txt` 파일을 읽어 데이터가 유지됨을 확인 | `docker exec container-B cat /app/test.txt` |

### 9-2. 환경 확인 로그 및 캡처

**[실행 및 검증 로그]**
```bash
$ docker volume create my-data-vol
my-data-vol

$ docker run -d --name container-A -v my-data-vol:/app ubuntu sleep 1000

$ docker exec container-A sh -c "echo 'Hello, Docker Volume!' > /app/test.txt"

$ docker rm -f container-A
container-A

$ docker run -d --name container-B -v my-data-vol:/app ubuntu sleep 1000

$ docker exec container-B cat /app/test.txt
Hello, Docker Volume!
```

[실행 환경 확인 로그 보기](./images/docker-vol.png)

[실행 환경 확인 로그 보기](./images/docker-volume.png)

---

# 10. Git 설정 및 GitHub 연동

버전 관리를 위한 Git 환경 설정을 진행하고, 로컬 저장소와 원격 저장소(GitHub)의 연동을 확인합니다.

### 10-1. Git 설정 및 연동 진행 결과

| 진행 단계 | 실행 결과 및 설명 | 명령어 |
| --- | --- | --- |
| **사용자 정보 설정** | Git 커밋에 기록될 사용자 이름과 이메일 설정 | `git config --global user.name "이름"`<br>`git config --global user.email "이메일"` |
| **기본 브랜치 설정** | Git 초기화 시 생성되는 기본 브랜치명을 `main`으로 설정 | `git config --global init.defaultBranch main` |
| **설정 내역 확인** | 적용된 Git 글로벌 설정 리스트 출력 및 확인 | `git config --list` |
| **원격 저장소 연동** | 로컬 저장소와 GitHub 원격 저장소(`origin`) 연결 | `git remote add origin <저장소 주소>` |
| **연동 상태 확인** | 연결된 원격 저장소의 URL(fetch/push) 확인 | `git remote -v` |

### 10-2. 환경 확인 로그 및 캡처

**[Git 설정 확인 로그]**
```bash
$ git config --list

```
[실행 환경 확인 로그 보기](./images/gitsetting.png)

---

# 11. 수행항목 체크리스트

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

