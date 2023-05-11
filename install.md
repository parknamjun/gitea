
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
* package설치 참조: https://docs.gitea.io/en-us/installation/install-from-package/
```
gitea|root@28a65bec36db:/># mkdir gitea_install
gitea|root@28a65bec36db:/gitea_install># yum install wget -y
gitea|root@28a65bec36db:/gitea_install># wget -O gitea https://dl.gitea.com/gitea/1.19.3/gitea-1.19.3-linux-amd64
gitea|root@28a65bec36db:/gitea_install># chmod +x gitea
gitea|root@28a65bec36db:/gitea_install># ls -l
total 122772
-rwxr-xr-x 1 root root 125716896 May  4 05:26 gitea

# git install
gitea|root@28a65bec36db:/gitea_install># yum install git -y
gitea|root@28a65bec36db:/gitea_install># git --version
git version 2.31.1

gitea|root@28a65bec36db:/gitea_install># useradd --system --shell /bin/bash --comment 'Git Version Control' --create-home --home /home/git git
gitea|root@28a65bec36db:/gitea_install># mv gitea /usr/bin/gitea
gitea|root@28a65bec36db:/gitea_install># gitea --version
Gitea version 1.19.3 built with GNU Make 4.1, go1.20.4 : bindata, sqlite, sqlite_unlock_notify

# create directory
gitea|root@28a65bec36db:/gitea_install># mkdir -p /var/lib/gitea/{custom,data,log}
gitea|root@28a65bec36db:/gitea_install># chown -R git:git /var/lib/gitea/
gitea|root@28a65bec36db:/gitea_install># chmod -R 750 /var/lib/gitea/
gitea|root@28a65bec36db:/gitea_install># mkdir /etc/gitea
gitea|root@28a65bec36db:/gitea_install># chown root:git /etc/gitea
gitea|root@28a65bec36db:/gitea_install># chmod 770 /etc/gitea
```

* 설치 후 속성을 변경해야 함
```
NOTE: /etc/gitea is temporarily set with write permissions for user git so that the web installer can write the configuration file. After the installation is finished, it is recommended to set permissions to read-only using:

*chmod 750 /etc/gitea
*chmod 640 /etc/gitea/app.ini
```

7. gitea service install
* service file : [https://docs.gitea.io/en-us/installation/linux-service/](https://github.com/go-gitea/gitea/blob/main/contrib/systemd/gitea.service)
```
# 위 링크 내용을 아래 위치에 작성
gitea|root@28a65bec36db:/etc/systemd/system># ls -l gitea.service
-rw-r--r-- 1 root root 2456 May 11 11:15 gitea.service

# ps command
gitea|root@28a65bec36db:/gitea_install># yum install procps -y
```
