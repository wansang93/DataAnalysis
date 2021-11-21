# Chapter 08 테이블과 뷰

## 08-01 Workbench에서 테이블 생성

테이블을 생성할 때 **제약 조건(Constraint)** 을 잘 적용하여 만들어보자! For 무결성

```sql
-- 1. DB가 존재하면 삭제하기
DROP DATABASE IF EXISTS ShopDB;
DROP DATABASE IF EXISTS ModelDB;
DROP DATABASE IF EXISTS sqldb;
DROP DATABASE IF EXISTS tabledb;

-- 2. DB 만들기
CREATE DATABASE tabledb;

-- 3. tabledb에 buytbl 만들기
CREATE TABLE `tabledb`.`buytbl` (
  `num` INT NOT NULL AUTO_INCREMENT,
  `userid` CHAR(8) NOT NULL,
  `prodName` CHAR(6) NOT NULL,
  `groupName` CHAR(4) NULL,
  `price` INT NOT NULL,
  `amount` SMALLINT NOT NULL,
  PRIMARY KEY (`num`),
  FOREIGN KEY (userid) REFERENCES usertbl(userID)  
);

-- </실습 1> --
```

## 08-02 SQL문으로 테이블 생성

DB 생성 및 CREATE TABLE

```sql
-- <실습 2> --

-- 1. DB 삭제
DROP DATABASE tabledb;
CREATE DATABASE tabledb;

USE tabledb;
-- 2. 테이블 생성
DROP TABLE IF EXISTS buytbl, usertbl;
CREATE TABLE usertbl -- 회원 테이블
( userID  CHAR(8), -- 사용자 아이디
  name    VARCHAR(10), -- 이름
  birthYear   INT,  -- 출생년도
  addr    CHAR(2), -- 지역(경기,서울,경남 등으로 글자만 입력)
  mobile1  CHAR(3), -- 휴대폰의국번(011, 016, 017, 018, 019, 010 등)
  mobile2  CHAR(8), -- 휴대폰의 나머지 전화번호(하이픈 제외)
  height    SMALLINT,  -- 키
  mDate    DATE  -- 회원 가입일
);
CREATE TABLE buytbl -- 구매 테이블
(  num INT, -- 순번(PK)
   userid  CHAR(8),-- 아이디(FK)
   prodName CHAR(6), -- 물품명
   groupName CHAR(4) , -- 분류
   price     INT , -- 단가
   amount SMALLINT -- 수량
);

-- 3. 조건을 넣어서 테이블 생성하기
-- (NULL, NOT NULL), AUTO_INCREMENT, FOREIGN KEY, REFERNCES
USE tabledb;
DROP TABLE IF EXISTS buytbl, usertbl;
CREATE TABLE usertbl 
( userID  CHAR(8) NOT NULL PRIMARY KEY, 
  name    VARCHAR(10) NOT NULL, 
  birthYear   INT NOT NULL,  
  addr    CHAR(2) NOT NULL,
  mobile1 CHAR(3) NULL, 
  mobile2   CHAR(8) NULL, 
  height    SMALLINT NULL, 
  mDate    DATE NULL 
);
DROP TABLE IF EXISTS buytbl;
CREATE TABLE buytbl 
(  num INT AUTO_INCREMENT NOT NULL PRIMARY KEY, 
   userid  CHAR(8) NOT NULL ,
   prodName CHAR(6) NOT NULL,
   groupName CHAR(4) NULL,
   price     INT  NOT NULL,
   amount    SMALLINT  NOT NULL 
   , FOREIGN KEY(userid) REFERENCES usertbl(userID)
);
```

INSERT INTO TABLE

```sql
INSERT INTO usertbl VALUES('LSG', '이승기', 1987, '서울', '011', '1111111', 182, '2008-8-8');
INSERT INTO usertbl VALUES('KBS', '김범수', 1979, '경남', '011', '2222222', 173, '2012-4-4');
INSERT INTO usertbl VALUES('KKH', '김경호', 1971, '전남', '019', '3333333', 177, '2007-7-7');

INSERT INTO buytbl VALUES(NULL, 'KBS', '운동화', NULL, 30, 2);
INSERT INTO buytbl VALUES(NULL, 'KBS', '노트북', '전자', 1000, 1);
INSERT INTO buytbl VALUES(NULL, 'JYP', '모니터', '전자', 200, 1);

-- 추가 입력 
INSERT INTO usertbl VALUES('JYP', '조용필', 1950, '경기', '011', '4444444', 166, '2009-4-4');
INSERT INTO usertbl VALUES('SSK', '성시경', 1979, '서울', NULL  , NULL      , 186, '2013-12-12');
INSERT INTO usertbl VALUES('LJB', '임재범', 1963, '서울', '016', '6666666', 182, '2009-9-9');
INSERT INTO usertbl VALUES('YJS', '윤종신', 1969, '경남', NULL  , NULL      , 170, '2005-5-5');
INSERT INTO usertbl VALUES('EJW', '은지원', 1972, '경북', '011', '8888888', 174, '2014-3-3');
INSERT INTO usertbl VALUES('JKW', '조관우', 1965, '경기', '018', '9999999', 172, '2010-10-10');
INSERT INTO usertbl VALUES('BBK', '바비킴', 1973, '서울', '010', '0000000', 176, '2013-5-5');
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
```

## 08-03 제약조건(기본키, 외래키)

- 제약 조건을 통해 **무결성**을 지켜짐
- 조건을 만족 했을 때 입력되도록 제약하는 것
- 제약 조건에는 기본키, 외래키, 유니크, 체크, 널 허용 등이 있음

제약조건 확인 방법

```sql
DESCRIBE usertbl;
```

### PRIMARY KEY(기본키) 제약 조건

- 식별자(ID) 기능, NULL 비허용, PRI라고 함
- 예시: 주민의 주민번호, 회원가입 아이디

```sql
DROP TABLE IF EXISTS usertbl;
CREATE TABLE usertbl
( userID  CHAR(8) NOT NULL,
  name    VARCHAR(10) NOT NULL,
  birthYear   INT NOT NULL,
  -- PRIMARY KEY를 지정하면 키의 이름을 직접 지어줄 수 있음
  CONSTRAINT PRIMARY KEY PK_usertbl_userID (userID)
  -- 2개를 합쳐서 키로 만드는 경우
  -- CONSTRAINT PRIMARY KEY PK_usertbl_userID (userID, userName)
);

-- 이미 사용중인 Table에 PRIMARY KEY 걸기
ALTER TABLE userTbl
  ADD CONSTRAINT PK_userTBL_userID
    PRIMARY KEY (userID);
```

제품 코드, 제품 일렬 번호 각각을 합쳐서 키로 생성

```sql
ALTER TABLE prodTbl
  ADD CONSTRAINT PK_prodTbl_proCode_prodID
    PRIMARY KEY (prodCode, prodID);
```

### FOREIGN KEY(외래키) 제약 조건

- 두 테이블 사이의 관계를 선언함으로써 데이터의 무결성을 보장
- 외래키 테이블이 참조하는 기준 테이블의 열은 반드시 **Primary Key거나 Unique 제약 조건이 설정**되어야 함
- 예시: 회원만 구매 가능, 학생만 성적 조회 가능

외래키 생성하는 방법

```sql
DROP TABLE IF EXISTS buytbl, usertbl;
CREATE TABLE usertbl 
( userID  CHAR(8) NOT NULL PRIMARY KEY, 
  name    VARCHAR(10) NOT NULL, 
  birthYear   INT NOT NULL 
);

CREATE TABLE buytbl 
(  num INT AUTO_INCREMENT NOT NULL PRIMARY KEY,
   userID  CHAR(8) NOT NULL, 
   prodName CHAR(6) NOT NULL,
   -- 외래키(키이름) 참조 참조할테이블이름(열이름)
   FOREIGN KEY(userID) REFERENCES usertbl(userID)
);

DROP TABLE IF EXISTS buytbl;
CREATE TABLE buytbl 
(  num INT AUTO_INCREMENT NOT NULL PRIMARY KEY,
   userID  CHAR(8) NOT NULL, 
   prodName CHAR(6) NOT NULL,
   -- 외래키에 이름을 지정하는 방식
   CONSTRAINT FK_usertbl_buytbl FOREIGN KEY(userID) REFERENCES usertbl(userID)
);

-- 이미 사용중인 Table에 FOREIGN KEY 걸기
ALTER TABLE buyTBL
  ADD CONSTRAINT FK_userTBL_buyTBL
  FOREIGN KEY (userID)
  REFERENCES userTBL(userID);
```

외래키 옵션 중에 `ON DELETE CASCADE`, `ON UPDATE CASCADE`가 있음

기존 테이블에서 해당 튜플이 삭제되면 자동으로 외래키가 집힌 값들을 삭제하거나 업데이트 합니다.

```sql
-- 기존 외래키 삭제
ALTER TABLE buytbl
  DROP FOREIGN KEY FK_userTBL_buyTBL;
-- 외래키 새로 추가
ALTER TABLE buytbl
  ADD CONSTRAINT FK_userTBL_buyTBL
  FOREIGN KEY (userID)
  REFERENCES usertbl(userID)
  -- 참조값이 업데이트 되면 자동으로 업데이트
  ON UPDATE CASCADE;
```

## 08-04 제약조건(Unique, Check 등) 및 테이블 압축, 임시테이블

### UNIQUE 제약 조건

- 중복되지 않는 유일한 값을 입력, NULL 허용
- 기본키가 아닌 것들 중 유일해야 한 것들에 사용하기 유용
- 예시: 이메일, 전화번호

```sql
DROP TABLE IF EXISTS usertbl;
CREATE TABLE usertbl 
( userID    CHAR(8) NOT NULL PRIMARY KEY,
  name      VARCHAR(10) NOT NULL, 
  birthYear INT NOT NULL,  
  email     CHAR(30) NULL ,  
  CONSTRAINT AK_email UNIQUE (email)
);
```

### CHECK 제약 조건(8.0.16버전부터 지원)

- 입력할 때 데이터를 검사하는 기능
- 키에 음수 불가, 출생년도가 1900년 이후만 가능, 이름은 반드시 넣어야 함

```sql
-- 출생연도가 1900년 이후 그리고 2023년 이전, 이름은 반드시 넣어야 함.
DROP TABLE IF EXISTS usertbl;
CREATE TABLE usertbl 
( userID  CHAR(8) PRIMARY KEY,
  name    VARCHAR(10) , 
  birthYear  INT CHECK (birthYear >= 1900 AND birthYear <= 2023),
  mobile1 char(3) NULL, 
  CONSTRAINT CK_name CHECK (name IS NOT NULL)  
);

-- 휴대폰 국번 체크
ALTER TABLE usertbl
  ADD CONSTRAINT CK_mobile1
  CHECK (mobile1 IN ('010', '011', '016', '017', '018', '019'));
```

### DEFAULT 정의

- 값을 입력하지 않으면 자동으로 입력되게 함
- 예시: 국가를 자동으로 한국으로 하기

```sql
DROP TABLE IF EXISTS usertbl;
CREATE TABLE usertbl 
( userID    CHAR(8) NOT NULL PRIMARY KEY,  
  name      VARCHAR(10) NOT NULL, 
  birthYear INT NOT NULL DEFAULT -1,
  addr      CHAR(2) NOT NULL DEFAULT '서울',
  mobile1 CHAR(3) NULL, 
  mobile2 CHAR(8) NULL, 
  height  SMALLINT NULL DEFAULT 170, 
  mDate     DATE NULL
);

-- 이미 사용중인 Table에 DEFAULT 걸기
ALTER TABLE userTBL
  ALTER COLUMN birthYear SET DEFAULT -1;
ALTER TABLE userTBL
  ALTER COLUMN addr SET DEFAULT '한국';

-- 이미 DEFAULT 값이 설정중인 테이블에 데이터 넣기
-- default 문은 DEFAULT로 설정된 값을 자동 입력
INSERT INTO usertbl VALUES ('LHL', '이혜리', default, default, '011', '1234567', default, '2023.12.12');
-- 열이름이 명시되지 않으면 DEFAULT로 설정된 값을 자동 입력한다
INSERT INTO usertbl(userID, name) VALUES('KAY', '김아영');
-- 값이 직접 명기되면 DEFAULT로 설정된 값은 무시된다.
INSERT INTO usertbl VALUES ('WB', '원빈', 1982, '대전', '019', '9876543', 176, '2020.5.5');
SELECT * FROM usertbl;
```

### NULL 값 허용

- `NULL`은 NULL 허용, `NOT NULL`은 반드시 채워주세요
- NULL 저장 시에 CHAR(고정 문자)는 공간을 모두 차지, VARCHAR(가변 문자)는 공간 차지 x
- NULL 값을 많이 입력한다면 가변 길이의 데이터 형식을 사용 추천!

### 테이블 압축

데이터를 DB에 넣을 때 알아서 압축해주는 방법 사용

기본 방식

```sql
ROW_FORMAT=COMPRESSED
```

사원들의 이름을 압축해보고 결과 보기

```sql
-- DB 생성
CREATE DATABASE IF NOT EXISTS compressDB;
USE compressDB;

-- TABLE 생성
CREATE TABLE normalTBL( emp_no int , first_name varchar(14));
CREATE TABLE compressTBL( emp_no int , first_name varchar(14))
  ROW_FORMAT=COMPRESSED;

-- 데이터 입력
INSERT INTO normalTbl 
     SELECT emp_no, first_name FROM employees.employees;   
INSERT INTO compressTBL 
     SELECT emp_no, first_name FROM employees.employees;

-- 결과 보기(Data_length)
SHOW TABLE STATUS FROM compressDB;
```

### 임시 테이블

- 임시로 잠깐 사용되는 테이블
- MySQL의 재시작, SQL 클라이언트 종료하면 자동으로 삭제됨
- **현재 세션**에서만 존재
- 원본 테이블과 동일한 이름 사용 가능, CRUD는 임시 테이블이 우선시 됨
  - 그래서 원본 수정을 하고싶으면 제거를 해야 함
  - 이름을 같이하면 혼란을 주기 때문에 비추!

사용 방법

```sql
CREATE TEMPORARY TABLE [IF NOT EXISTS] 테이블이름
(열 정의 ...)
```

## 08-05 테이블 삭제, 수정 및 제약조건 전체 실습

### 열의 추가

```sql
USE tabledb;
ALTER TABLE usertbl
  ADD Fhomepage VARCHAR(30) FIRST  -- 제일 앞에 열 추가
  DEFAULT 'http://www.hanbit.co.kr'  -- 디폴트 값
  NULL;  -- Null 허용
```

열을 추가하면 기본적으로 가장 뒤에 추가, 순서 변경은 가장 뒤에 `AFTER <열이름>`, `FIRST` 추가

### 열의 삭제

```sql
ALTER TABLE usertbl
  DROP COLUMN mobile1;
```

제약 조건이 걸려있으면 삭제가 안됨

### 테이블 삭제

```sql
DROP TABLE 테이블이름;
```

### 테이블 수정 

```sql
-- 열 이름 및 데이터 형식 변경
ALTER TABLE usertbl
  CHANGE COLUMN name uName VARCHAR(20) NULL;

-- 기본 키 삭제
ALTER TABLE usertbl
  DROP PRIMARY KEY;
-- 제약 조건이 걸려있으면 삭제 불가능
-- 따라서 외래 키를 제거 후 기본키를 삭제해야 함
ALTER TABLE buytbl
  DROP FOREIGN KEY buytbl_ibfk_1;
```

### 전체 실습

```sql
USE tabledb;
DROP TABLE IF EXISTS buytbl, usertbl;

-- 제약 없는 기본값
CREATE TABLE usertbl 
( userID  CHAR(8), 
  name    VARCHAR(10),
  birthYear   INT,  
  addr    CHAR(2), 
  mobile1 CHAR(3), 
  mobile2   CHAR(8), 
  height    SMALLINT, 
  mDate    DATE 
);

-- 제약 없는 기본값, num만 자동 생성
CREATE TABLE buytbl 
(  num int AUTO_INCREMENT PRIMARY KEY,
   userid  CHAR(8),
   prodName CHAR(6),
   groupName CHAR(4),
   price     INT ,
   amount   SMALLINT
);

-- 데이터 삽입
INSERT INTO usertbl VALUES('LSG', '이승기', 1987, '서울', '011', '1111111', 182, '2008-8-8');
-- 김범수씨 생년을 모름
INSERT INTO usertbl VALUES('KBS', '김범수', NULL, '경남', '011', '2222222', 173, '2012-4-4');
-- 김경호씨 생년을 잘못 입력
INSERT INTO usertbl VALUES('KKH', '김경호', 1871, '전남', '019', '3333333', 177, '2007-7-7');
INSERT INTO usertbl VALUES('JYP', '조용필', 1950, '경기', '011', '4444444', 166, '2009-4-4');
INSERT INTO buytbl VALUES(NULL, 'KBS', '운동화', NULL , 30,   2);
INSERT INTO buytbl VALUES(NULL,'KBS', '노트북', '전자', 1000, 1);
INSERT INTO buytbl VALUES(NULL,'JYP', '모니터', '전자', 200,  1);
-- BBK는 회원이 아님
INSERT INTO buytbl VALUES(NULL,'BBK', '모니터', '전자', 200,  5);

-- UserId로 기본키 지정하기
ALTER TABLE usertbl
  ADD CONSTRAINT PK_usertbl_userID
  PRIMARY KEY (userID);

-- 확인하기
DESC usertbl;

-- buytbl 테이블의 userID를 usertbl의 userID 를 참조하여 외래키로 지정
ALTER TABLE buytbl
  ADD CONSTRAINT FK_usertbl_buytbl
  FOREIGN KEY (userID)
  REFERENCES usertbl (userID);
-- 오류 발생(기존 유저tbl에 ID는 BBK가 없기 때문에)

-- 아이디가 없는데 구매한 BBK 삭제
DELETE FROM buytbl WHERE userid = 'BBK';
-- 외래키 재 지정(성공)
ALTER TABLE buytbl
  ADD CONSTRAINT FK_usertbl_buytbl
  FOREIGN KEY (userID)
  REFERENCES usertbl (userID);

-- 오류 발생(BBK 아이디가 없어서)
INSERT INTO buytbl VALUES(NULL,'BBK', '모니터', '전자', 200,  5);

-- 외래키 지정하려면 다 삭제하고 다시 넣어야되? 불가능하자나
-- 그럼 외래키 잠시 비활성화(0) 하고 데이터 넣고 다시 활성화(1) 하자
SET foreign_key_checks = 0;
INSERT INTO buytbl VALUES(NULL, 'BBK', '모니터', '전자', 200,  5);
INSERT INTO buytbl VALUES(NULL, 'KBS', '청바지', '의류', 50,   3);
INSERT INTO buytbl VALUES(NULL, 'BBK', '메모리', '전자', 80,  10);
INSERT INTO buytbl VALUES(NULL, 'SSK', '책'    , '서적', 15,   5);
INSERT INTO buytbl VALUES(NULL, 'EJW', '책'    , '서적', 15,   2);
INSERT INTO buytbl VALUES(NULL, 'EJW', '청바지', '의류', 50,   1);
INSERT INTO buytbl VALUES(NULL, 'BBK', '운동화', NULL   , 30,   2);
INSERT INTO buytbl VALUES(NULL, 'EJW', '책'    , '서적', 15,   1);
INSERT INTO buytbl VALUES(NULL, 'BBK', '운동화', NULL   , 30,   2);
SET foreign_key_checks = 1;

-- 체크 제약조건으로 범위 설정
ALTER TABLE userTBL
  ADD CONSTRAINT CK_birthYear
  CHECK ( (birthYear >= 1900 AND birthYear <= 2023) AND (birthYear IS NOT NULL) );
-- 오류 발생(1891년생이 있어서, NULL 도 있어서)

-- 올바르게 고치기
UPDATE usertbl SET birthYear=1979 WHERE userID='KBS';
UPDATE usertbl SET birthYear=1971 WHERE userID='KKH';

-- 체크 제약조건 자동으로 발동
ALTER TABLE userTBL
  ADD CONSTRAINT CK_birthYear
  CHECK ( (birthYear >= 1900 AND birthYear <= 2023) AND (birthYear IS NOT NULL) );

-- 체크 제약조건 발동으로 2999년 떄문에 입력이 안됨
INSERT INTO usertbl VALUES('TKV', '태권뷔', 2999, '우주', NULL  , NULL , 186, '2023-12-12');

-- 정상적으로 구성된 테이블 만들기
INSERT INTO usertbl VALUES('SSK', '성시경', 1979, '서울', NULL  , NULL , 186, '2013-12-12');
INSERT INTO usertbl VALUES('LJB', '임재범', 1963, '서울', '016', '6666666', 182, '2009-9-9');
INSERT INTO usertbl VALUES('YJS', '윤종신', 1969, '경남', NULL  , NULL , 170, '2005-5-5');
INSERT INTO usertbl VALUES('EJW', '은지원', 1972, '경북', '011', '8888888', 174, '2014-3-3');
INSERT INTO usertbl VALUES('JKW', '조관우', 1965, '경기', '018', '9999999', 172, '2010-10-10');
INSERT INTO usertbl VALUES('BBK', '바비킴', 1973, '서울', '010', '0000000', 176, '2013-5-5');

-- ID 잘못 입력되서 바꾸려고 함
UPDATE usertbl SET userID = 'VVK' WHERE userID='BBK';
-- 오류 발생(PK, FK가 발동되어 있어서)

-- FK 비활성화해서 바꾸면 됨
SET foreign_key_checks = 0;
UPDATE usertbl SET userID = 'VVK' WHERE userID='BBK';
SET foreign_key_checks = 1;
-- FK 활성화

-- 구매와 유저 테이블 조인 -> 8건
SELECT B.userid, U.name, B.prodName, U.addr, CONCAT(U.mobile1, U.mobile2)  AS '연락처'
   FROM buytbl B
     INNER JOIN usertbl U
        ON B.userid = U.userid ;

-- 구매 테이블 -> 12건
-- 조인해서 4개가 사라진 이유는?
SELECT COUNT(*) FROM buytbl;

-- OUTER JOIN 으로 확인
SELECT B.userid, U.name, B.prodName, U.addr, CONCAT(U.mobile1, U.mobile2) AS '연락처'
   FROM buytbl B
     LEFT OUTER JOIN usertbl U
        ON B.userid = U.userid
   ORDER BY B.userid ;

-- 이름 정상으로 돌아옴
SET foreign_key_checks = 0;
UPDATE usertbl SET userID = 'BBK' WHERE userID='VVK';
SET foreign_key_checks = 1;

-- 원상 복귀를 위해 잠시 끊음
ALTER TABLE buytbl
  DROP FOREIGN KEY FK_usertbl_buytbl;

-- PK가 바끼면서 FK도 바뀌게 설정
ALTER TABLE buytbl 
  ADD CONSTRAINT FK_usertbl_buytbl
  FOREIGN KEY (userID)
  REFERENCES usertbl (userID)
  ON UPDATE CASCADE;  -- 이 부분에서 적용

-- 바뀌면 따라 바뀌는지 확인
UPDATE usertbl SET userID = 'VVK' WHERE userID='BBK';
SELECT B.userid, U.name, B.prodName, U.addr, CONCAT(U.mobile1, U.mobile2) AS '연락처'
   FROM buytbl B
     INNER JOIN usertbl U
        ON B.userid = U.userid
  ORDER BY B.userid;

-- 회원 탈퇴하면? 오류 발생!(FK가 설정되어 있어서)
DELETE FROM usertbl WHERE userID = 'VVK';

-- 다시 초기화
ALTER TABLE buytbl
  DROP FOREIGN KEY FK_usertbl_buytbl;
-- 같이 삭제되게 하기
ALTER TABLE buytbl 
  ADD CONSTRAINT FK_usertbl_buytbl
  FOREIGN KEY (userID)
  REFERENCES usertbl (userID)
  ON UPDATE CASCADE
  ON DELETE CASCADE;  -- 이 부분 추가

-- 잘 삭제됨
DELETE FROM usertbl WHERE userID = 'VVK';
SELECT * FROM buytbl ;

-- 체크 조건 삭제는 아무 문제없이 잘 삭제됨
ALTER TABLE usertbl
  DROP COLUMN birthYear ;
-- 결론: PK, FK는 삭제, 수정 시 영향을 미치기 때문에 ON CASCADE를 적절히 사용해야 함
```

## 08-06 뷰의 개념과 실습

### 뷰

뷰는 일반 사용자 입장에서 테이블과 동일

장점
- 보안의 도움이 됨
- 복잡한 쿼리를 단순화 할 수 있음, 매번 Select문 쓸 필요 없음

뷰를 만드는 방법

```sql
USE tabledb;
CREATE VIEW v_usertbl
AS
SELECT userid, name, addr FROM usertbl;
```

뷰 실습

```sql
USE sqlDB;

-- VIEW 생성
CREATE VIEW v_userbuytbl
AS
   SELECT U.userid AS 'USER ID', U.name AS 'USER NAME', B.prodName AS 'PRODUCT NAME', 
		U.addr, CONCAT(U.mobile1, U.mobile2) AS 'MOBILE PHONE'
      FROM usertbl U
	INNER JOIN buytbl B
	 ON U.userid = B.userid;

-- VIEW 조회
SELECT `USER ID`, `USER NAME` FROM v_userbuytbl; -- 주의! 백틱을 사용한다.

-- VIEW 수정
ALTER VIEW v_userbuytbl
AS
   SELECT U.userid AS '사용자 아이디', U.name AS '이름', B.prodName AS '제품 이름', 
		U.addr, CONCAT(U.mobile1, U.mobile2)  AS '전화 번호'
      FROM usertbl U
          INNER JOIN buytbl B
             ON U.userid = B.userid ;

-- 한글 이름은 추천 안함
SELECT `이름`,`전화 번호` FROM v_userbuytbl;

-- VIEW 삭제
DROP VIEW v_userbuytbl;

-- 기존에 VIEW가 존재하면 덮어씀(오류를 더 줄임)
USE sqlDB;
CREATE OR REPLACE VIEW v_usertbl
AS
	SELECT userid, name, addr FROM usertbl;

-- VIEW 테이블 조회
DESCRIBE v_usertbl;

-- VIEW를 어떻게 생성했는지 쿼리 자세히 보기
SHOW CREATE VIEW v_usertbl;

SELECT * FROM v_usertbl;
-- VIEW에서 값 바꾸기
UPDATE v_usertbl SET addr = '부산' WHERE userid='JKW' ;
-- 변경 확인
SELECT * FROM v_usertbl;

-- 오류 발생 -> 원본 Table에 특정 칼럼이 NOT NULL이 설정되어 있기 때문에
INSERT INTO v_usertbl(userid, name, addr) VALUES('KBM','김병만','충북') ;
-- 해결방법: 원본 테이블의 칼럼들을 NOT NULL 설정 해제
-- 해결방법의 문제점: 데이터 원본의 구조를 바꾸기 때문에 비추천
-- 왠만하면 데이터 삽입은 불가능!

-- 총액에 대한 그룹화
CREATE VIEW v_sum
AS
	SELECT userid AS 'userid', SUM(price*amount) AS 'total'  
	   FROM buytbl GROUP BY userid;

SELECT * FROM v_sum;

-- VIEWS의 스키마 보기
SELECT * FROM INFORMATION_SCHEMA.VIEWS
     WHERE TABLE_SCHEMA = 'sqldb' AND TABLE_NAME = 'v_sum';
-- IsUpdatable이 No로 확인

-- 177이상인 사람의 VIEW 생성
CREATE VIEW v_height177
AS
	SELECT * FROM usertbl WHERE height >= 177 ;

-- VIEW 조회
SELECT * FROM v_height177 ;

-- VIEW에서 177이하인 사람 삭제
DELETE FROM v_height177 WHERE height < 177 ;
-- VIEW에 변화가 없음

-- VIEW에서 177이하인 사람 삽입
INSERT INTO v_height177 VALUES('KBM', '김병만', 1977 , '경기', '010', '5555555', 158, '2023-01-01') ;
-- VIEW에 변화가 없음(조건이 만족하지 않아서 자동으로 입력 안함)

-- CHECK OPTION을 확인하고 VIEW에 넣으세요
ALTER VIEW v_height177
AS
	SELECT * FROM usertbl WHERE height >= 177
	    WITH CHECK OPTION ;

-- 155인 사람의 키를 넣으면 
INSERT INTO v_height177 VALUES('WDT', '서장훈', 2006 , '서울', '010', '3333333', 155, '2023-3-3') ;

-- 2개 이상의 테이블을 합친 뷰를 복합뷰라고 함
CREATE VIEW v_userbuytbl
AS
  SELECT U.userid, U.name, B.prodName, U.addr, CONCAT(U.mobile1, U.mobile2) AS mobile
   FROM usertbl U
      INNER JOIN buytbl B
         ON U.userid = B.userid ;

-- 복합뷰는 삽입 불가
INSERT INTO v_userbuytbl VALUES('PKL','박경리','운동화','경기','00000000000','2023-2-2');

-- VIEW가 살아있어도 원본 테이블을 지울 수 있음
DROP TABLE IF EXISTS buytbl, usertbl;

-- 원본 테이블을 지우면 VIEW는 조회가 안됨
SELECT * FROM v_userbuytbl;

-- VIEW 테이블들에 대한 정보 보기
CHECK TABLE v_userbuytbl;
```

## 08-07 테이블스페이스

테이블 스페이스는 별로 신경쓰지 않아도 되지만 대용량의 데이터를 다룰 때는 성능 향상을 위해 테이블스페이스에 대한 설정을 하는 것이 좋음

테이블스페이스란?
- 테이블이 실제로 저장되는 물리적인 공간을 말함(데이터베이스는 테이블이 저장되는 논리적 공간을 말함)
- 테이블을 생성할 때 별도의 테이블스페이스를 지정하지 않아서 시스템 테이블스페이스에 테이블이 저장됨
- 대용량 DB는 테이블스페이스를 따로 지정해서 관리하는게 좋음

### 테이블 데이터베이스

```sql
-- 테이블 당 하나의 파일 생성(기본값: ON)
SHOW VARIABLES LIKE 'innodb_file_per_table';

-- DB의 데이터 파일 경로
SHOW VARIABLES LIKE 'innodb_data_file_path';
-- ibdata1:12M:autoextend(데이터:데이터용량:자동확장)
-- 사실 MySQL Server 8.0\Data에 ibdata1가 있음

-- 3개의 TABLESPACE 만들기(확장명은 반드시 .idb로 할 것!)
CREATE TABLESPACE ts_a ADD DATAFILE 'ts_a.ibd';
CREATE TABLESPACE ts_b ADD DATAFILE 'ts_b.ibd';
CREATE TABLESPACE ts_c ADD DATAFILE 'ts_c.ibd';

-- sqldb 사용
USE sqldb;
CREATE TABLE table_a (id INT) TABLESPACE ts_a;

CREATE TABLE table_b (id INT);
ALTER TABLE table_b TABLESPACE ts_b;

CREATE TABLE table_c (SELECT * FROM employees.salaries);
ALTER TABLE table_c TABLESPACE ts_c;
-- 만약에 속도 문제가 생기면 아래와 같이 수행해야 함
-- Workbench에서 DBMS 연결의 타임아웃 시간이 설정되어서 시간이 오래걸리면 자동으로 끊음
```

속도 문제 해결법: `<Edit>` -> `<Preferences>` -> `<MySQL Session>` 에서 DBMS connection timeout을 0(무제한)으로 변경해야 함
