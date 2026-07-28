# Codyssey E1-1
# 개발 워크스테이션 구축

> 터미널, Docker, Git/GitHub를 이용해 누구나 같은 방식으로 실행하고 검증할 수 있는 개발 환경을 구성한 과정을 기록

## 문서 작성 원칙

- 이 문서의 명령은 직접 실행한 뒤, `실제 출력` 영역에 본인의 결과를 기록한다.
- 실행하지 않은 명령이나 다른 사람의 출력은 수행 결과처럼 작성하지 않는다.
- 스크린샷은 `docs/images/`에 저장하고 이 문서에서 상대 경로로 연결한다.
- 토큰, 비밀번호, 개인키, 인증 코드, 이메일 등 민감정보는 기록하지 않거나 마스킹한다.
- 개인 PC에만 맞는 경로가 있다면 다른 사람이 따라 할 수 있도록 대체 방법을 함께 적는다.

---

## 1. 프로젝트 개요

이 프로젝트의 목표는 리눅스 CLI로 파일과 권한을 다루고, Docker로 재현 가능한 웹 서버 실행 환경을 만들며, Git과 GitHub로 변경 이력을 관리하고 공유하는 것이 이번 미션의 핵심이다.
직접 확인할 핵심 내용

1. 절대 경로와 상대 경로를 구분한다.
2. 파일과 디렉터리의 권한을 확인하고 변경한다.
3. Docker 이미지와 컨테이너의 차이를 이해한다.
4. Dockerfile로 커스텀 이미지를 만든다.
5. 포트 매핑을 통해 컨테이너의 웹 서버에 접속한다.
6. 바인드 마운트로 호스트의 변경 사항을 반영한다.
7. Docker 볼륨으로 컨테이너가 삭제된 뒤에도 데이터를 유지한다.
8. Git으로 로컬 변경 이력을 관리하고 GitHub에서 협업 가능한 형태로 공유한다.

## 2. 실행 환경

| 항목 | 사용 환경 | 확인 명령 |
| --- | --- | --- |
| OS | `ProductName : <MacOs>`, `ProductVersion : <15.7.4>`, `BuildVersion : <24G517>` | `sw_vers`|
| CPU 아키텍처 | `<x86_64>` | `uname -m` |
| 터미널 | `<Finder>` | 사용하는 앱 이름 직접 기록 |
| Shell | `</bin/zsh>` | `echo $SHELL` |
| Docker 실행 환경 | `<OrbStack>` | 애플리케이션 및 `docker info` 확인 |
| Docker | `<28.5.2>, <build ecc6942>` | `docker --version` |
| Docker Compose | `<Docker Compose version v2.40.3>` | `docker compose version` |
| Git | `<git version 2.53.0>` | `git --version` |
| VS Code | `<x64>` | `code --version` |

### 환경 확인 로그

```bash
$ sw_vers
# ProductName : <MacOs>`, `ProductVersion : <15.7.4>`, `BuildVersion : <24G517>

$ uname -m
# x86_64

$ echo $SHELL
# bin/zsh

$ docker --version
# <28.5.2>, <build ecc6942>

$ git --version
# <git version 2.53.0>
```

## 3. 프로젝트 구조

```text
dev-workstation/
├── README.md
├── Dockerfile
├── site/
│   └── index.html
└── docs/
    └── images/
```

| 경로 | 역할 |
| --- | --- |
| `README.md` | 실습 과정, 명령, 결과, 문제 해결 과정을 기록한다. |
| `Dockerfile` | 웹 서버 커스텀 이미지의 제작 방법을 정의한다. |
| `site/index.html` | 컨테이너에서 제공할 정적 웹 페이지다. |
| `docs/images/` | 터미널, 브라우저, VS Code 연동 증거를 보관한다. |

## 4. 수행 체크리스트

- [ ] 실행 환경 확인
- [ ] 현재 위치 및 숨김 파일 확인
- [ ] 디렉터리 이동·생성 및 파일 생성·복사·이동·삭제
- [ ] 파일 1개의 권한 변경 전후 비교
- [ ] 디렉터리 1개의 권한 변경 전후 비교
- [ ] Docker 설치 및 데몬 동작 확인
- [ ] Docker 이미지 다운로드 및 목록 확인
- [ ] 컨테이너 실행·중지·목록·로그·리소스 확인
- [ ] `hello-world` 실행
- [ ] Ubuntu 컨테이너 진입 및 내부 명령 실행
- [ ] `attach`와 `exec`의 차이 관찰
- [ ] Dockerfile 작성 및 커스텀 이미지 빌드
- [ ] 포트 매핑 후 브라우저 또는 `curl` 접속 확인
- [ ] 바인드 마운트 변경 전후 비교
- [ ] Docker 볼륨 데이터 영속성 확인
- [ ] Git 사용자 정보와 기본 브랜치 설정 확인
- [ ] GitHub 저장소 연결 및 push
- [ ] VS Code GitHub 연동 증거 추가
- [ ] 트러블슈팅 2건 이상 기록
- [ ] 민감정보 포함 여부 최종 점검

---

## 5. 터미널 기본 조작

### 5.1 현재 위치, 목록, 이동

#### 수행 명령

```bash
pwd
mkdir -p ~/dev-workstation/cli-practice
cd ~/dev-workstation/cli-practice
pwd
ls -la
```

#### 실제 출력

```text
여기에 실제 명령과 출력을 붙여넣는다.
```

#### 설명

- `pwd`: 현재 작업 디렉터리의 절대 경로를 출력한다.
- `mkdir -p`: 필요한 상위 디렉터리까지 함께 생성한다.
- `cd`: 현재 작업 디렉터리를 변경한다.
- `ls -la`: 숨김 파일을 포함해 권한과 소유자 등의 상세 정보를 표시한다.

### 5.2 생성, 내용 확인, 복사, 이동, 이름 변경, 삭제

#### 수행 명령

```bash
cd ~/dev-workstation/cli-practice
mkdir original copied
touch original/note.txt
echo "CLI practice" > original/note.txt
cat original/note.txt
cp original/note.txt copied/note-copy.txt
mv copied/note-copy.txt copied/renamed-note.txt
ls -la original copied
rm copied/renamed-note.txt
rmdir copied
ls -la
```

#### 실제 출력

```text
여기에 실제 명령과 출력을 붙여넣는다.
```

#### 설명

- `touch`: 빈 파일을 만들거나 기존 파일의 수정 시간을 갱신한다.
- `cat`: 파일 내용을 터미널에 출력한다.
- `cp`: 파일이나 디렉터리를 복사한다.
- `mv`: 위치를 옮기며, 같은 위치에서는 이름 변경 용도로도 사용한다.
- `rm`: 파일을 삭제한다.
- `rmdir`: 비어 있는 디렉터리를 삭제한다.

### 5.3 절대 경로와 상대 경로

- 절대 경로는 루트부터 대상을 전부 나타낸다. 예: `/Users/<사용자명>/dev-workstation/cli-practice/original/note.txt`
- 상대 경로는 현재 작업 디렉터리를 기준으로 나타낸다. 예: 현재 위치가 `cli-practice`라면 `original/note.txt`
- 절대 경로는 현재 위치와 관계없이 같은 대상을 가리키지만, 상대 경로는 현재 위치에 따라 대상이 달라질 수 있다.

#### 직접 확인

```bash
cd ~/dev-workstation/cli-practice
cat "$HOME/dev-workstation/cli-practice/original/note.txt"
cat original/note.txt
```

두 명령이 같은 파일의 내용을 출력하는지 확인한다.

---

## 6. 파일과 디렉터리 권한

### 6.1 권한 표기 이해

`ls -l`의 권한은 `소유자(user) / 그룹(group) / 그 외 사용자(others)` 순서로 읽는다.

| 권한 | 숫자 | 파일에서의 의미 | 디렉터리에서의 의미 |
| --- | ---: | --- | --- |
| `r` | 4 | 내용 읽기 | 내부 목록 보기 |
| `w` | 2 | 내용 수정 | 내부 항목 생성·삭제 |
| `x` | 1 | 실행 | 디렉터리 내부로 진입 |

- `755 = rwxr-xr-x`: 소유자는 읽기·쓰기·실행, 나머지는 읽기·실행
- `644 = rw-r--r--`: 소유자는 읽기·쓰기, 나머지는 읽기

### 6.2 파일 권한 변경

```bash
cd ~/dev-workstation/cli-practice
touch permission-file.txt
ls -l permission-file.txt
chmod 600 permission-file.txt
ls -l permission-file.txt
chmod 644 permission-file.txt
ls -l permission-file.txt
```

#### 변경 전후 실제 출력

```text
여기에 실제 출력을 붙여넣는다.
```

#### 관찰 내용

`<600과 644에서 소유자·그룹·기타 사용자의 권한이 어떻게 달라졌는지 직접 작성>`

### 6.3 디렉터리 권한 변경

```bash
cd ~/dev-workstation/cli-practice
mkdir permission-dir
ls -ld permission-dir
chmod 700 permission-dir
ls -ld permission-dir
chmod 755 permission-dir
ls -ld permission-dir
```

#### 변경 전후 실제 출력

```text
여기에 실제 출력을 붙여넣는다.
```

#### 관찰 내용

`<700과 755에서 디렉터리 접근 권한이 어떻게 달라졌는지 직접 작성>`

---

## 7. Docker 설치 및 기본 점검

서울캠퍼스에서는 시스템 보안 정책으로 `sudo` 사용이 제한될 수 있으므로 OrbStack을 실행해 Docker 엔진을 구동한다. 터미널의 `docker` 명령은 OrbStack 내부의 Docker 엔진에 요청을 전달한다.

### 수행 명령

```bash
docker --version
docker info
docker compose version
```

### 실제 출력

```text
민감정보가 없는지 확인한 뒤 핵심 출력을 붙여넣는다.
```

### 확인한 내용

- `docker --version`은 Docker CLI 버전을 확인한다.
- `docker info`는 Docker 엔진과 통신할 수 있는지, 현재 이미지·컨테이너 수와 실행 환경이 무엇인지 확인한다.
- 버전은 나오지만 `docker info`가 실패한다면 CLI는 설치됐지만 Docker 엔진이 실행되지 않았을 가능성이 있다.

---

## 8. Docker 기본 운영

### 8.1 이미지 다운로드와 목록 확인

```bash
docker pull hello-world
docker pull ubuntu:24.04
docker images
```

### 실제 출력

```text
여기에 실제 출력을 붙여넣는다.
```

### 8.2 hello-world 실행

```bash
docker run --name hello-test hello-world
docker ps
docker ps -a
docker logs hello-test
```

### 실제 출력

```text
여기에 실제 출력을 붙여넣는다.
```

### 관찰

`hello-world` 컨테이너는 안내 문구를 출력한 뒤 실행할 작업이 끝나므로 종료된다. 따라서 실행 중인 컨테이너만 표시하는 `docker ps`에는 없고, 종료된 컨테이너까지 표시하는 `docker ps -a`에는 나타난다.

### 8.3 Ubuntu 컨테이너 실행과 내부 진입

```bash
docker run -dit --name ubuntu-lab ubuntu:24.04 bash
docker ps
docker exec -it ubuntu-lab bash
```

컨테이너 내부에서 실행한다.

```bash
pwd
ls -la
echo "inside ubuntu container"
exit
```

호스트 터미널에서 다시 실행한다.

```bash
docker ps
docker stats --no-stream ubuntu-lab
docker stop ubuntu-lab
docker ps -a
```

### 실제 출력

```text
여기에 실제 출력을 붙여넣는다.
```

### `attach`와 `exec` 관찰 정리

- `docker attach ubuntu-lab`: 컨테이너의 기존 주 프로세스에 연결한다. 입력과 종료 동작이 주 프로세스에 영향을 줄 수 있다.
- `docker exec -it ubuntu-lab bash`: 실행 중인 컨테이너 안에 새로운 `bash` 프로세스를 추가로 실행한다. 해당 셸에서 `exit`해도 컨테이너의 주 프로세스가 계속 실행 중이면 컨테이너는 유지된다.
- 이 실습에서는 안전하게 별도의 셸을 여는 `exec`를 주로 사용했다.

---

## 9. Dockerfile 기반 커스텀 웹 서버

### 9.1 선택한 베이스 이미지

`nginx:alpine`을 베이스 이미지로 사용한다.

- NGINX는 정적 파일을 HTTP로 제공하는 웹 서버다.
- Alpine 기반 이미지는 비교적 크기가 작아 간단한 정적 웹 서버 실습에 적합하다.

### 9.2 커스텀 포인트

| 커스텀 항목 | 목적 |
| --- | --- |
| `site/index.html` 복사 | 기본 NGINX 화면 대신 직접 만든 페이지 제공 |
| OCI 라벨 추가 | 이미지의 이름과 설명 기록 |
| `HEALTHCHECK` 추가 | 웹 서버가 실제로 응답하는지 상태 확인 |

### 9.3 Dockerfile

```dockerfile
FROM nginx:alpine

LABEL org.opencontainers.image.title="dev-workstation-web"
LABEL org.opencontainers.image.description="Docker workstation mission"

COPY site/index.html /usr/share/nginx/html/index.html

HEALTHCHECK --interval=10s --timeout=3s --retries=3 \
  CMD wget -qO- http://localhost/ > /dev/null || exit 1

EXPOSE 80
```

### 9.4 빌드 및 실행

```bash
cd ~/dev-workstation
docker build -t dev-workstation-web:1.0 .
docker images
docker run -d --name workstation-web -p 8080:80 dev-workstation-web:1.0
docker ps
docker logs workstation-web
curl http://localhost:8080
```

### 실제 출력

```text
여기에 빌드, 실행, 로그, curl의 핵심 출력을 붙여넣는다.
```

### 포트 매핑 설명

`-p 8080:80`은 호스트의 `8080`번 포트로 들어온 요청을 컨테이너의 `80`번 포트로 전달한다.

컨테이너는 호스트와 격리된 네트워크 공간을 사용하므로, NGINX가 컨테이너 내부의 80번 포트에서 실행 중이라는 사실만으로 호스트 브라우저에서 바로 접근할 수 없다. 포트 매핑은 이 격리된 두 네트워크 사이에 접속 통로를 만든다.

### 브라우저 접속 증거

브라우저 주소창에 `http://localhost:8080`을 입력하고 주소창과 페이지가 함께 보이도록 캡처한다.

```markdown
![포트 매핑 접속 성공](docs/images/port-mapping-8080.png)
```

---

## 10. 바인드 마운트 변경 반영

바인드 마운트는 호스트의 실제 디렉터리를 컨테이너 경로에 직접 연결한다. 호스트 파일을 수정하면 이미지를 다시 빌드하지 않아도 컨테이너에서 변경된 내용을 볼 수 있다.

### 실행 및 변경 전 확인

```bash
docker rm -f bind-web 2>/dev/null || true
docker run -d \
  --name bind-web \
  -p 8081:80 \
  -v "$HOME/dev-workstation/site:/usr/share/nginx/html:ro" \
  nginx:alpine
curl http://localhost:8081
```

### 호스트 파일 변경 후 확인

`site/index.html`의 화면 문구를 수정한 뒤 실행한다.

```bash
curl http://localhost:8081
docker inspect bind-web
```

### 변경 전후 실제 출력

```text
변경 전 응답:

변경 후 응답:
```

### 관찰 내용

`<이미지를 다시 빌드하지 않았는데 응답이 달라진 이유를 직접 작성>`

---

## 11. Docker 볼륨 영속성

Docker 볼륨은 Docker가 관리하는 데이터 저장 공간이다. 컨테이너의 생명주기와 분리되어 있으므로 컨테이너를 삭제하고 새로 만들어도 같은 볼륨을 연결하면 데이터를 다시 읽을 수 있다.

### 11.1 볼륨 생성과 데이터 저장

```bash
docker volume create workstation-data
docker volume ls
docker run -d --name volume-test-1 \
  -v workstation-data:/data \
  ubuntu:24.04 sleep infinity
docker exec volume-test-1 \
  bash -lc 'echo "persistent data" > /data/message.txt && cat /data/message.txt'
```

### 11.2 첫 번째 컨테이너 삭제

```bash
docker rm -f volume-test-1
docker ps -a
docker volume ls
```

### 11.3 새 컨테이너에서 데이터 재확인

```bash
docker run -d --name volume-test-2 \
  -v workstation-data:/data \
  ubuntu:24.04 sleep infinity
docker exec volume-test-2 cat /data/message.txt
```

### 삭제 전후 실제 출력

```text
첫 번째 컨테이너에서 읽은 값:

첫 번째 컨테이너 삭제 결과:

두 번째 컨테이너에서 읽은 값:
```

### 검증 결론

`<컨테이너를 삭제했는데도 두 번째 컨테이너에서 같은 데이터를 읽을 수 있었던 이유를 직접 작성>`

---

## 12. 이미지와 컨테이너의 차이

| 구분 | 이미지 | 컨테이너 |
| --- | --- | --- |
| 의미 | 실행 환경과 애플리케이션을 담은 읽기 전용 설계도 | 이미지를 바탕으로 생성된 실행 인스턴스 |
| 비유 | 클래스 또는 빵틀 | 객체 또는 빵틀로 만든 빵 |
| 생성 명령 예시 | `docker build`, `docker pull` | `docker run`, `docker create` |
| 상태 | 실행되지 않음 | 생성·실행·중지·삭제 상태를 가짐 |
| 재사용 | 하나의 이미지로 여러 컨테이너 생성 가능 | 각 컨테이너는 독립된 쓰기 영역을 가짐 |

같은 이미지에서 여러 컨테이너를 생성하면 기본 실행 환경은 같지만 각 컨테이너의 실행 상태는 서로 격리된다. 공유해야 하는 데이터는 바인드 마운트나 볼륨처럼 명시적인 연결을 사용한다.

---

## 13. Git 설정 및 GitHub 연동

### 13.1 Git 설정

전역 설정을 변경하기 전에 현재 값을 먼저 확인한다.

```bash
git config --global --get user.name
git config --global --get user.email
git config --global --get init.defaultBranch
```

필요한 경우 본인의 정보로 설정한다.

```bash
git config --global user.name "<Git 표시 이름>"
git config --global user.email "<GitHub에 사용할 이메일>"
git config --global init.defaultBranch main
```

민감하거나 불필요한 값까지 공개하지 않도록 필요한 항목만 확인한다.

```bash
git config --global --get user.name
git config --global --get user.email
git config --global --get init.defaultBranch
```

### 실제 출력

```text
공개해도 되는 값만 기록하고 필요한 부분은 마스킹한다.
```

### 13.2 로컬 저장소 생성과 첫 커밋

```bash
cd ~/dev-workstation
git init
git status
git add README.md Dockerfile site docs
git status
git commit -m "docs: add workstation mission"
git log --oneline
```

### 실제 출력

```text
여기에 실제 출력을 붙여넣는다.
```

### 13.3 GitHub 원격 저장소 연결

GitHub에서 빈 저장소를 만든 뒤, 저장소 화면에 표시된 본인의 URL을 사용한다.

```bash
git remote add origin <본인의-GitHub-저장소-URL>
git remote -v
git push -u origin main
```

### 실제 출력

```text
토큰이나 인증 정보가 노출되지 않은 push 결과를 붙여넣는다.
```

### Git과 GitHub의 역할 차이

- Git은 내 컴퓨터에서 파일 변경 이력을 저장하고 브랜치와 커밋을 관리하는 버전 관리 도구다.
- GitHub는 Git 저장소를 원격에 보관하고 코드 공유, 리뷰, 이슈, 협업 기능을 제공하는 플랫폼이다.
- 인터넷 연결이 없어도 Git의 로컬 커밋은 가능하지만, GitHub로 `push`하거나 원격 변경을 `pull`하려면 네트워크 연결과 인증이 필요하다.

### VS Code 연동 증거

1. VS Code에서 GitHub 계정에 로그인한다.
2. 프로젝트 폴더를 연다.
3. Source Control에서 저장소와 변경 사항이 정상 표시되는지 확인한다.
4. 계정이나 저장소 연결은 보이되 토큰·이메일 등 민감정보는 보이지 않도록 캡처한다.

```markdown
![VS Code GitHub 연동](docs/images/vscode-github-link.png)
```

---

## 14. Docker 운영 확인 및 정리

### 상태와 로그 확인

```bash
docker images
docker ps
docker ps -a
docker logs workstation-web
docker stats --no-stream
docker volume ls
```

### 실습 컨테이너 정리

아래 명령은 이름이 일치하는 실습 컨테이너만 삭제한다.

```bash
docker rm -f workstation-web bind-web volume-test-2 ubuntu-lab hello-test
docker ps -a
```

볼륨 영속성 증거를 모두 기록한 뒤에만 실습 볼륨을 삭제한다.

```bash
docker volume rm workstation-data
```

---

## 15. 검증 결과와 증거

| 검증 대상 | 검증 명령 또는 방법 | 성공 기준 | 증거 위치 |
| --- | --- | --- | --- |
| 터미널 기본 조작 | `pwd`, `ls -la`, `cat` 등 | 생성·복사·이동·삭제 결과 확인 | [5. 터미널 기본 조작](#5-터미널-기본-조작) |
| 권한 변경 | `ls -l`, `chmod` | 변경 전후 권한 문자열이 달라짐 | [6. 파일과 디렉터리 권한](#6-파일과-디렉터리-권한) |
| Docker 엔진 | `docker info` | 서버 정보가 오류 없이 표시됨 | [7. Docker 설치 및 기본 점검](#7-docker-설치-및-기본-점검) |
| hello-world | `docker logs hello-test` | 성공 안내 문구 확인 | [8. Docker 기본 운영](#8-docker-기본-운영) |
| 커스텀 이미지 | `docker build`, `docker images` | 이미지 빌드 및 목록 표시 | [9. Dockerfile 기반 커스텀 웹 서버](#9-dockerfile-기반-커스텀-웹-서버) |
| 포트 매핑 | 브라우저, `curl` | `localhost:8080` 응답 확인 | `docs/images/port-mapping-8080.png` |
| 바인드 마운트 | 변경 전후 `curl` | 이미지 재빌드 없이 응답 변경 | [10. 바인드 마운트 변경 반영](#10-바인드-마운트-변경-반영) |
| 볼륨 영속성 | 컨테이너 삭제 전후 `cat` | 새 컨테이너에서 기존 값 확인 | [11. Docker 볼륨 영속성](#11-docker-볼륨-영속성) |
| GitHub 연동 | `git remote -v`, `git push` | 원격 저장소에서 커밋 확인 | `<GitHub 저장소 링크>` |
| VS Code 연동 | Source Control 화면 | 저장소가 정상 인식됨 | `docs/images/vscode-github-link.png` |

---

## 16. 트러블슈팅

실제로 겪은 문제를 최소 2건 기록한다. 아래 예시 문장을 그대로 결과로 제출하지 않고, 본인이 관찰한 오류 메시지와 확인 과정을 작성한다.

### 문제 1: `<문제 제목>`

- 문제: `<어떤 명령에서 어떤 현상이 발생했는가?>`
- 오류 메시지: `<핵심 오류를 정확히 기록>`
- 원인 가설: `<왜 발생했다고 생각했는가?>`
- 확인: `<가설을 확인하기 위해 실행한 명령과 결과>`
- 해결 또는 대안: `<어떤 조치로 해결했는가?>`
- 배운 점: `<다음에는 무엇을 먼저 확인할 것인가?>`

### 문제 2: `<문제 제목>`

- 문제: `<어떤 명령에서 어떤 현상이 발생했는가?>`
- 오류 메시지: `<핵심 오류를 정확히 기록>`
- 원인 가설: `<왜 발생했다고 생각했는가?>`
- 확인: `<가설을 확인하기 위해 실행한 명령과 결과>`
- 해결 또는 대안: `<어떤 조치로 해결했는가?>`
- 배운 점: `<다음에는 무엇을 먼저 확인할 것인가?>`

### 자주 발생할 수 있는 문제 후보

| 증상 | 먼저 확인할 것 |
| --- | --- |
| `docker info`가 서버 연결 오류를 표시함 | OrbStack이 실행 중인지 확인 |
| 포트 바인딩 오류가 발생함 | `docker ps`와 `lsof -i :8080`으로 포트 사용 여부 확인 |
| 브라우저에 접속되지 않음 | 컨테이너 상태, `docker logs`, `curl`, 포트 매핑 순서로 확인 |
| 바인드 마운트 내용이 보이지 않음 | 호스트 절대 경로와 컨테이너 마운트 경로 확인 |
| `git push`가 거절됨 | 원격 URL, 현재 브랜치, 인증 상태, 원격 커밋 존재 여부 확인 |

---

## 17. 보안 점검

- [ ] README에 비밀번호, 토큰, 인증 코드가 없다.
- [ ] `.env`, 개인키, 인증 파일을 커밋하지 않았다.
- [ ] 스크린샷의 이메일, 사용자명 등 불필요한 개인정보를 마스킹했다.
- [ ] `git diff --cached`로 커밋 예정 내용을 확인했다.
- [ ] `git log --all -p` 또는 저장소 검색으로 민감정보 노출 여부를 확인했다.

민감정보가 이미 Git 이력에 포함됐다면 단순히 최신 파일에서 삭제하는 것만으로는 부족하다. 해당 인증 정보를 즉시 폐기·재발급한 뒤, 저장소 이력에서도 제거해야 한다.

---

## 18. 재현 방법

평가자는 다음 순서로 커스텀 웹 서버를 재현할 수 있다.

```bash
git clone <GitHub 저장소 URL>
cd <저장소 폴더>
docker build -t dev-workstation-web:1.0 .
docker run -d --name workstation-web -p 8080:80 dev-workstation-web:1.0
curl http://localhost:8080
docker logs workstation-web
docker rm -f workstation-web
```

예상 성공 기준:

1. 이미지 빌드가 오류 없이 끝난다.
2. 컨테이너가 `docker ps`에서 실행 중으로 보인다.
3. `http://localhost:8080`에서 직접 작성한 웹 페이지가 응답한다.

---

## 19. 동료 학습 설명용 요약

다른 학습자에게 설명할 때는 명령어 이름만 나열하지 않고 다음 흐름으로 말한다.

1. **문제**: 개발 환경이 사람마다 다르면 같은 코드도 다르게 동작할 수 있다.
2. **해결 구조**: Docker 이미지에 실행 환경을 정의하고, 그 이미지로 격리된 컨테이너를 만든다.
3. **외부 연결**: 포트 매핑으로 호스트와 컨테이너의 네트워크를 연결한다.
4. **파일 연결**: 바인드 마운트는 호스트 파일 변경을 바로 반영하고, 볼륨은 컨테이너와 데이터 생명주기를 분리한다.
5. **기록과 협업**: Git은 로컬 이력을 관리하고, GitHub는 저장소를 원격에서 공유하고 협업하게 한다.
6. **검증**: 명령 성공 여부만 믿지 않고 목록, 로그, HTTP 응답, 컨테이너 삭제 전후 데이터로 실제 동작을 확인한다.

### 한 문장 설명

> 이 미션은 터미널로 개발 환경의 기반을 다지고, Docker로 동일하게 재현되는 실행 환경을 만들며, Git/GitHub로 그 과정과 결과를 다른 사람도 검증할 수 있게 공유하는 실습이다.

---

## 20. 회고

- 가장 이해가 잘 된 개념: `<직접 작성>`
- 가장 어려웠던 부분: `<직접 작성>`
- 문제를 해결할 때 사용한 확인 순서: `<직접 작성>`
- 다시 수행한다면 개선할 점: `<직접 작성>`
- CI/CD 또는 클라우드 배포와 연결되는 지점: `<직접 작성>`
