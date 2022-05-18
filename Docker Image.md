### Module Content

- Two Core Concepts : Images & Containers
- Using Pre-Built & Custom Images
- Creating & Managing Containers

### 이미지가 뭘까

컨테이너는 애플리케이션을 실행하는 모든 전체 환경 등, 무엇이든 포함하는 작은 패키지다. 컨테이너에는 소프트웨어 실행 유닛이 있다. 그 유닛을 우리가 실행한다.

도커를 작업할 때 디졸버라는 개념이 있다.

이미지는 컨테이너의 템플릿, blueprint 이다. 이미지는 실제 코드와 코드를 실행하는 도구를 포함한다. 컨테이너가 실행되어 코드를 실행한다.

이미지를 사용해서 여러 컨테이너를 만들 수도 있다.

예를 들어, NodeJS 애플리케이션을 한 번 정의해서 다른 시스템과 다른 서버에서 여러 번 사용할 수 있다.

이미지는 모든 config 명령과 모든 코드가 포함된 공유 가능한 패키지다.

컨테이너는 그런 이미지를 구체적으로 실행하는 인스턴스다.(컨테이너는 이미지를 기반으로 작동)

### 외부 이미지 사용

이미지를 이미 존재하는 pre-built 이미지를 사용할 수 있는데 docker hub에서 찾을 수 있다. 여기 있는 이미지는 보통 공식 팀에서 관리한다. 

```bash
docker run node
```

위 명령어를 입력하면 도커 허브에서 최신 노드를 다운로드하고 로컬 머신에서 다운되면 이 이미지를 컨테이너로 실행한다. 

```bash
docker ps -a
```

위 명령어를 실행하면 도커가 생성한 컨테이너를 확인할 수 있다. 확인하면 위의 node 이미지로 생성된 컨테이너가 보인다. 

```bash
docker run -it node
```

도커에게 컨테이너 내부에서 호스트로 대화형 세션을 노출하고 싶으면 -it 옵션을 지정한다. 이렇게 하면 바로 node 터미널이 시작된다.

```bash
➜  seoin git:(main) ✗ docker run -it node
Unable to find image 'node:latest' locally
latest: Pulling from library/node
3a36574378e6: Pull complete
a61d3345afba: Pull complete
3e267d6aa58f: Pull complete
f647907d26b8: Pull complete
3f0b3e17bdee: Pull complete
9cf8545035a8: Pull complete
4ccb0e2dc25c: Pull complete
4f0db5b8ea0e: Pull complete
70b40a159ab4: Pull complete
Digest: sha256:82f9e078898dce32c7bf3232049715f1b8fbf0d62d5f3091bca20fcaede50bf0
Status: Downloaded newer image for node:latest
Welcome to Node.js v18.1.0.
Type ".help" for more information.
> console.log("hi")
hi
undefined
```

```bash
➜  seoin git:(main) ✗ docker ps -a
CONTAINER ID   IMAGE          COMMAND                  CREATED              STATUS                          PORTS     NAMES
aa662df408f9   node           "docker-entrypoint.s…"   About a minute ago   Exited (0) About a minute ago             jolly_kalam
24796eb65279   2f02b6905925   "docker-entrypoint.s…"   16 hours ago         Exited (137) 16 hours ago                 trusting_goldstine
```

Dockerfile

```docker
FROM node # node 이미지 가져옴

# 작업 디렉토리 지정
# 설정 안하면 모든 작업이 기본적으로 현재 디렉토리에서 돌아감
# 예를 들어 의존성 다운로드(npm install)도 /app이 아닌 .(현재 디렉토리)에서 실행됨
WORKDIR /app

COPY . /app # 어떤 파일이 이미지에 들어가야 하는지 알려줌
# 기본적으로 2개의 경로를 지정함
# 첫 번째 경로는 컨테이너 외부, 이미지의 외부 경로(이미지로 복사되어야 할 파일)
# 첫번째 .은 이 프로젝트의 모든 폴더, 하위 폴더 및 파일을 복사해야 한다고 도커에 알림
# 두 번째 경로는 그 파일을 저장해야 하는 이미지의 내부 경로를 의미한다.
# /app이라고 지정하면 현재 프로젝트의 모든 폴더가 컨테이너 내부의 app 폴더로 저장된다.

# 필요한 의존성 다운로드
RUN npm install

# 현재 이 서버가 80 포트에 열려 있음
# 실행 전에 먼저 80포트로 노출함을 써줌
EXPOSE 80

# 이미지를 기반으로 실행하는 것이므로 도커 파일에 server 실행하는 코드는 넣지 않음
# 예를 들어, RUN node server.js 같은 명령어는 빼는게 맞음
# 대신 CMD를 씀 - 이미지 생성 시점에 실행이 아니라 이미지를 기반으로 컨테이너가 실행될 때 실행함
CMD ["node", "server.js"]
```