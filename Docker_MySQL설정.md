1. command line - Mac M1 기준
    1. —platform linux/amd64 설정해서 다운받기
        1. m1은 arm64 인데 MySQL 등 amd64 용 이미지로만 제공하는 패키지가 있음
        2. 설정을 따로 안하면 arm64가 기본으로 다운로드 되므로 실행 안됨

    ```bash
    docker run 
    --platform linux/amd64 -p 3306:3306 
    --name mysql_container  // docker container 이름
    -e MYSQL_ROOT_PASSWORD=1234 
    -e MYSQL_DATABASE=docker 
    -e MYSQL_USER=user 
    -e MYSQL_PASSWORD=1234 
    -d mysql
    ```

    2. `docker ps -a` 로 컨테이너 id, 이름 등을 확인할 수 있음
        1. 더 자세한 정보는 inspect 사용
        2. IPAddress를 가져오려면 아래와 같이 씀
        3. `docker inspect mysql_container | grep IPAddress`
    3. bash로 접속 가능

    ```bash
    docker exec -i -t mysql_container bash

    // 여기서 mysql_container는 컨테이너 이름
    ```

    4. `mysql -u root -p` 로 비밀번호 입력해 mysql 확인 가능


2. yaml 파일로 설정
