# gitea

* 참고 : https://docs.gitea.io/en-us/install-with-docker/

```
docker network create gitea-network
docker pull mysql
docker pull gitea/gitea

컨테이너 실행
docker run --name mysql-server -e MYSQL_ROOT_PASSWORD=root -d -p 3306:3306 --network gitea-network mysql:latest
docker run --name gitea-server -d -p 3000:3000 --network gitea-network gitea/gitea:latest

상태확인
docker network inspect gitea-network

네트워크 연결을 해야 컨테이너명으로 접근이 가능함
docker network connect gitea-network mysql-server
docker network connect gitea-network gitea-server

docker network disconnect bridge mysql-server
docker network disconnect bridge gigitea-servertea
```

```
DB설정
docker exec -it mysql-server bash
mysql -u root -p <root>

DB생성
mysql> CREATE DATABASE gitea;
Query OK, 1 row affected (0.35sec)
```

* gitea 설치
  * localhost:3000
  * DB Hostname mysql-server:3306
  
