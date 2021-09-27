# Chapter 06 SQL기본

## 06-01 SELECT FROM 문

### SELECT

자주쓰는 기본 구조

```sql
SELECT select_expr  -- 선택하고 싶은 것들(ex. 이름, 날짜 등등)
FROM table_references  -- 참고할 테이블 이름
WHERE where_condition  -- 
GROUP BY col_name | expr | position
HAVING where_condition
ORDER BY col_name | expr | position
```

### USE

이 DB를 사용하겠습니다.

```sql
USE db_name;
```

### SHOW, DESCRIBE

목록 보여주기

```sql
SHOW DATABASES;  -- DATABASES 목록 보여주세요
SHOW TABLE STATUS;  -- table 상세보기로 보여주세요
DESCRIBE table_name;  -- 해당 table의 field 타입을 보여주세요
DESC table_name;
```

## 06-02 샘플데이터베이스(sqlDB)생성

[Code Link](https://cafe.naver.com/thisismysql/1155)

```sql
DROP DATABASE IF EXISTS sqldb; -- 만약 sqldb가 존재하면 우선 삭제한다.
CREATE DATABASE sqldb;

USE sqldb;
CREATE TABLE usertbl -- 회원 테이블
( userID   CHAR(8) NOT NULL PRIMARY KEY, -- 사용자 아이디(PK)
  name     VARCHAR(10) NOT NULL, -- 이름
  birthYear   INT NOT NULL,  -- 출생년도
  addr    CHAR(2) NOT NULL, -- 지역(경기,서울,경남 식으로 2글자만입력)
  mobile1 CHAR(3), -- 휴대폰의 국번(011, 016, 017, 018, 019, 010 등)
  mobile2 CHAR(8), -- 휴대폰의 나머지 전화번호(하이픈제외)
  height     SMALLINT,  -- 키
  mDate     DATE  -- 회원 가입일
);
CREATE TABLE buytbl -- 회원 구매 테이블(Buy Table의 약자)
(  num  INT AUTO_INCREMENT NOT NULL PRIMARY KEY, -- 순번(PK)
   userID   CHAR(8) NOT NULL, -- 아이디(FK)
   prodName  CHAR(6) NOT NULL, --  물품명
   groupName  CHAR(4)  , -- 분류
   price      INT  NOT NULL, -- 단가
   amount     SMALLINT  NOT NULL, -- 수량
   FOREIGN KEY (userID) REFERENCES usertbl(userID)
);

INSERT INTO usertbl VALUES('LSG', '이승기', 1987, '서울', '011', '1111111', 182, '2008-8-8');
INSERT INTO usertbl VALUES('KBS', '김범수', 1979, '경남', '011', '2222222', 173, '2012-4-4');
INSERT INTO usertbl VALUES('KKH', '김경호', 1971, '전남', '019', '3333333', 177, '2007-7-7');
INSERT INTO usertbl VALUES('JYP', '조용필', 1950, '경기', '011', '4444444', 166, '2009-4-4');
INSERT INTO usertbl VALUES('SSK', '성시경', 1979, '서울', NULL  , NULL      , 186, '2013-12-12');
INSERT INTO usertbl VALUES('LJB', '임재범', 1963, '서울', '016', '6666666', 182, '2009-9-9');
INSERT INTO usertbl VALUES('YJS', '윤종신', 1969, '경남', NULL  , NULL      , 170, '2005-5-5');
INSERT INTO usertbl VALUES('EJW', '은지원', 1972, '경북', '011', '8888888', 174, '2014-3-3');
INSERT INTO usertbl VALUES('JKW', '조관우', 1965, '경기', '018', '9999999', 172, '2010-10-10');
INSERT INTO usertbl VALUES('BBK', '바비킴', 1973, '서울', '010', '0000000', 176, '2013-5-5');
INSERT INTO buytbl VALUES(NULL, 'KBS', '운동화', NULL   , 30,   2);
INSERT INTO buytbl VALUES(NULL, 'KBS', '노트북', '전자', 1000, 1);
INSERT INTO buytbl VALUES(NULL, 'JYP', '모니터', '전자', 200,  1);
INSERT INTO buytbl VALUES(NULL, 'BBK', '모니터', '전자', 200,  5);
INSERT INTO buytbl VALUES(NULL, 'KBS', '청바지', '의류', 50,   3);
INSERT INTO buytbl VALUES(NULL, 'BBK', '메모리', '전자', 80,  10);
INSERT INTO buytbl VALUES(NULL, 'SSK', '책'    , '서적', 15,   5);
INSERT INTO buytbl VALUES(NULL, 'EJW', '책'    , '서적', 15,   2);
INSERT INTO buytbl VALUES(NULL, 'EJW', '청바지', '의류', 50,   1);
INSERT INTO buytbl VALUES(NULL, 'BBK', '운동화', NULL   , 30,   2);
INSERT INTO buytbl VALUES(NULL, 'EJW', '책'    , '서적', 15,   1);
INSERT INTO buytbl VALUES(NULL, 'BBK', '운동화', NULL   , 30,   2);

SELECT * FROM usertbl;
SELECT * FROM buytbl;
```

## 06-03 WHERE 절

### 특정한 조건만 조회하는 WHERE 절

```sql
USE sqldb;
SELECT * FROM usertbl;
SELECT * FROM usertbl WHERE name = '김경호';
SELECT userid, name FROM usertbl WHERE birthyear >= 1970 AND height >= 182;
SELECT userid, name FROM usertbl WHERE birthyear >= 1970 OR height >= 182;
```

### BETWEEN... AND, IN(), LIKE

```sql
-- BETWEEN... AND
SELECT name, height FROM usertbl WHERE height >= 180 AND height <= 183;
SELECT name, height FROM usertbl WHERE height BETWEEN 180 AND 183;

-- IN()
SELECT name, height FROM usertbl WHERE addr ='경남' OR addr ='전남' OR addr = '경북';
SELECT name, height FROM usertbl WHERE addr in ('경남', '전남', '경북');

-- LIKE
SELECT name, height FROM usertbl WHERE name LIKE '김%';
SELECT name, height FROM usertbl WHERE name LIKE '_종신';
```

### ANY, ALL, SOME, SubQuery

```sql
-- SubQuery(서브쿼리)
SELECT name, height FROM usertbl WHERE height > (SELECT height FROM usertbl WHERE Name = '김경호');

-- SubQuery가 2건인 경우
SELECT height FROM usertbl WHERE addr = '경남'; 
-- ANY 사용(조건 아무거나 만족)
SELECT name, height FROM usertbl WHERE height > ANY (SELECT height FROM usertbl WHERE addr = '경남');
-- ALL 사용(조건 모두 만족)
SELECT name, height FROM usertbl WHERE height > ALL (SELECT height FROM usertbl WHERE addr = '경남');

```

### 정렬하기 ORDER BY option: [DESC, ASC]

mysql 성능을 상당히 떨어뜨릴 소지가 있음으로 권장x

```sql
SELECT name, mDate FROM usertbl ORDER BY mDate;
SELECT name, mDate FROM usertbl ORDER BY mDate DESC;
SELECT name, mDate FROM usertbl ORDER BY height DESC, mDate ASC;
```

### 중복제거 DISTINCT

```sql
SELECT DISTINCT addr FROM usertbl;
```

### 출력 갯수 제한 LIMIT

```sql
USE employees;
SELECT emp_no, hire_date FROM employees
    ORDER BY hire_date ASC
    LIMIT 0, 5;  -- 0번째부터 5개
```

### 테이블 복사하기 CREATE TABLE ... SELECT

```sql
USE sqldb;
CREATE TABLE buytbl2 (SELECT * FROM buytbl);
SELECT * FROM buytbl2;

USE sqldb;
CREATE TABLE buytbl3 (SELECT userid, prodname FROM buytbl);
SELECT * FROM buytbl3;
```

주의! 제약조건(PK, FK 등)은 복사 x

## 06-04 Group BY, HAVING절

### GROUP BY

```sql
-- userid 당 구매 갯수 총 합
SELECT userid, SUM(amount) FROM buytbl GROUP BY userid;
SELECT userid AS '사용자 아이디', SUM(amount) AS '총 구매 개수' FROM buytbl GROUP BY userid;

-- userid 당 구매 갯수의 평균
SELECT userid, AVG(amount) FROM buytbl GROUP BY userid;
```

### 집계 함수

`GROUP BY`와 자주 사용

|   집계함수명    |          설명           |
| :-------------: | :---------------------: |
|      AVG()      |          평균           |
|      MIN()      |         최솟값          |
|      MAX()      |         최댓값          |
|     COUNT()     |        행의 갯수        |
| COUNT(DISTINCT) | 행의 갯수(중복은 1개로) |
|     STDEV()     |        표준편차         |
|   VAR_SAMP()    |          분산           |

```sql
SELECT AVG(amount) FROM buytbl;
SELECT AVG(amount) FROM buytbl GROUP BY userid;

-- 가장 큰사람, 작은사람의 이름과 키 출력
SELECT name, height FROM usertbl
    WHERE height = (SELECT MAX(height) FROM usertbl)
    OR  height = (SELECT MIN(height) FROM usertbl);
```

### HAVING

집계함수는 기본적으로 WHERE 절과 사용 불가, 이때 HAVING 절 사용

HAVING 절은 GROUP BY절 다음에 나와야 함

```sql
SELECT userid, SUM(price*amount) FROM buytbl GROUP BY userid HAVING SUM(price*amount) > 1000;
```

### ROLLUP

중간 합계나 총 합을 구하고 싶을 때

```sql
SELECT num, groupname, SUM(price*amount) FROM buytbl GROUP BY groupname, num WITH ROLLUP;
```

### SQL의 분류

1. DML(Data Manipulation Language)

    데이터 조작(UPDATE, INSERT, DELETE), 트렌잭션 발생(SELECT)
2. DDL(Data Definition Language)

    데이터베이스 개체 조작(CREATE, DROP, ALTER)
3. DCL(Data Control Language)

   사용자 권한 부여(GRANT/REVOKE/DENY)

## 06-05 Insert,Update,Delete 및 CTE

### INSERT문

형식

```sql
INSERT [INTO] 테이블[(열1, 열2, ...)] VALUES (값1, 값2, ...)
```

예시

```sql
USE sqldb;
CREATE TABLE testtbl(id int, username char(3), age int);

-- 모든 값을 다 넣을 때
INSERT INTO testbl VALUES (1, '홍길동', 25);

-- 특정 값만 넣을 때
INSERT INTO testtbl(id, username) VALUES (2, '설현'); 

-- 순서를 바꿀 때
INSERT INTO testtbl(username, id, age) VALUES ('삼이', 26, 3); 
```

### AUTO_INCREMENT

테이블을 만들 때 해당 값을 자동으로 생성하게 해줌

반드시 `PRIMARY KEY` or `UNIQUE` 값을 지정해야 하고 숫자 형식만 사용 가능

```sql
USE sqldb;
CREATE TABLE testtbl2 (
    -- 자동완성 생성
    id INT AUTO_INCREMENT PRIMARY KEY,
    username CHAR(3),
    age INT
);
insert into testtbl2 values (null, '지민', 25);
insert into testtbl2 values (null, '유나', 22);
insert into testtbl2 values (null, '유경', 21);
-- 1부터 1씩 증가하는 모습
SELECT * FROM testtbl2;

-- 킷값을 1000부터 3씩 증가하려면
CREATE TABLE testtbl3 (
    id INT AUTO_INCREMENT PRIMARY KEY,
    username CHAR(3),
    age INT
);

-- 자동생성 1000으로 설정
ALTER TABLE testtbl3 AUTO_INCREMENT=1000;
-- 증감을 3으로 설정
SET @@auto_increment_increment=3;
INSERT INTO testtbl3 VALUES(NULL, '나연', 20);
INSERT INTO testtbl3 VALUES(NULL, '정연', 18);
INSERT INTO testtbl3 VALUES(NULL, '모모', 19);
SELECT * FROM testtbl3;
```

### 대량의 샘플 데이터 생성

```sql
USE sqldb;
CREATE TABLE testtbl4 (
    id INT,
    fname VARCHAR(50),
    lname VARCHAR(50)
);
INSERT INTO testtbl4 SELECT emp_no, first_name, last_name FROM employees.employees;

-- 빠르게 만들기
CREATE TABLE testtbl5 (SELECT emp_no, first_name, last_name FROM employees.employees);
```

### 조건부 데이터 입력

키가 중복된 경우 에러가 발생합니다. 그 경우를 방지하기 위해 다음과 같이 사용합니다.

```sql
-- 에러가 나면 무시하고 넘어감
INSERT IGNORE INTO 테이블이름 VALUES('값1', '값2', '값3');

-- 중복이 발생하면 ON DUPLICATE 실행
-- 중복이 발생하지 않으면 VALUE로 실행
INSERT INTO 테이블이름 VALUES('값1', '값2', '값3')
ON DUPLICATE KEY UPDATE 열2='값2', 열3='값3';
```

### 데이터의 수정: Update

기본 형식

```sql
UPDATE 테이블이름
SET 열1='값1', 열2='값2', ...
WHERE 조건;
```

예시1

```sql
UPDATE testtbl4 SET lname = '없음' WHERE fname = 'Kyoichi';
```

업데이트 중에 **WHERE 절을 빼버리면 전체의 lname이 없음**으로 바뀌므로 **주의**해야합니다!

예시2

```sql
USE sqldb;
SHOW TABLES;
SELECT * FROM buytbl;
-- 가격이 모두 1.5배 인상되었으면?
UPDATE buytbl SET price = price * 1.5;
SELECT * FROM buytbl;
```

### 데이터의 삭제: DELETE FROM

기본 형식

```sql
DELETE FROM 테이블이름
WHERE 조건;
```

```sql
DELETE FROM testtbl4 WHERE fname='Aamer';
-- 상위 5개만 삭제
DELETE FROM testtbl4 WHERE fname='Aamer' LIMIT 5;

-- 대용량 삭제
DELETE FROM bigtbl1;
DROP TABLE bigtbl2;
TRUNCATE TABLE bigtbl3;
-- DELETE FROM: 한행씩 모두 삭제, 테이블은 남아있음
-- DROP TABLE: 테이블 자체를 삭제
-- TRUNCATE TABLE: 테이블 남기고 모든 행 삭제
-- 빠른 순위 DROP > TRUNCATE > DELETE
```

### WITH절과 CTE

CTE(Common Table Expression)란?

SELECT, INSERT, UPDATE, DELETE 또는 CREATE VIEW 문 하나의 실행 범위 내에서 정의되는 임시 결과 집합

기본 형식

```sql
-- CTE 생성
WITH CTE_테이블이름(열 이름)
AS
(
    <쿼리문>
)
-- 사용법
SELECT 열 이름 FROM CTE_테이블이름;

-- 사실은 한번에 실행됨!
```

기존에 SELECT로 불러온 데이터들이 하나의 TABLE 처럼 보이기 때문에 이것을 바로 테이블처럼 사용하기 위해 만든 느낌으로 이해!

SELECT 문으로 만든 결과

```sql
USE sqlDB;
SELECT userid AS 'userid', SUM(price*amount) AS 'total'
FROM buytbl GROUP BY userid;
```

그 결과를 CTE 테이블로 변환

```sql
WITH abc(userid, total)
AS
(SELECT userid, SUM(price*amount)
FROM buytbl GROUP BY userid)
-- 결과
SELECT * FROM abc ORDER BY total DESC;
```
