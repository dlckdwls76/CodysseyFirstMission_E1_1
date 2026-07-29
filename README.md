
# 개발 워크스테이션 구축

## 1. 프로젝트 개요

이 프로젝트는 macOS에서 터미널, Docker, Git/GitHub를 직접 사용해 재현 가능한 개발 환경을 구성한 과정을 기록한다.

주요 목표는 다음과 같다.

- 터미널에서 파일과 디렉토리를 생성·이동·복사·삭제한다.
- 파일과 디렉토리의 권한을 확인하고 변경한다.
- OrbStack의 Docker 엔진을 터미널의 Docker CLI로 제어한다.
- Docker 이미지와 컨테이너의 차이를 이해한다.
- NGINX 기반 커스텀 이미지를 직접 빌드한다.
- 포트 매핑, 바인드 마운트, Docker 볼륨의 동작을 확인한다.
- Git으로 변경 이력을 관리하고 GitHub 저장소에 공유한다.

서울캠퍼스 환경에서는 `sudo` 사용이 제한될 수 있으므로 OrbStack을 Docker 실행 환경으로 사용했다. 명령어 입력은 macOS의 터미널에서 수행하고, 컨테이너 실행은 OrbStack의 Docker 엔진이 담당한다.

```text
macOS 터미널 → Docker CLI → OrbStack Docker 엔진 → Docker 컨테이너
```

---

## 2. 실행 환경

| 항목 | 환경 | 확인 명령 |
| --- | --- | --- |
| OS | macOS 15.7.4 (Build 24G517) | `sw_vers` |
| Shell | zsh (`/bin/zsh`) | `echo $SHELL` |
| 터미널 | VS Code 통합 터미널 | `echo $TERM_PROGRAM` |
| Docker 실행 환경 | OrbStack | `docker info` |
| Docker Engine | 28.5.2 (`docker info` 기록 기준) | `docker --version`, `docker info` |
| Git | 2.53.0 | `git --version` |
| 기본 브랜치 | `main` | `git branch --show-current` |

환경 확인 결과:

```text
$ sw_vers
ProductName:            macOS
ProductVersion:         15.7.4
BuildVersion:           24G517

$ echo $SHELL
/bin/zsh

$ git --version
git version 2.53.0

$ docker info
Context: orbstack
Server Version: 28.5.2
Operating System: OrbStack
OSType: linux
Architecture: x86_64
```

> `docker --version`과 `docker info`는 최종 제출 직전에 같은 터미널에서 다시 실행해 최신 값을 기록한다.

---

## 3. 저장소 구조

```text
CodysseyFirstMission_E1_1/
├── README.md
├── docker-study/
│   ├── Dockerfile
│   ├── index.html
│   └── test.txt
└── perm_dir/
    └── perm_file.txt
```

주요 결과물:

- [Dockerfile](docker-study/Dockerfile)
- [커스텀 웹 페이지](docker-study/index.html)
- [터미널 실습 파일](docker-study/test.txt)
- [권한 실습 파일](perm_dir/perm_file.txt)

---

## 4. 수행 체크리스트

- [x] 실행 환경 확인
- [x] 터미널 기본 조작
- [x] 절대 경로와 상대 경로 확인
- [x] 파일 권한 변경
- [x] 디렉토리 권한 변경
- [x] Docker 엔진 점검
- [x] `hello-world` 실행
- [x] Ubuntu 컨테이너 진입
- [x] NGINX 컨테이너 실행·로그·중지·삭제
- [x] Dockerfile 기반 커스텀 이미지 빌드
- [x] 포트 매핑 및 `curl` 접속 확인
- [x] Docker 볼륨 영속성 확인
- [x] GitHub 원격 저장소 연결
- [ ] `docker images`, `docker stats --no-stream` 최종 출력 추가
- [ ] 바인드 마운트 변경 전·후 출력 추가
- [ ] 포트가 보이는 브라우저 접속 화면 추가
- [ ] `git config --list` 및 VS Code GitHub 연동 증거 추가

체크되지 않은 항목은 재현 명령을 아래에 기록했으며, 최종 제출 전에 실제 출력 또는 스크린샷을 추가한다.

---

## 5. 터미널 기본 조작

### 5-1. 사용한 명령

| 작업 | 명령 | 설명 |
| --- | --- | --- |
| 현재 위치 확인 | `pwd` | 현재 작업 디렉토리의 절대 경로 출력 |
| 숨김 파일 포함 목록 | `ls -la` | 권한·소유자·크기·숨김 파일 확인 |
| 디렉토리 생성 | `mkdir docker-study` | 연습용 디렉토리 생성 |
| 디렉토리 이동 | `cd docker-study` | 연습용 디렉토리로 이동 |
| 빈 파일 생성 | `touch test.txt` | 빈 파일 생성 |
| 내용 확인 | `cat test.txt` | 텍스트 파일 내용 출력 |
| 파일 복사 | `cp test.txt copy.txt` | 같은 내용의 파일 복사 |
| 이름 변경 | `mv copy.txt hello.txt` | 파일 이름 변경 |
| 파일 삭제 | `rm hello.txt` | 지정한 파일 삭제 |

### 5-2. 실행 로그

```text
$ pwd
/Users/[사용자명]/CodysseyFirstMission_E1_1

$ mkdir docker-study

$ cd docker-study

$ pwd
/Users/[사용자명]/CodysseyFirstMission_E1_1/docker-study

$ touch test.txt

$ ls -la
-rw-r--r--  1 [사용자명]  [그룹명]  3 Jul 29 18:18 test.txt

$ cat test.txt
123

$ cp test.txt copy.txt
$ mv copy.txt hello.txt
$ rm hello.txt
```

### 5-3. 절대 경로와 상대 경로

- 절대 경로는 `/`부터 시작하는 전체 경로다.
- 상대 경로는 현재 위치를 기준으로 작성하는 경로다.

예시:

```text
절대 경로: /Users/[사용자명]/CodysseyFirstMission_E1_1/docker-study
상대 경로: docker-study
상위 경로: ..
현재 경로: .
```

---

## 6. 파일 및 디렉토리 권한

### 6-1. 권한 의미

파일과 디렉토리의 권한은 소유자, 그룹, 기타 사용자 순서로 표시한다.

| 기호 | 숫자 | 파일 | 디렉토리 |
| --- | ---: | --- | --- |
| `r` | 4 | 내용 읽기 | 목록 확인 |
| `w` | 2 | 내용 수정 | 항목 생성·삭제 |
| `x` | 1 | 파일 실행 | 디렉토리 진입·탐색 |
| `-` | 0 | 권한 없음 | 권한 없음 |

숫자 권한 예시:

- `755`: 소유자는 읽기·쓰기·실행, 그룹과 기타 사용자는 읽기·실행
- `644`: 소유자는 읽기·쓰기, 그룹과 기타 사용자는 읽기
- `700`: 소유자만 읽기·쓰기·실행

### 6-2. 권한 변경 로그

```text
$ touch perm_file.txt
$ mkdir perm_dir

$ ls -la
drwxr-xr-x  perm_dir
-rw-r--r--  perm_file.txt

$ chmod 777 perm_file.txt
$ chmod 700 perm_dir

$ ls -la
drwx------  perm_dir
-rwxrwxrwx  perm_file.txt
```

권한 실습은 권한 표기의 변화를 확인하기 위한 것이다. 일반 텍스트 파일에는 실행 권한이 필요하지 않으므로 실제 프로젝트에서는 보통 `644`를 사용한다.

---

## 7. Docker 설치 및 엔진 점검

OrbStack을 실행한 뒤 다음 명령으로 Docker CLI와 Docker 엔진을 확인했다.

```bash
docker --version
docker info
```

성공 기준:

- `docker --version`에서 버전이 출력된다.
- `docker info`에서 `Client`와 `Server` 정보가 모두 출력된다.
- `Context`가 `orbstack`으로 표시된다.
- Docker 데몬 연결 오류가 발생하지 않는다.

최종 제출 전 추가할 운영 명령:

```bash
docker images
docker ps
docker ps -a
docker stats --no-stream
```

`docker stats`는 계속 갱신되는 명령이므로 제출 로그에는 한 번만 출력하는 `--no-stream` 옵션을 사용한다.

---

## 8. Docker 기본 운영 및 NGINX 컨테이너

### 8-1. hello-world

```text
$ docker run hello-world

Hello from Docker!
This message shows that your installation appears to be working correctly.
```

이 결과로 Docker CLI가 Docker 엔진에 연결되고, 이미지 다운로드·컨테이너 생성·실행·출력 전달이 정상적으로 동작함을 확인했다.

종료된 컨테이너 확인:

```text
$ docker ps -a
CONTAINER ID   IMAGE         STATUS
767c627c402d   hello-world   Exited (0)
```

### 8-2. NGINX 실행

```bash
docker run -d -p 8080:80 --name my-web nginx
docker ps
docker logs my-web
docker stop my-web
docker rm my-web
```

실행 결과:

```text
$ docker ps
CONTAINER ID   IMAGE   STATUS   PORTS                                     NAMES
3bbae6d4e2c5   nginx   Up       0.0.0.0:8080->80/tcp, [::]:8080->80/tcp   my-web
```

`docker logs my-web`에서 NGINX의 `Configuration complete; ready for start up` 메시지를 확인했다. 브라우저의 `/` 요청은 HTTP 200으로 성공했다.

로그에 나타난 `favicon.ico` 404는 브라우저 아이콘 파일이 없다는 뜻이다. 웹 페이지 본문 요청은 200으로 성공했으므로 웹 서버 실행 실패가 아니다.

---

## 9. Ubuntu 컨테이너 실행

Ubuntu 이미지로 대화형 컨테이너를 실행했다.

```text
$ docker run -it ubuntu bash
root@ad52d768be9d:/# ls
bin  boot  dev  etc  home  lib  lib64  media  mnt  opt  proc  root  run  sbin  srv  sys  tmp  usr  var

root@ad52d768be9d:/# echo "Hello Ubuntu!"
Hello Ubuntu!

root@ad52d768be9d:/# exit
exit
```

### attach와 exec 차이

- `docker run -it ubuntu bash` 또는 `docker attach`는 컨테이너의 주 프로세스에 연결한다. 주 프로세스인 셸에서 `exit`하면 컨테이너도 종료될 수 있다.
- `docker exec -it 컨테이너이름 bash`는 실행 중인 컨테이너에 새로운 프로세스를 추가한다. `exec`로 연 셸에서 `exit`해도 기존 주 프로세스가 살아 있으면 컨테이너는 계속 실행된다.

재현 명령:

```bash
docker run -d --name ubuntu-keep ubuntu sleep infinity
docker exec -it ubuntu-keep bash
exit
docker ps
docker rm -f ubuntu-keep
```

---

## 10. Dockerfile 기반 커스텀 이미지

### 10-1. 베이스 이미지

공식 NGINX 이미지인 `nginx:latest`를 베이스 이미지로 선택했다.

### 10-2. 커스텀 포인트

- 기본 NGINX 환영 페이지를 직접 작성한 `index.html`로 교체한다.
- NGINX 기본 웹 문서 경로인 `/usr/share/nginx/html/index.html`로 파일을 복사한다.

`docker-study/Dockerfile`:

```dockerfile
FROM nginx:latest
COPY index.html /usr/share/nginx/html/index.html
```

`docker-study/index.html`:

```html
<h1>Hello! This is my custom Nginx server!</h1>
```

### 10-3. 저장소 최상단에서 빌드

Dockerfile은 `docker-study` 디렉토리에 있으므로 빌드 컨텍스트를 명시한다.

```bash
docker build -t my-custom-nginx:1.0 ./docker-study
```

빌드 결과:

```text
[+] Building 2.1s (7/7) FINISHED
=> [1/2] FROM docker.io/library/nginx:latest
=> [2/2] COPY index.html /usr/share/nginx/html/index.html
=> naming to docker.io/library/my-custom-nginx
```

컨테이너 실행:

```bash
docker run -d -p 8081:80 --name my-custom-web my-custom-nginx:1.0
```

---

## 11. 포트 매핑 및 접속 확인

사용한 포트 매핑:

```text
-p 8081:80
```

- `8081`: macOS 호스트에서 접속하는 포트
- `80`: 컨테이너 내부 NGINX가 사용하는 포트

터미널 접속 결과:

```text
$ curl http://localhost:8081
<h1>Hello! This is my custom Nginx server!</h1>
```

이 결과로 호스트의 8081번 포트가 컨테이너의 80번 포트로 연결되고, 커스텀 HTML이 응답됨을 확인했다.

최종 제출 전에는 주소창의 `http://localhost:8081`과 웹 페이지가 함께 보이도록 브라우저 화면을 캡처해 저장소에 추가한다.

---

## 12. 바인드 마운트 검증 절차

바인드 마운트는 macOS의 실제 디렉토리를 컨테이너 내부 경로에 직접 연결한다. 호스트 파일을 수정하면 이미지를 다시 빌드하지 않아도 컨테이너에서 변경된 파일을 읽을 수 있다.

다음 명령은 저장소 최상단에서 실행한다.

```bash
docker run -d \
  --name bind-web \
  -p 8082:80 \
  -v "$(pwd)/docker-study:/usr/share/nginx/html:ro" \
  nginx
```

변경 전 확인:

```bash
curl http://localhost:8082
```

원본 백업 후 내용 변경:

```bash
cp docker-study/index.html docker-study/index.backup.html
echo '<h1>Bind mount update verified</h1>' > docker-study/index.html
curl http://localhost:8082
```

마운트 정보 확인:

```bash
docker inspect bind-web --format='{{json .Mounts}}'
```

실습 종료 후 원본 복구:

```bash
mv docker-study/index.backup.html docker-study/index.html
docker rm -f bind-web
```

성공 기준은 두 번째 `curl`에서 `Bind mount update verified`가 출력되는 것이다. 최종 제출 전 변경 전·후 출력을 README에 추가한다.

---

## 13. Docker 볼륨 영속성

Docker 볼륨은 Docker가 관리하는 저장 공간이다. 컨테이너가 삭제돼도 볼륨을 삭제하지 않으면 데이터가 유지된다.

### 13-1. 올바른 재현 절차

```bash
docker volume create my-data

docker run -it --name container-A -v my-data:/workspace ubuntu
echo "This data will survive!" > /workspace/test.txt
cat /workspace/test.txt
exit

docker rm container-A

docker run -it --name container-B -v my-data:/workspace ubuntu
cat /workspace/test.txt
exit
```

확인 결과:

```text
This data will survive!
```

첫 번째 컨테이너를 삭제한 뒤 같은 볼륨을 두 번째 컨테이너에 연결했을 때 파일 내용이 유지됐다.

### 13-2. 실습 중 발견한 이름 불일치

최초 실습에서는 실수로 `mt-data`라는 볼륨을 생성한 뒤 컨테이너에는 `my-data`를 연결했다. `-v my-data:/workspace` 실행 시 Docker가 `my-data`를 자동 생성했기 때문에 데이터 유지 확인은 성공했지만, 문서의 재현성을 위해 위 절차에서는 볼륨 이름을 `my-data`로 통일했다.

---

## 14. Git과 GitHub 연동

- Git은 로컬 파일의 변경 이력을 관리하는 버전 관리 도구다.
- Commit은 특정 시점의 변경 내용을 Git 이력으로 저장하는 작업이다.
- GitHub는 Git 저장소를 원격에서 공유하고 협업하는 플랫폼이다.
- Push는 로컬 커밋을 GitHub 원격 저장소로 전송하는 작업이다.

원격 저장소 연결 결과:

```text
$ git remote -v
origin  https://github.com/dlckdwls76/CodysseyFirstMission_E1_1.git (fetch)
origin  https://github.com/dlckdwls76/CodysseyFirstMission_E1_1.git (push)

$ git branch --show-current
main
```

Git 설정 확인 명령:

```bash
git config --global --get user.name
git config --global --get user.email
git config --global --get init.defaultBranch
git config --list
```

보안을 위해 토큰, 비밀번호, 인증 코드, 개인키는 README와 스크린샷에 포함하지 않는다.

---

## 15. 검증 방법 요약

| 검증 대상 | 명령 | 성공 기준 | 결과 위치 |
| --- | --- | --- | --- |
| OS와 Shell | `sw_vers`, `echo $SHELL` | 버전과 `/bin/zsh` 출력 | 2절 |
| Docker 엔진 | `docker info` | Client와 Server 출력 | 7절 |
| 컨테이너 목록 | `docker ps -a` | 실행·종료 컨테이너 표시 | 8절 |
| NGINX 로그 | `docker logs my-web` | NGINX 시작 완료 메시지 | 8절 |
| Ubuntu 진입 | `docker run -it ubuntu bash` | `root@...:/#` 프롬프트 | 9절 |
| 이미지 빌드 | `docker build -t my-custom-nginx:1.0 ./docker-study` | 빌드 완료 | 10절 |
| 포트 매핑 | `curl http://localhost:8081` | 커스텀 HTML 출력 | 11절 |
| 바인드 마운트 | `curl http://localhost:8082` | 호스트 변경 내용 출력 | 12절 |
| 볼륨 영속성 | `cat /workspace/test.txt` | 컨테이너 삭제 후 데이터 출력 | 13절 |
| GitHub 연결 | `git remote -v` | 원격 저장소 주소 출력 | 14절 |

---

## 16. 트러블슈팅

### 16-1. 디렉토리 이름 오타

- 문제: `cd coker-study` 실행 시 디렉토리를 찾지 못했다.
- 원인 가설: 생성한 디렉토리 이름과 입력한 이름이 다르다.
- 확인: `ls -la`로 실제 이름이 `docker-study`임을 확인했다.
- 해결: `cd docker-study`로 다시 실행했다.
- 결과: 연습 디렉토리 진입에 성공했다.

### 16-2. 명령어와 옵션 사이 공백 누락

- 문제: `chmod644 test.txt` 실행 시 `command not found`가 발생했다.
- 원인 가설: 셸이 `chmod644` 전체를 하나의 명령 이름으로 해석했다.
- 확인: `chmod --help`와 명령 형식을 확인했다.
- 해결: 명령과 권한 값 사이에 공백을 넣어 `chmod 644 test.txt`로 실행했다.
- 결과: 파일 권한이 `-rw-r--r--`로 변경됐다.

### 16-3. 파일 이름 오타

- 문제: `ls -l text.txt` 실행 시 `No such file or directory`가 발생했다.
- 원인 가설: 실제 파일은 `test.txt`인데 `text.txt`로 잘못 입력했다.
- 확인: `ls -la`로 실제 파일명을 확인했다.
- 해결: `ls -l test.txt`로 다시 실행했다.
- 결과: 파일 권한과 정보가 정상 출력됐다.

### 16-4. 볼륨 이름 불일치

- 문제: `mt-data`를 생성한 뒤 컨테이너에는 `my-data`를 연결했다.
- 원인 가설: 볼륨 이름 입력 오타다.
- 확인: `docker volume ls`로 두 이름을 확인했다.
- 해결: 재현 문서의 볼륨 이름을 `my-data`로 통일했다.
- 결과: 두 컨테이너가 같은 볼륨의 데이터를 확인할 수 있는 절차가 명확해졌다.

---

## 17. 보안 및 개인정보 보호

- GitHub 토큰, 비밀번호, 인증 코드, SSH 개인키를 커밋하지 않는다.
- 터미널 캡처에 민감정보가 보이면 업로드 전에 마스킹한다.
- `.env`, 개인키, 인증 파일은 `.gitignore`로 제외한다.
- 민감정보가 Git 이력에 포함되면 해당 정보를 즉시 폐기·재발급하고 저장소 이력에서도 제거한다.

---

## 18. 평가자 재현 절차

평가자는 다음 명령으로 커스텀 웹 서버를 재현할 수 있다.

```bash
git clone https://github.com/dlckdwls76/CodysseyFirstMission_E1_1.git
cd CodysseyFirstMission_E1_1

docker build -t my-custom-nginx:1.0 ./docker-study
docker run -d --name my-custom-web -p 8081:80 my-custom-nginx:1.0

docker ps
curl http://localhost:8081
docker logs my-custom-web

docker rm -f my-custom-web
```

예상 성공 기준:

1. 이미지 빌드가 오류 없이 완료된다.
2. `docker ps`에서 `my-custom-web`이 실행 중으로 표시된다.
3. `curl http://localhost:8081`에서 커스텀 HTML이 출력된다.
4. `docker logs my-custom-web`에서 NGINX 시작 완료 메시지를 확인할 수 있다.

---

## 19. 최종 제출 전 추가할 증거

다음 항목은 실제 터미널 또는 브라우저에서 다시 실행해 결과를 추가한다.

1. `docker images` 출력
2. `docker stats --no-stream` 출력
3. 바인드 마운트 변경 전·후 `curl` 출력
4. 주소창에 `http://localhost:8081`이 보이는 브라우저 화면
5. `git config --list` 결과
6. VS Code GitHub 로그인 및 저장소 연동 화면

증거 파일을 추가할 경우 README에서 상대 경로로 연결한다.
