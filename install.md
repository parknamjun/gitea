
### os : Rocky 8, Linux
```
gitea|root@28a65bec36db:/># uname -a
Linux 28a65bec36db 5.10.124-linuxkit #1 SMP Thu Jun 30 08:19:10 UTC 2022 x86_64 x86_64 x86_64 GNU/Linux
```


1. docker start
```
gitea|root@28a65bec36db:/># docker run -d -it --privileged --name gitea -p 9080:8080 -p 20022:22 lanovia/rocky-8.7-ssh
```


2. yum update
``` 
gitea|root@28a65bec36db:/># yum update -y
```


3. db install
```
gitea|root@28a65bec36db:/># yum install mariadb-server -y
```


4. db service install
```
gitea|root@28a65bec36db:/># systemctl start mariadb

# 상태확인
gitea|root@28a65bec36db:/># systemctl status mariadb
● mariadb.service - MariaDB 10.3 database server
   Loaded: loaded (/usr/lib/systemd/system/mariadb.service; disabled; vendor preset: disabled)
   Active: active (running) since Thu 2023-05-11 10:22:57 KST; 5s ago
     Docs: man:mysqld(8)
           https://mariadb.com/kb/en/library/systemd/
  Process: 490 ExecStartPost=/usr/libexec/mysql-check-upgrade (code=exited, status=0/SUCCESS)
  Process: 358 ExecStartPre=/usr/libexec/mysql-prepare-db-dir mariadb.service (code=exited, status=0/SUCCESS)
  Process: 334 ExecStartPre=/usr/libexec/mysql-check-socket (code=exited, status=0/SUCCESS)

# symlink 추가
gitea|root@28a65bec36db:/># systemctl enable mariadb
Created symlink /etc/systemd/system/mysql.service → /usr/lib/systemd/system/mariadb.service.
Created symlink /etc/systemd/system/mysqld.service → /usr/lib/systemd/system/mariadb.service.
Created symlink /etc/systemd/system/multi-user.target.wants/mariadb.service → /usr/lib/systemd/system/mariadb.service.
```


5. database create
```
# 현재는 docker 내부에서만 사용하므로 root 계정의 비밀번호가 없음
gitea|root@28a65bec36db:/># mysql
MariaDB [(none)]> CREATE DATABASE gitea CHARACTER SET 'utf8mb4' COLLATE 'utf8mb4_unicode_ci';
MariaDB [(none)]> GRANT ALL ON gitea.* TO 'gitea'@'localhost' IDENTIFIED BY 'gitea';
MariaDB [(none)]> show databases;
+--------------------+
| Database           |
+--------------------+
| gitea              |
| information_schema |
| mysql              |
| performance_schema |
+--------------------+
```


6. gitea binary install
* ㄷ
