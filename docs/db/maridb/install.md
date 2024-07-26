# Install

MariaDB의 기본 설정 파일을 확인하려면 보통 다음 경로에서 설정 파일을 찾을 수 있습니다:

- Linux: `/etc/mysql/my.cnf` 또는 `/etc/my.cnf`
- Windows: `C:\Program Files\MariaDB XX.X\data\my.ini` (XX.X는 설치된 버전 번호)

다음은 기본 설정 파일에서 중요한 설정 항목을 확인하는 방법입니다.

1. **설정 파일 열기:**
   ```bash
   sudo nano /etc/mysql/my.cnf
   ```

2. **주요 설정 항목:**

   - **서버 설정:**
     ```bash
     [mysqld]
     port = 3306
     datadir = /var/lib/mysql
     socket = /var/lib/mysql/mysql.sock
     user = mysql
     symbolic-links = 0
     ```

   - **클라이언트 설정:**
     ```bash
     [client]
     port = 3306
     socket = /var/lib/mysql/mysql.sock
     ```

   - **로그 파일 설정:**
     ```bash
     log_error = /var/log/mysql/error.log
     ```

   - **기타 유용한 설정:**
     ```bash
     max_connections = 151
     query_cache_size = 16M
     innodb_buffer_pool_size = 128M
     ```

3. **설정 적용 후 MariaDB 재시작:**
   ```bash
   sudo systemctl restart mariadb
   ```

또한 `my.cnf` 파일이 여러 위치에 있을 수 있으므로, MariaDB는 여러 설정 파일을 읽을 수 있습니다. 예를 들어:

- `/etc/my.cnf`
- `/etc/mysql/my.cnf`
- `/usr/local/etc/my.cnf`
- `~/.my.cnf`

이 파일들을 모두 확인하여 필요한 설정을 적용하면 됩니다.

필요한 추가 설정 항목이나 특정 설정에 대한 자세한 설명이 필요하시면 알려주세요.


## Maria DB Install

- Environment 
	- OS : Ubuntu 20.04
	- DB : MariaDB Server and Client 10.3

```bash
apt update
apt install -y mariadb-server
```

## Maria DB 접속

```bash
mysql -u root
Welcome to the MariaDB monitor.  Commands end with ; or \g.
Your MariaDB connection id is 39
Server version: 10.3.39-MariaDB-0ubuntu0.20.04.2 Ubuntu 20.04

Copyright (c) 2000, 2018, Oracle, MariaDB Corporation Ab and others.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

MariaDB [(none)]> help;

General information about MariaDB can be found at
```

> [!note] 접속시 'sudo mysql' 로도 접속이 가능함. 'mysql -u root -p' 옵션으로 접속하면 root의 패스워드를 입력해야 접속이 가능함. 


### Maria DB 타임 확인

Maria DB는 기본으로 system 시간을 따른다. 


1. **DB Timezone 설정 확인**
   ```bash
   MariaDB [(none)]> SELECT @@global.time_zone, @@session.time_zone; 
   +--------------------+---------------------+
   | @@global.time_zone | @@session.time_zone |
   +--------------------+---------------------+
   | SYSTEM             | SYSTEM              |
   +--------------------+---------------------+
   1 row in set (0.000 sec)
   ```

2. **현재 설정되어 있는 시간 확인**
```bash
   MariaDB [(none)]> select now();
   +---------------------+
   | now()               |
   +---------------------+
   | 2024-07-02 10:12:22 |
   +---------------------+
   1 row in set (0.000 sec)

   MariaDB [(none)]> Ctrl-C -- exit!
```


---

## 간단한 사용법  (정리 필요)

모든 호스트에서 접속 가능한 계정을 만들기 위해서는 계정을 생성할 때 호스트 부분을 `%`로 지정하면 됩니다. `%`는 모든 호스트를 의미합니다. 아래에 그 방법을 설명합니다.

### 모든 호스트에서 접속 가능한 계정 생성

1. **MariaDB에 접속**
   ```bash
   mysql -u root -p
   ```

2. **모든 호스트에서 접속 가능한 계정 생성:**
   ```sql
   CREATE USER 'username'@'%' IDENTIFIED BY 'password';
   ```

3. **계정에 권한 부여:**
   예를 들어, 특정 데이터베이스에 대한 모든 권한을 부여하려면:
   ```sql
   GRANT ALL PRIVILEGES ON database_name.* TO 'username'@'%';
   ```

**4. 권한 적용:**
   ```sql
   FLUSH PRIVILEGES;
   ```


MariaDB에서 데이터베이스, 사용자, 권한 및 테이블 관리를 위한 기본적인 가이드를 아래에 제공합니다.

### 데이터베이스 생성 및 관리

1. **데이터베이스 생성:**
   ```sql
   CREATE DATABASE dbname;
   ```

2. **데이터베이스 목록 보기:**
   ```sql
   SHOW DATABASES;
   ```

3. **데이터베이스 사용:**
   ```sql
   USE dbname;
   ```

4. **데이터베이스 삭제:**
   ```sql
   DROP DATABASE dbname;
   ```

### 사용자 생성 및 권한 관리

1. **사용자 생성:**
   ```sql
   CREATE USER 'username'@'host' IDENTIFIED BY 'password';
   ```

2. **사용자에게 권한 부여:**
   ```sql
   GRANT ALL PRIVILEGES ON dbname.* TO 'username'@'host';
   ```

3. **권한 적용:**
   ```sql
   FLUSH PRIVILEGES;
   ```

4. **사용자 권한 확인:**
   ```sql
   SHOW GRANTS FOR 'username'@'host';
   ```

5. **사용자 삭제:**
   ```sql
   DROP USER 'username'@'host';
   ```

### 테이블 생성 및 관리

1. **테이블 생성:**
   ```sql
   CREATE TABLE tablename (
       id INT AUTO_INCREMENT PRIMARY KEY,
       column1 VARCHAR(100),
       column2 INT,
       column3 DATE
   );
   ```

2. **테이블 목록 보기:**
   ```sql
   SHOW TABLES;
   ```

3. **테이블 구조 보기:**
   ```sql
   DESCRIBE tablename;
   ```

4. **테이블 데이터 삽입:**
   ```sql
   INSERT INTO tablename (column1, column2, column3)
   VALUES ('value1', value2, 'value3');
   ```

5. **테이블 데이터 조회:**
   ```sql
   SELECT * FROM tablename;
   ```

6. **테이블 데이터 업데이트:**
   ```sql
   UPDATE tablename
   SET column1 = 'new_value'
   WHERE id = 1;
   ```

7. **테이블 데이터 삭제:**
   ```sql
   DELETE FROM tablename
   WHERE id = 1;
   ```

8. **테이블 삭제:**
   ```sql
   DROP TABLE tablename;
   ```

### 기타 유용한 명령어

1. **컬럼 추가:**
   ```sql
   ALTER TABLE tablename
   ADD column4 VARCHAR(50);
   ```

2. **컬럼 삭제:**
   ```sql
   ALTER TABLE tablename
   DROP COLUMN column4;
   ```

3. **인덱스 생성:**
   ```sql
   CREATE INDEX idx_column1 ON tablename (column1);
   ```

4. **인덱스 삭제:**
   ```sql
   DROP INDEX idx_column1 ON tablename;
   ```

이 가이드는 기본적인 MariaDB 관리 작업을 다루고 있으며, 필요에 따라 명령어를 조합하여 사용할 수 있습니다. 추가로 필요한 내용이 있거나 더 상세한 설명이 필요하다면 알려주세요.

### 예제

1. **MariaDB에 접속:**
   ```bash
   mysql -u root -p
   ```

2. **사용자 생성:**
   ```sql
   CREATE USER 'myuser'@'%' IDENTIFIED BY 'mypassword';
   ```

3. **모든 데이터베이스에 대한 모든 권한 부여:**
   ```sql
   GRANT ALL PRIVILEGES ON *.* TO 'myuser'@'%';
   ```

4. **권한 적용:**
   ```sql
   FLUSH PRIVILEGES;
   ```

이 명령어들을 실행하면 `myuser`라는 사용자가 모든 호스트에서 `mypassword`를 사용하여 접속할 수 있게 됩니다. 또한, 이 사용자는 모든 데이터베이스와 테이블에 대해 모든 권한을 갖게 됩니다.

추가로 필요한 정보나 특정 설정에 대한 문의가 있으면 말씀해 주세요.


MariaDB에서 사용자 목록을 확인하려면 `mysql` 데이터베이스의 `user` 테이블을 조회하면 됩니다. 다음 SQL 명령어를 사용하여 사용자 목록을 확인할 수 있습니다.

```sql
SELECT User, Host FROM mysql.user;
```

이 명령어를 실행하면 현재 MariaDB 서버에 등록된 모든 사용자 계정과 그들이 연결될 수 있는 호스트를 보여줍니다.

### 예제

1. MariaDB에 접속합니다:
   ```bash
   mysql -u root -p
   ```

2. 데이터베이스를 선택하고 사용자 목록을 조회합니다:
   ```sql
   USE mysql;
   SELECT User, Host FROM user;
   ```

이 명령어는 다음과 같은 형식으로 결과를 반환합니다:

| User      | Host      |
|-----------|-----------|
| root      | localhost |
| myuser    | localhost |
| otheruser | %         |

이 표는 각 사용자 계정과 그 계정이 허용되는 호스트를 보여줍니다. `%`는 모든 호스트를 의미합니다. 

추가로 필요한 정보나 명령어가 있으면 말씀해 주세요.


MariaDB 10.3에 대한 구체적인 예제와 가이드를 제공합니다.

### MariaDB 10.3에서 데이터베이스, 사용자, 권한, 테이블 관리

#### 데이터베이스 생성 및 관리

1. **데이터베이스 생성:**
   ```sql
   CREATE DATABASE mydb CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci;
   ```

2. **데이터베이스 목록 보기:**
   ```sql
   SHOW DATABASES;
   ```

3. **데이터베이스 사용:**
   ```sql
   USE mydb;
   ```

4. **데이터베이스 삭제:**
   ```sql
   DROP DATABASE mydb;
   ```

#### 사용자 생성 및 권한 관리

1. **사용자 생성:**
   ```sql
   CREATE USER 'myuser'@'localhost' IDENTIFIED BY 'mypassword';
   ```

2. **사용자에게 모든 권한 부여:**
   ```sql
   GRANT ALL PRIVILEGES ON mydb.* TO 'myuser'@'localhost';
   ```

3. **특정 권한 부여:**
   ```sql
   GRANT SELECT, INSERT, UPDATE, DELETE ON mydb.* TO 'myuser'@'localhost';
   ```

4. **권한 적용:**
   ```sql
   FLUSH PRIVILEGES;
   ```

5. **사용자 권한 확인:**
   ```sql
   SHOW GRANTS FOR 'myuser'@'localhost';
   ```

6. **사용자 삭제:**
   ```sql
   DROP USER 'myuser'@'localhost';
   ```

#### 테이블 생성 및 관리

1. **테이블 생성:**
   ```sql
   CREATE TABLE mytable (
       id INT AUTO_INCREMENT PRIMARY KEY,
       name VARCHAR(100),
       age INT,
       created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
   ) ENGINE=InnoDB;
   ```

2. **테이블 목록 보기:**
   ```sql
   SHOW TABLES;
   ```

3. **테이블 구조 보기:**
   ```sql
   DESCRIBE mytable;
   ```

4. **테이블 데이터 삽입:**
   ```sql
   INSERT INTO mytable (name, age)
   VALUES ('Alice', 30);
   ```

5. **테이블 데이터 조회:**
   ```sql
   SELECT * FROM mytable;
   ```

6. **테이블 데이터 업데이트:**
   ```sql
   UPDATE mytable
   SET age = 31
   WHERE name = 'Alice';
   ```

7. **테이블 데이터 삭제:**
   ```sql
   DELETE FROM mytable
   WHERE name = 'Alice';
   ```

8. **테이블 삭제:**
   ```sql
   DROP TABLE mytable;
   ```

#### 기타 유용한 명령어

1. **컬럼 추가:**
   ```sql
   ALTER TABLE mytable
   ADD email VARCHAR(100);
   ```

2. **컬럼 삭제:**
   ```sql
   ALTER TABLE mytable
   DROP COLUMN email;
   ```

3. **인덱스 생성:**
   ```sql
   CREATE INDEX idx_name ON mytable (name);
   ```

4. **인덱스 삭제:**
   ```sql
   DROP INDEX idx_name ON mytable;
   ```

이 명령어들은 MariaDB 10.3에서 기본적인 데이터베이스, 사용자, 권한 및 테이블 관리를 수행하는 데 유용합니다. 추가로 특정 기능이나 더 자세한 설명이 필요하다면 언제든지 질문해주세요.

## Sample DB를 이용한 실습가이드

튜토리얼용으로 사용될 샘플 데이터베이스로 MySQL의 `sakila` 샘플 데이터베이스가 많이 사용됩니다. `sakila` 데이터베이스는 영화 대여점을 모델로 한 스키마와 데이터가 포함되어 있어 다양한 SQL 쿼리 예제에 적합합니다. 아래는 `sakila` 샘플 데이터베이스를 MariaDB에 설치하고 사용하는 방법에 대한 가이드입니다.

### `sakila` 샘플 데이터베이스 설치 및 사용 가이드

#### 1. `sakila` 데이터베이스 다운로드

먼저, `sakila` 샘플 데이터베이스의 SQL 스크립트를 다운로드합니다. 공식 MySQL 웹사이트나 GitHub에서 다운로드할 수 있습니다. 예를 들어, [GitHub repository](https://github.com/jOOQ/sakila)에서 다운로드합니다.

#### 2. MariaDB에 데이터베이스 설치

다운로드한 SQL 스크립트를 MariaDB에 설치합니다. 스크립트 파일은 보통 `sakila-schema.sql`과 `sakila-data.sql`로 나누어져 있습니다.

1. **MariaDB에 접속:**
   ```bash
   mysql -u root -p
   ```

2. **데이터베이스 및 스키마 설치:**
   ```sql
   SOURCE /path/to/sakila-schema.sql;
   ```

3. **데이터 삽입:**
   ```sql
   SOURCE /path/to/sakila-data.sql;
   ```

#### 3. `sakila` 데이터베이스 구조

`sakila` 데이터베이스에는 여러 개의 테이블이 포함되어 있습니다. 주요 테이블과 그 설명은 다음과 같습니다:

- **actor**: 배우 정보 (actor_id, first_name, last_name, last_update)
- **film**: 영화 정보 (film_id, title, description, release_year, language_id, rental_duration, rental_rate, length, replacement_cost, rating, special_features, last_update)
- **inventory**: 재고 정보 (inventory_id, film_id, store_id, last_update)
- **rental**: 대여 정보 (rental_id, rental_date, inventory_id, customer_id, return_date, staff_id, last_update)
- **customer**: 고객 정보 (customer_id, store_id, first_name, last_name, email, address_id, active, create_date, last_update)

#### 4. 예제 쿼리

1. **모든 배우 목록 조회:**
   ```sql
   SELECT * FROM actor;
   ```

2. **특정 배우의 영화 목록 조회:**
   ```sql
   SELECT film.title
   FROM film
   INNER JOIN film_actor ON film.film_id = film_actor.film_id
   INNER JOIN actor ON film_actor.actor_id = actor.actor_id
   WHERE actor.first_name = 'PENELOPE' AND actor.last_name = 'GUINESS';
   ```

3. **대여 기록 조회:**
   ```sql
   SELECT rental.rental_id, rental.rental_date, customer.first_name, customer.last_name
   FROM rental
   INNER JOIN customer ON rental.customer_id = customer.customer_id
   ORDER BY rental.rental_date DESC
   LIMIT 10;
   ```

4. **영화 재고 조회:**
   ```sql
   SELECT film.title, COUNT(inventory.inventory_id) AS stock
   FROM film
   INNER JOIN inventory ON film.film_id = inventory.film_id
   GROUP BY film.title
   HAVING stock > 0;
   ```

이 가이드는 `sakila` 샘플 데이터베이스를 사용하여 MariaDB에서 다양한 SQL 쿼리를 연습할 수 있는 방법을 제공합니다. 추가로 필요한 정보나 특정 쿼리에 대한 설명이 필요하면 말씀해 주세요.

