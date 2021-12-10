# 이것이 MySQL이다(8.0)

- 2020-11-09 (Finish Chapter01)
- 2021-09-15 ~ 2021-09-27 (Finish Chapter02~06)
- 2021-10-22 ~ 2021-12-10 (Finish Chapter07~15)

강의 링크 -> <https://www.youtube.com/playlist?list=PLVsNizTWUw7Hox7NMhenT-bulldCp9HP9>

## 강의록

- [Chapter 01 DBMS 개요와 MySQL 소개](./summary/Chapter01.md)
  - 01-00 Orietation
  - 01-01 DBMS 개요
  - 01-02 DBMS 분류와 MySQL 소개
- [Chapter 02 MySQL 설치](./summary/Chapter02.md)
  - 02-01 MySQL 8.0 설치
  - 02-02 샘플 데이터베이스 생성, MySQL 제거
- [Chapter 03 MySQL 전체 운영 실습](./summary/Chapter03.md)
  - 03-01 데이터베이스 필수 용어
  - 03-02 데이터베이스 구축 절차 실습
  - 03-03 데이터베이스 개체 - 인덱스
  - 03-04 데이터베이스 개체 - 뷰, 저장프로시저, 트리거
  - 03-05 백업과 복원
  - 03-06 MySQL과 응용프로그램 연동
- [Chapter 04 데이터베이스 모델링](./summary/Chapter04.md)
  - 04-01 데이터베이스 모델링 개념
  - 04-02 데이터베이스 모델링 툴 실습
- [Chapter 05 데이터베이스 모델링](./summary/Chapter05.md)
  - 05-01 MySQL Workbench 사용법 - 관리툴
  - 05-02 MySQL Workbench 사용법 - 쿼리창
  - 05-03 외부 MySQL 서버 관리 - Linux 서버g
  - 05-04 사용자 관리
- [Chapter 06 SQL기본](./summary/Chapter06.md)
  - 06-01 SELECT FROM 문
  - 06-02 샘플데이터베이스(sqlDB)생성
  - 06-03 WHERE 절
  - 06-04 Group By, Having절
  - 06-05 Insert,Update,Delete 및 CTE
- [Chapter 07 SQL고급](./summary/Chapter07.md)
  - 07-01 데이터 형식, 변수
  - 07-02 데이터형 변환, 내장 함수
  - 07-03 영화사이트(대용량 데이터) 구축 실습
  - 07-04 피벗, JSON
  - 07-05 조인 개념과 Inner Join
  - 07-06 Outer, Cross, Self Join 및 Union 등
  - 07-07 SQL 프로그래밍
- [Chapter 08 테이블과 뷰](./summary/Chapter08.md)
  - 08-01 Workbench에서 테이블 생성
  - 08-02 SQL문으로 테이블 생성
  - 08-03 제약조건(기본키, 외래키)
  - 08-04 제약조건(Unique, Check 등) 및 테이블 압축, 임시테이블
  - 08-05 테이블 삭제, 수정 및 제약조건 전체 실습
  - 08-06 뷰의 개념과 실습
  - 08-07 테이블스페이스
- [Chapter 09 인덱스](./summary/Chapter09.md)
  - 09-01 인덱스의 개념
  - 09-02 자동으로 생성되는 인덱스
  - 09-03 인덱스의 내부작동 1
  - 09-04 인덱스의 내부작동 2
  - 09-05 인덱스 실습과 결론
- [Chapter 10 스토어드 프로그램](./summary/Chapter10.md)
  - 10-01 스토어드 프로시저
  - 10-02 스토어드 함수, 커서
  - 10-03 트리거 개념과 실습
  - 10-04 트리거 임시 테이블, 중첩 트리거
- [Chapter 11 전체 텍스트검색과 파티션](./summary/Chapter11.md)
  - 11-01 전체 텍스트 검색 개념과 실습
  - 11-02 파티션 개념과 실습
- [Chapter 12 PHP 기본](./summary/Chapter12.md)
  - 12-01 웹 개발환경 구축
  - 12-02 HTML 태그, PHP 기초문법
  - 12-03 PHP 문법 및 내장 함수
  - 12-04 HTML과 PHP의 관계
- [Chapter 13 PHP와 MySQL 연동](./summary/Chapter13.md)
  - 13-01 기본 연동 방법
  - 13-02 회원 관리 시스템 구현
- [Chapter 14 공간 데이터](./summary/Chapter14.md)
  - 14-01 GIS 개념과 MySQL의 공간데이터
  - 14-02 공간 데이터 실습, 공간 함수
  - 14-03 GIS 응용 프로젝트
- [Chapter 15 파이썬과 MySQL 응용](./summary/Chapter15.md)
  - 15-01 파이썬 개발 환경 구축
  - 15-02 파이썬 기초 문법
  - 15-03 리스트(배열), 문자열, map함수
  - 15-04 함수, 윈도 프로그래밍
  - 15-05 파이썬과 MySQL 연동
  - 15-06 공간데이터 조회 응용프로그램

## Quick Start

### 데이터베이스, 테이블 조회

```sql
-- DATABASES 목록 보기
SHOW DATABASES;

-- 사용할 DB를 선택
USE db_name;

-- table 상세보기
SHOW TABLE STATUS;  

-- 해당 table의 field 타입 보기
DESCRIBE table_name; 
-- DESC table_name;
```

### 회원 테이블, 구매 테이블 만들기

```sql
-- DB 생성
DROP DATABASE IF EXISTS sqldb; -- 만약 sqldb가 존재하면 우선 삭제한다.
CREATE DATABASE sqldb;

-- DB 사용
USE sqldb;

-- 회원 테이블 생성
CREATE TABLE usertbl
( userID   CHAR(8) NOT NULL PRIMARY KEY, -- 사용자 아이디(PK)
  name     VARCHAR(10) NOT NULL, -- 이름
  birthYear   INT NOT NULL,  -- 출생년도
  addr    CHAR(2) NOT NULL, -- 지역(경기,서울,경남 식으로 2글자만입력)
  mobile1 CHAR(3), -- 휴대폰의 국번(011, 016, 017, 018, 019, 010 등)
  mobile2 CHAR(8), -- 휴대폰의 나머지 전화번호(하이픈제외)
  height     SMALLINT,  -- 키
  mDate     DATE  -- 회원 가입일
);

-- 구매 테이블 생성
CREATE TABLE buytbl -- 회원 구매 테이블(Buy Table의 약자)
(  num  INT AUTO_INCREMENT NOT NULL PRIMARY KEY, -- 순번(PK)
   userID   CHAR(8) NOT NULL, -- 아이디(FK)
   prodName  CHAR(6) NOT NULL, --  물품명
   groupName  CHAR(4)  , -- 분류
   price      INT  NOT NULL, -- 단가
   amount     SMALLINT  NOT NULL, -- 수량
   FOREIGN KEY (userID) REFERENCES usertbl(userID)
);

-- 데이터 삽입
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

### Error Code: 1175, MySQL sql_safe_updates 변수 수정

```sql
SET SQL_SAFE_UPDATES = 0;
```

## 마지막으로

이런 좋은 강의를 무료로 제공해주신 한빛미디어 및 우재남선생님께 감사드립니다.😊
