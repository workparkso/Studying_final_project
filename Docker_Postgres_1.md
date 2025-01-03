공부하면서 알게된 정보이며,
Docker와 Postgres가 따로 설계하는 것이 아니라, 연결되어있는 점, 이를 Docker에 Postgres 설치하는 방법이 있습니다.

---
참고자료 : 
https://blog.naver.com/towards-ai/223294506000
https://en.wikipedia.org/wiki/PostgreSQL

----


 Docker에서 PostgreSQL을 설치하고 실행하는 것은 매우 간단합니다. Docker를 사용하면 PostgreSQL을 컨테이너로 실행할 수 있어서, 시스템 환경에 영향을 주지 않으면서 손쉽게 데이터베이스를 관리할 수 있습니다. Docker를 사용해 PostgreSQL을 설정하는 방법을 아래에 설명합니다.

### 1. **Docker 설치 확인**
먼저, Docker가 설치되어 있는지 확인하세요. 터미널에서 다음 명령어를 실행하여 Docker가 설치되어 있는지 확인할 수 있습니다:

```bash
docker --version
```

설치되어 있지 않다면, [Docker 공식 웹사이트](https://www.docker.com/get-started)에서 Docker를 설치할 수 있습니다.

### 2. **PostgreSQL Docker 이미지 다운로드**
Docker Hub에서 공식 PostgreSQL 이미지를 사용할 수 있습니다. `docker pull` 명령어로 PostgreSQL 이미지를 다운로드합니다:

```bash
docker pull postgres
```

이 명령어는 `postgres`라는 이름의 최신 PostgreSQL 이미지를 다운로드합니다.

### 3. **PostgreSQL 컨테이너 실행**
PostgreSQL 컨테이너를 실행하기 위해서는 데이터베이스 이름, 사용자 이름, 비밀번호 등을 설정할 수 있습니다. 기본적인 명령어는 다음과 같습니다:

```bash
docker run --name my_postgres -e POSTGRES_USER=my_user -e POSTGRES_PASSWORD=my_password -e POSTGRES_DB=my_database -p 5432:5432 -d postgres
```

여기서:
- `--name my_postgres`는 컨테이너의 이름을 `my_postgres`로 설정합니다.
- `-e POSTGRES_USER=my_user`는 PostgreSQL의 사용자 이름을 `my_user`로 설정합니다.
- `-e POSTGRES_PASSWORD=my_password`는 사용자 비밀번호를 `my_password`로 설정합니다.
- `-e POSTGRES_DB=my_database`는 기본 데이터베이스 이름을 `my_database`로 설정합니다.
- `-p 5432:5432`는 로컬 머신의 5432 포트를 Docker 컨테이너의 5432 포트에 연결합니다. 이 포트는 PostgreSQL의 기본 포트입니다.
- `-d`는 백그라운드에서 컨테이너를 실행하도록 합니다.
- `postgres`는 Docker 이미지 이름입니다.

### 4. **컨테이너 상태 확인**
컨테이너가 제대로 실행되고 있는지 확인하려면 다음 명령어를 사용하여 실행 중인 컨테이너 목록을 확인할 수 있습니다:

```bash
docker ps
```

이 명령어는 현재 실행 중인 Docker 컨테이너 목록을 보여줍니다. `my_postgres`라는 이름의 컨테이너가 목록에 나타나면 성공적으로 실행된 것입니다.

### 5. **PostgreSQL에 접속하기**
PostgreSQL에 접속하려면 `docker exec` 명령어를 사용하여 PostgreSQL 컨테이너 내에 접속할 수 있습니다. 예를 들어:

```bash
docker exec -it my_postgres psql -U my_user -d my_database
```

- `-it`는 터미널을 연결하는 옵션입니다.
- `psql`은 PostgreSQL의 명령줄 클라이언트입니다.
- `-U my_user`는 PostgreSQL에 접속할 사용자 이름을 지정합니다.
- `-d my_database`는 접속할 데이터베이스 이름을 지정합니다.

이 명령어로 PostgreSQL 데이터베이스에 접속하면 SQL 명령어를 실행할 수 있습니다.

### 6. **데이터 영속성**
Docker 컨테이너는 기본적으로 상태가 유지되지 않기 때문에, 데이터베이스의 데이터를 지속적으로 저장하려면 **볼륨(volume)**을 사용해야 합니다. PostgreSQL 데이터를 호스트 시스템에 저장하려면 다음과 같이 볼륨을 추가하여 실행할 수 있습니다:

```bash
docker run --name my_postgres -e POSTGRES_USER=my_user -e POSTGRES_PASSWORD=my_password -e POSTGRES_DB=my_database -p 5432:5432 -v pg_data:/var/lib/postgresql/data -d postgres
```

여기서 `-v pg_data:/var/lib/postgresql/data`는 `pg_data`라는 Docker 볼륨을 사용하여 PostgreSQL 데이터베이스 파일을 저장하는 경로를 지정합니다. 이렇게 하면 컨테이너가 재시작되거나 삭제되더라도 데이터는 유지됩니다.

### 7. **Docker Compose 사용 (선택사항)**
여러 서비스를 관리할 때 Docker Compose를 사용하면 유용합니다. 예를 들어, `docker-compose.yml` 파일을 만들어 PostgreSQL을 쉽게 설정하고 관리할 수 있습니다.

다음은 `docker-compose.yml` 파일의 예입니다:

```yaml
version: '3'
services:
  postgres:
    image: postgres
    environment:
      POSTGRES_USER: my_user
      POSTGRES_PASSWORD: my_password
      POSTGRES_DB: my_database
    ports:
      - "5432:5432"
    volumes:
      - pg_data:/var/lib/postgresql/data
volumes:
  pg_data:
```

이 파일을 사용하면 `docker-compose up` 명령어로 PostgreSQL 서비스를 시작할 수 있습니다.

```bash
docker-compose up -d
```

이 방법을 사용하면 여러 서비스를 정의하고 쉽게 관리할 수 있습니다.

### 8. **PostgreSQL 종료 및 삭제**
PostgreSQL 컨테이너를 종료하려면 다음 명령어를 사용합니다:

```bash
docker stop my_postgres
```

컨테이너를 완전히 삭제하려면:

```bash
docker rm my_postgres
```

컨테이너와 함께 생성된 볼륨도 삭제하려면:

```bash
docker volume rm pg_data
```

---

이러한 방법을 통해 Docker에서 PostgreSQL을 쉽게 설정하고 실행할 수 있습니다.