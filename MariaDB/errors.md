**ERROR 1698 (28000): Access denied for user 'root'@'localhost'**
----------

> 초기 설정되어 있는 root 계정의 패스워드 타입때문에 발생한다.

```
>> sudo mariadb -u root  // sudo를 사용하여 root 계정으로 mariadb에 접속한다.

MariaDB [(none)]> use mysql;

MariaDB [mysql]> select user, host, plugin from user;
+------+-----------+-------------+
| user | host      | plugin      |
+------+-----------+-------------+
| root | localhost | unix_socket |
+------+-----------+-------------+
1 row in set (0.000 sec)
```
root의 plugin을 mysql_native_password로 변경해주면 일반적인 로그인이 가능하다.

```
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
