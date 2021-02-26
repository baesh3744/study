MariaDB를 설치한다.
```
$ sudo apt update | sudo apt -y upgrade
$ sudo apt install mariadb-server
```

mysql_secure_installation을 통해 보안을 설정한다. (root 비밀번호 변경, anonymouse 계정 삭제 등)
```
$ sudo service mysql start
$ sudo mysql_secure_installation
```

초기 설정된 root의 패스워드 타입을 변경한다.
```
$ sudo mariadb -u root  // sudo를 사용하여 root 계정으로 mariadb에 접속한다.

MariaDB [(none)]> use mysql;

MariaDB [mysql]> select user, host, plugin from user;
+------+-----------+-------------+
| user | host      | plugin      |
+------+-----------+-------------+
| root | localhost | unix_socket |
+------+-----------+-------------+
1 row in set (0.000 sec)

MariaDB [mysql]> update user set plugin='mysql_native_password' where user='root';

MariaDB [mysql]> flush privileges;

MariaDB [mysql]> select user, host, plugin from user;
+------+-----------+-----------------------+
| user | host      | plugin                |
+------+-----------+-----------------------+
| root | localhost | mysql_native_password |
+------+-----------+-----------------------+
1 row in set (0.000 sec)
```

MariaDB에 접속한다.
```
$ mariadb -u root -p
```
