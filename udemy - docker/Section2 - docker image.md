# Section 2 : Docker 이미지 & 컨테이너: 코어 빌딩 블록

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

### 자체 이미지 기반으로 실행하기

이미지 빌드

```bash
docker build .
=> [internal] load build definition from Dockerfile          0.0s
=> => transferring dockerfile: 133B                          0.0s
=> [internal] load .dockerignore                             0.0s
=> => transferring context: 2B                               0.0s
=> [internal] load metadata for docker.io/library/node:late  0.0s
=> [1/4] FROM docker.io/library/node                         0.1s
=> [internal] load build context                             0.0s
=> => transferring context: 8.97kB                           0.0s
=> [2/4] WORKDIR /app                                        0.0s
=> [3/4] COPY . /app                                         0.0s
=> [4/4] RUN npm install                                     2.8s
=> exporting to image                                        0.1s
=> => exporting layers                                       0.1s
=> => writing image sha256:38d638113ae561d80354aa2fc830ca8c
```

아래 나온 image에서 sha256: 뒤에 있는 문자열 복사

```bash
docker run 38d638113a
```

이러고 새로운 cmd를 열어서 docker ps로 확인해보면 실행중인 걸 확인할 수 있음

```bash
CONTAINER ID   IMAGE        COMMAND                  CREATED          STATUS          PORTS     NAMES
8ea95ae92ee3   38d638113a   "docker-entrypoint.s…"   54 seconds ago   Up 53 seconds   80/tcp    nice_cohen
```

EXPOSE 80은 단순히 80포트로 연다는 표시일 뿐, 더 추가적인 작업이 필요함.(단지 문서로서 남기는 정도임)

컨테이너를 docker run으로 실행할 때 플래그를 추가함 -p(publish)

도커에게 어떤 로컬 포트가 있고 이 내부 도커 특정 포트에 엑세스 할 수 있는지 알려줌

```bash
docker run -p 3000:80 38d638113a
```

3000 포트는 내 로컬 노트북에서 허용할 포트이고 뒤에 80포트는 도커 내부에서 expose한 포트를 적어주면 된다.

**Dockerfile의 ‘EXPOSE 80’은 선택 사항이다.** 

컨테이너의 프로세스가 이 포트를 노출할 것임을 **문서화**하는 목적이다. 

실제 ‘docker run’을 실행할 때 ‘-p’를 사용해서 실제로 포트를 노출해야 하는데 Dockerfile에 ‘EXPOSE’를 추가해서 이 동작을 문서화하는게 모범적인 사용법이다. 

참고 : ID를 사용할 수 없는 docker 명령의 경우 항상 전체 id를 복사해 사용할 필요는 없다. 앞 부분 몇 개 문자만 사용해도 돌아감. 위에 나온 이미지 문자열을 다 안써도 된다는 말.

### 이미지는 읽기 전용이다

코드를 변경하고 컨테이너를 단지 재시작 해봤자 변경된 코드는 반영되지 않는다.

도커 파일로 생성된 이미지는 읽기 전용이기 때문에 다시 이미지를 빌드해서 해당 이미지로 컨테이너를 시작해야 한다.

### 이미지 레이어 이해하기

이미지를 빌드하거나 재빌드할 때 변경된 부분의 명령과 그 이후 모든 명령이 재평가된다. 다시 빌드하면 Using Cache라는 문구가 뜬다.

도커는 기본적으로 모든 명령에 대해 명령어를 재시작한 후 이전과 동일한지 판단한다. 

변경된 내용이 없으면 그 명령을 다시 거칠 필요 없이 결과를 캐시하고 그 결과를 쓴다.

이걸 레이어 기반이라고 한다. 

이미지 레이어가 생성되고 이 레이어가 캐시된다. 이미지를 기반으로 컨테이너를 실행하면 그 컨테이너는 도커파일에 지정한 명령을 실행한 결과를 이미지 위에 새로운 추가 레이어를 추가한다.

```bash
FROM node # 1

WORKDIR /app # 2

COPY package.json /app # 7 추가

RUN npm install # 4

COPY . /app # 3

EXPOSE 80 # 5

CMD ["node", "server.js"] # 6
```

위와 같은 도커 파일이 있다고 하면 6개의 레이어가 있고 코드가 변경되어 3번 레이어에서 도커가 변경을 감지하면 3번부터 이후 작업들도 모두 재빌드한다.(캐싱 안함)

소스 코드 변경이 npm install에 영향을 미치지 않으니 다시 실행될 필요가 없다. 여기서 최적화가 가능하다.

이렇게 모든 파일을 복사한 다음 npm install하는게 아니라 npm install한 후 모두 복사하고 package.json 파일도 /app 에 복사하면 npm install이 재시작되지 않게 할 수 있다.

pacakge.json 파일은 변경되지 않을 거니 그 다음 npm install도 실행되지 않음.

### Recap

1. 도커의 이미지란 무엇일까요?
    
    이미지는 읽기/쓰기 액세스 권한이 있는 인스턴스를 실행하는 컨테이너의 blueprint다.
    
2. 이미지와 컨테이너가 있는 이유는 무엇일까요? 컨테이너만으로는 왜 안될까요?
    
    분리함으로써 여러 컨테이너가 동일한 이미지를 기반으로 하더라도 서로 간섭하지 않는 격리된 상태로 동작한다.
    
3. 컨테이너와 관련해 격리는 뭘 의미하나요?
    
    컨테이너가 서로 분리되어 있고, 디폴트로 공유된 데이터나 상태가 없음을 의미한다.
    
4. 컨테이너는 무엇일까요?
    
    이미지의 실행 중인 인스턴스다. 이미지를 기반으로 격리된 소프트웨어 유닛이다. 
    
5. 이미지의 내용 context에서 레이어는 뭘 의미할까요?
    
    이미지의 모든 명령은 캐시 가능한 레이를 생성한다. 레이어는 이미지 재구축 및 공유를 돕는다.
    
6. 이 명령은 무엇을 하나요?
    
    ```bash
    docker build .
    ```
    
    이미지를 구축한다.
    
7. 이건 또 뭘할까요?
    
    ```bash
    docker run node
    ```
    
    node라는 이미지를 기반으로 컨테이너를 만들어 실행한다.
    

### 컨테이너 중지 & 재시작

```bash
docker start 컨테이너이름
```

docker start는 처음 했던 docker run과는 다르다. 

docker run 으로 실행하면 새로운 컨테이너가 실행되지만 docker start는 기존에 있던 컨테이너를 재시작한다.

docker start의 경우 detached 모드가 디폴트고, docker run은 attatched가 기본 모드다. 

detached mode로 실행하면 실행 중인 컨테이너에 연결되어 실제 앱에서 실행된 결과가 콘솔에 출력되나 detached mode에서는 그렇지 않다. 

docker run도 detached mode로 시작하고 싶다면 -d 플래그를 붙여주면 된다.

```bash
docker run -p 8000:80 -d 38d638113a
```

실행 중에 attach mode로 변경하고 싶다면 attach 명령어를 써주면 된다.

```bash
docker attach 컨테이너이름
```

log를 보고 싶은 목적이라면 docker log를 써도 된다.

```bash
docker logs -f 컨테이너이름
```

이렇게 하면 향후 컨테이너에서 발생하는 로그를 볼 수 있게 된다.

(-f를 안붙이면 지금까지 뜬 모든 로그가 출력됨, -f를 쓰면 그 이후부터 생기는 로그들만 출력된다.)

-a 플래그를 써서 docker start에서 바로 attached mode로 시작할 수 있다.

```bash
docker start -a 컨테이너이름
```

### interactive 모드

사용자의 입력을 받는 등의 동작이 필요할 때는?

```docker
FROM python

WORKDIR /app

COPY . /app

CMD ["python", "rng.py"] // rng.py 파일 실행 
```

```bash
docker build .
docker run -it e391df... (-i, -t)
```

docker start는 detach가 디폴트이므로 -i, -t를 함께 실행해 하면 interactive하게 실행할 수 있다. 

-i : 컨테이너에 입력을 받을 수 있게함

### 이미지 & 컨테이너 삭제

```bash
docker ps -a
docker rm elqu... # 실행중인 컨테이너는 삭제 못함
# docker stop 후 rm 할 수 있다. 
```

사용되지 않을 때 자동으로 삭제할 수도 있다.

```bash
docker rmi 230dfbdk # rmi -> 이미지 삭제
# rm 만 쓰면 컨테이너 삭제

# 사용하지 않는 이미지 모두 삭제
# docker image prune

# 여러 개 삭제
docker rmi 1230fdk 1290klda 1233adsf
```

### 중지된 컨테이너 자동 삭제하기

docker run —help를 이용해서 자주 확인해보기.

컨테이너가 종료될 때 자동으로 이미지가 삭제되는 옵션(--rm)

```bash
 docker run -p 3000:80 -d --rm 2kdkda93kdac
```

detached 모드로 실행되고, 컨테이너가 종료되면 컨테이너가 알아서 삭제된다. 

### 이미지 검사

docker image inspect 명령을 이용해 이미지에 대한 정보를 출력할 수 있다. 

몇 가지 중요한 정보는 이미지가 생성된 시간이나 오픈된 port 번호, 사용중인 도커 버전, 운영체제 등에 대한 정보를 알 수 있다. 

### 컨테이너에서 파일 복사하기

docker cp 사용하기

- 컨테이너 → 로컬 호스트
- 로컬 호스트 → 컨테이너

```bash
docker cp dummy/. boring_vaughan:/test
                    destination(container 이름:/목적지)

docker cp boring_vaughan:/test dummy
docker cp boring_vaughan:/test/test.txt dummy
                      컨테이너에 있는 걸 -> 로컬 폴더로
```

### 컨테이너와 이미지에 이름 지정 & 태그 지정

```bash
docker run --help
# --name option
docker run -p 3000:80 -d --rm --name goalsapp 23dadfj39d
# 위에서 goalsapp 이 이름임

docker stop goalsapp
docker ps -a
```

**name : tag**

name : image 의 repository(특정 이미지 그룹)

tag : 그 이미지의 특정 버전(그룹 내에서의 특정 버전)

```docker
FROM node:14
```

태그를 지정하는 방법


### 이미지 공유하기

이미지를 공유하는 방법 

- Dockerfile 공유
    - docker build 과정 필요
    - 이미지에 들어가야 하는 모든 폴더 구조, 파일들이 필요함
    
- 빌드된 전체 이미지를 공유
    - 그냥 이미지를 다운 받아 컨테이너를 실행하면 된다.
    - build 하는 과정 필요 없음

### DockerHub에 이미지 push하기

이미지를 push 할 수 있는 2가지

- Docker hub - 보통 공개되는 official 이미지들이 올라감
- private Registry(개인 레지스트리)

```bash
docker tag node-demo:latest id/node-hello-world

docker login
docker push id/node-hello-world
```

### 공유 이미지 pull & 사용하기

```bash
docker pull id/node-hello-world
docker run -p 3000:80 --rm id/node-hello-world
```

### 모듈 요약

Image : 

템플릿, 컨테이너의 blueprint

하나의 동일한 이미지를 기반으로 하는 여러 컨테이너를 실행할 수 있으며 다양한 컨테이너를 위해 여러 이미지를 가질 수 있다.

컨테이너가 이미지 위에 작은 레이어로 실행되는 방식이다. 

이미지에 저장된 코드와 환경을 사용해 config도 하고 구성된 애플리케이션을 실행한다.

동일한 애플리케이션을 실행하는 여러 컨테이너가 시스템에서 적은 공간을 차지하면서 분리되어 실행될 수 있는 방식이다. 

이미지는 ‘docker pull’로 다운로드 되거나, 자체 이미지를 빌드하는 경우에는 Dockerfile의 도움으로 생성된다. 

Dockerfile과 ‘docker build’는 새 이미지를 빌드한다. 

이 이미지를 구성할 때는 이미지로 뭘 복사할지 제어하고, config 에 관한 명령들이 있다. → 이렇게 이미지를 기반으로 컨테이너를 실행할 때마다 실행해야 하는 명령이 있다. 

Dockerfile에 넣은 이 모든 명령은 레이어를 만든다. 

이미지는 여러 레이어로 구성되고, 이 레이어 개념은 빌드 속도를 최적화하기 위해 존재한다. 

도커는 레이어를 캐시할 수 있고, 해당 단계에서 변경된 사항이 없으면 레이어를 다시 실행할 필요가 없다. 이렇게 하면 재사용성에도 도움이 된다. 

예를 들어, Node 이미지를 공유한다면 도커는 이 Node 이미지가 이미 존재해 변경되지 않았음을 알 수 있다. 그렇게 이미지에 효율적으로 연결할 수 있다. 

‘docker run’ 명령을 통해 이미지를 기반으로 한 컨테이너를 실행한다.

여기 여러 옵션과 플래그가 있다.

애플리케이션에 뭔가 입력할 수 있게 컨테이너를 interactive하게 만들 수도 있고, 이름을 지정할 수도 있다. 

혹은 컨테이너가 중지되면 자동으로 제거되게 할 수도 있다. 

컨테이너를 관리하는 다양한 명령도 있다.

컨테이너를 리스팅하거나 제거할 수 있고 혹은 원하면 중지하거나 시작할 수도 있다. 이미지의 경우에도 마찬가지다. 

‘docker push’, ‘docker pull’로 공유할 수 있다.