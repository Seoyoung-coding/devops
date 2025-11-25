DevOps 06: Docker 실습 - Spring Boot & MySQL
강의 개요
실전 Docker 실습을 통해 Spring Boot와 MySQL을 컨테이너로 운영하는 방법을 학습합니다. 사용자 정의 네트워크 생성부터 Dockerfile 작성, 이미지 빌드, Docker Hub 배포까지
  전체 워크플로우를 직접 실습하며 Docker 컨테이너 운영 능력을 완성합니다.

주요 내용:

사용자 정의 브리지 네트워크로 컨테이너 간 통신 구성
MySQL 컨테이너 실행 및 환경 설정
Spring Boot 애플리케이션 Dockerfile 작성 및 빌드
Docker Hub를 통한 이미지 배포 및 공유
Gradle bootBuildImage를 활용한 이미지 빌드 자동화

Spring Boot 컨테이너와 MySQL 컨테이너가 통신할 수 있는 전용 네트워크를 만듦


  1) 사용자 정의 브리지 네트워크 생성
!!!spring-net 이라는 네트워크를 만들었고, 여기에 MySQL 컨테이너를 넣는다!!!   

  
  2) mysql 컨테이너를 네트워크에 넣는다
docker run -d \
    --name mymysql \
    --network spring-net \
    -e MYSQL_ROOT_PASSWORD=rootpass \
    -e MYSQL_DATABASE=testdb \
    -e MYSQL_USER=testuser \
    -e MYSQL_PASSWORD=testpass \
    -p 3307:3306 \
    mysql:8

정상 초기화 확인 : [System] [MY-010931] [Server] /usr/sbin/mysqld: ready for connections.

  
  3) mysql 컨테이너 내부에서 접속 : docker exec -it mymysql mysql -u testuser -p
결과: Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 9
Server version: 8.4.7 MySQL Community Server - GPL


  4) Dokerfile 설정 (시간대 설정) : 한국 표준시(KST)를 사용하고 UTF-8 문자셋을 설정한 MySQL 이미지를 빌드
# (1) MySQL 공식 이미지
FROM mysql:8    #mysql을 사용하겠다

# (2) 환경 변수 설정
ENV TZ=Asia/Seoul \     #로그 시간, DB 저장 시간이 한국 기준으로 설정
    DEBIAN_FRONTEND=noninteractive      #tzdata는 시간대 패키지

# (3) tzdata 설치 (microdnf)
RUN microdnf -y install tzdata && \
    ln -snf /usr/share/zoneinfo/$TZ /etc/localtime && \
    echo $TZ > /etc/timezone && \
    microdnf clean all          #시간대 데이터 설치

# (4) MySQL 서버 실행 시 파라미터로 시간대 및 문자셋 설정
CMD ["mysqld", \
     "--character-set-server=utf8mb4", \    #서버 전체 문자셋을 가장 확장된 버전으로 지정
     "--collation-server=utf8mb4_unicode_ci", \
     "--default-time-zone=+09:00"]

\q로 mysql에서 나오고 docker복귀

  
  5) 이미지 빌드 : docker build -t mymysql .

    
===> 여기까지 '한국 시간 + UTF-8 완전 지원 + 계정/DB 자동 설정된 MySQL 서버' 만든것!
MySQL 8 서버 + 한국 시간(Asia/Seoul) + UTF-8(utf8mb4) 기본 문자셋+ timezone(tzdata) 설치됨 + MySQL 실행 옵션+Oracle MySQL의 표준 설정 <- 포함된 '이미지' (=틀) 제작한 상황



    6) 
    
