Docker CLI 를 정리하였습니다. CLI 관련 학습한 내용이 있다면 여기에서 계속 업데이트할 계획입니다.

Docker 설치
https://myanjini.tistory.com/entry/%EC%9C%88%EB%8F%84%EC%9A%B0%EC%97%90-%EB%8F%84%EC%BB%A4-%EB%8D%B0%EC%8A%A4%ED%81%AC%ED%83%91-%EC%84%A4%EC%B9%98

"WSL2 installation is incomplete" 예외 발생 시

1. 파워셸을 관리자 권한으로 실행한 후 아래 명령어 입력
dism.exe /online /enable-feature /featurename:Microsoft-Windows-Subsystem-Linux /all /norestart
dism.exe /online /enable-feature /featurename:VirtualMachinePlatform /all /norestart

2. WSL2 리눅스 커널 설치 및 업데이트
https://wslstorestorage.blob.core.windows.net/wslblob/wsl_update_x64.msi

이미지 다운로드

# docker hub 에서 최신 버전 이미지 다운로드
$ docker pull [image-name]

# docker hub 에서 특정 버전 이미지 다운로드
$ docker pull [image-name]:[tag-name]

이미지 생성

# Dockerfile 이 있는 위치에서 이미지 생성
# -t : 이미지의 태그(버전). 명시하지 않는 경우 기본값 lastest
# Build Context : Dockerfile 이 위치한 경로. 상대경로 및 절대경로 모두 사용가능하다.
# 파일 지정은 -f 옵션으로 할 수 있다.
$ docker build -t <image-name>:<tag-name> <build-context-path>

# ex) 현재 위치에서 my-app:latest 생성
$ docker build -t my-app ./

# Dockerfile 지정하여 이미지 생성
$ docker build -t <image-name>:<tag-name> -f <Dockerfile-path> <build-context-path>

이미지 조회

# 다운 받은 이미지 조회
$ docker image ls

이미지 삭제

# 특정 이미지 삭제
$ docker image rm [image-name or image-id]...

# 실행 중이 아닌 컨테이너에서 사용하고 있는 이미지 강제 삭제하기
# 실행 중인 컨테이너의 이미지는 삭제할 수 없다.
$ docker image rm -f [image-name or image-id]...

# 시스템에 있는 모든 이미지의 ID 반환
$ docker images -q

# 컨테이너에서 사용되지 않고 있는 이미지만 삭제
$ docker image rm $(docker images -q)

# 실행 중이 아닌 컨테이너의 이미지 포함 전체 삭제
$ docker image rm -f $(docker images -q)

컨테이너 생성/실행
# 로컬에 이미지가 없는 경우 docker hub 에서 이미지를 자동으로 다운로드

# 최신 버전 이미지로 컨테이너 생성
$ docker create [image-name]

# 특정 버전 이미지로 컨테이너 생성
$ docker create [image-name]:[tag-name]

# 컨테이너 실행
$ docker start [container-name or container-id]...

# 최신 버전 이미지로 컨테이너 생성 및 실행
$ docker run [image-name]

# 특정 버전 이미지로 컨테이너 생성 및 실행
$ docker run [image-name]:[tag-name]

# -d : 백그라운드 실행
# -p : 포트 맵핑
# --name : 이미지 이름 지정
$ docker run -d -p [host-ip]:[container-ip] --name [container-name] [image-name]

컨테이너 조회

# 실행 중인 컨테이너 조회
$ docker ps

# 모든 컨테이너 조회
$ docker ps -a

# 컨테이너 로그 출력
$ docker logs [container-name or container-id]

# 컨테이너 로그 최근 n줄만 출력
$ docker logs --tail [row_amount] [container-name or container-id]

# 기존 로그와 함께 실시간 로그 출력
$ docker logs -f [container-name or container-id]

# 기존 로그 제외 실시간 로그 출력
$ docker logs --tail 0 -f [container-name or container-id]

컨테이너 접속

# 컨테이너 접속
$ docker exec -it [container-name or container-id] bash

컨테이너 중지/삭제

# 컨테이너 중지
$ docker stop [container-name or container-id]...

# 컨테이너 삭제
$ docker rm [container-name or container-id]...

# 중지된 컨테이너 전체 삭제
$ docker rm $(docker ps -qa)

# 컨테이너 중지 및 삭제
$ docker rm -f [container-name or container-id]...

# 컨테이너 전체 중지 및 삭제
$ docker rm -f $(docker ps -qa)

# 컨테이너 강제 중지
$ docker kill [container-name or container-id]...

컨테이너 볼륨

# 볼륨 지정
# 호스트 디렉토리가 이미 있는 경우 해당 디렉토리가 컨테이너 디렉토리를 덮어씌운다.
# 호스트 디렉토리가 없다면 호스트 디렉토리를 만들고 컨테이너 디렉토리의 파일들을 복사하여 호스트 디렉토리에 저장한다.
# 만약 호스트 디렉토리가 이미 존재하고, 컨테이너 디렉토리에는 애플리케이션 실행을 위한 필수 파일이 있어야 하는데, 덮어씌워서 필수 파일이 없는 상태가 된다면 예외가 발생할 것이다.
# 따라서 지정할 호스트 경로에는 필요한 파일을 모두 준비해놓아야 한다. 그렇지 않은 경우 해당 경로를 미리 만들지 말아야 한다.
$ docker run -v [host-absolute-path]:[container-absolute-path] [image-name]

# windows 에서 MySQL 실행
# 컨테이너 경로 : 공식문서를 그대로 참조할 경우, /var/lib/mysql 고정이다.
# -e : 환경변수를 추가한다. MYSQL_ROOT_PASSWORD 는 mysql 컨테이너 실행 시 필수 값이며, 예약된 환경변수는 공식문서에서 찾을 수 있다. 또한 처음으로 지정한 password 는 volume 을 통해 영속화될 수 있기 때문에 신중히 입력하거나 사후에 직접 mysql 명령어로 수정해야 한다.
$ docker run -d -p 3306:3306 --name mysql -e MYSQL_ROOT_PASSWORD=[password] -v C:\mysql\data:/var/lib/mysql mysql

도커 컴포즈

# compose.yml 파일을 기반으로 컨테이너들을 실행
$ docker compose up

# -d : 백그라운드 실행(기본 포어그라운드)
$ docker compose up -d

# --build : compose.yml 에 정의된 모든 서비스의 이미지를 새로 빌드
$ docker compose up --build

# compose.yml 에 정의된 이미지들을 다운로드하거나 갱신
$ docker compose pull

# compose 로 실행한 컨테이너들만 조회
$ docker compose ps

# -a : 실행 중인 컨테이너와 실행 중이지 않은 컨테이너를 모두 조회(기본 실행 중인 컨테이너만)
$ docker compose ps -a

# compose 로 실행한 컨테이너들의 로그만 조회
$ docker compose logs

# compose 로 실행한 컨테이너들을 종료
$ docker compose down

# compose 파일 지정하기
# -f : 파일을 지정함. 파일을 지정하여 실행한 경우 조회나 삭제를 할 때도 -f 옵션으로 파일을 지정해야 한다.
$ docker compose -f <file-name> up

# compose-prod.yml 의 프로세스 조회
$ docker compose -f compose-prod.yml ps

도커 스웜
# 준비사항 : 2개 이상의 인스턴스, port 번호 (2377, 4789, 7649) 개방
# 도커스웜 네트워크 : 클러스터 내 인스턴스들은 네크워크를 공유하고 있기에 어느 호스트로 서비스를 호출하더라도 동일하게 응답한다.
# 아쉽게도, replica 를 하나씩 끄고 다시 실행시키기 때문에 무중단 배포는 지원되지 않는다.

# 스웜 실행. 실행 시 매니저 노드로 지정됨
$ docker swarm init

# 스웜 종료
$ docker swarm leave

# 워커 노드로 참가. 스웜 실행 후 출력된 명령어를 복사하여 매니저 노드가 아닌 인스턴스에서 입력
$ docker swarm join --token xxx-yyy-zzz 1.2.3.4:2377

# 노드 조회
$ docker node ls

# 노드 삭제
$ docker node rm [node-id]

# 서비스 실행
$ docker service create -p [host-ip]:[container-ip] --name [container-name] --replicas [replica-amount] [image-name]

# 서비스 조회. 동일한 서비스는 한 번 출력된다.
$ docker service ls

# 서비스 로그 조회
$ docker service logs [service-id or service-name]

# 실행 중인 모든 태스크 조회. 복사본 개수만큼 모두 출력된다.
$ docker node ps

# 특정 서비스로 실행 중인 모든 태스크 조회
$ docker service ps [service-id or service-name]

# 서비스 종료. 컨테이너도 같이 종료된다.
$ docker service rm [service-id or service-name]

# 도커 컴포즈로 여러 서비스를 실행
$ docker stack deploy -c [compose-file-path] [stack-name]
$ docker stack deploy -c compose.yml project-name

# 서비스 스케일 아웃/인
$ docker service scale [service-id or service-name]=[replica-amount]

# 서비스 업데이트(롤링 업데이트)
$ docker service update --image [image-name]:[tag-name] [service-id or service-name]
