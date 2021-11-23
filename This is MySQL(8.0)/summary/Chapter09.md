# Chapter 09 인덱스

## 09-01 인덱스의 개념

인덱스란 책의 찾아보기 같은 기능

- 장점
  - 검색속도가 무척 빨라질 수 있음(단 항상 그런건 아님)
  - 해당 쿼리의 부하가 줄어들어 시스템 전체의 성능 향상
- 단점
  - DB 공간을 차지해 추가 공간이 필요(약 10% 정도)
  - 처음 인덱스를 생성 시 시간이 많이 소요
  - 데이터 변경 작업이 자주 일어날 경우 성능이 나빠질 수도 있음

## 09-02 자동으로 생성되는 인덱스

인덱스의 종류
- 클러스터형 인덱스(Clustered Index)
  - 순서대로 정렬, 테이블당 한 개만 생성(PK 같은 느낌)
  - 행 데이터를 해당 클러스터형 인덱스에 맞춰 자동 정렬
- 보조 인덱스(Secondary Index)
  - 다른 DBMS에서는 비클러스터형 인덱스(Nonclustered Index)로 나누기도 함

데이터베이스 튜닝
- 튜닝이란 SQL 서버가 기존보다 더욱 좋은 성능을 내도록 하는 전반적인 방법론
- 응답시간(Response Time): 요청한 작업이 응답하기 까지 걸리는 시간
- 서비스 시간(Service Time): 작업 처리하는 전체 시간
- 처리량(Throughput): 시스템에서 주어진 시간 안에 얼마나 많은 작업을 할 수 있는지

### 자동으로 생성되는 인덱스

- 1순위: **PRIMARY KEY** 생성시 자동으로 **클러스터 인덱스** 생성
- 2순위: **Unique NOT NULL** 생성시 자동으로 **클러스터 인덱스** 생성
- **Unique** 생성시 자동으로 **보조 인덱스** 생성
- **클러스터 인덱스**는 자동으로 정렬

```sql
CREATE TABLE  tbl5
  ( a INT UNIQUE NOT NULL,
    -- PRIMARY KEY만 없었으면 a는 Cluster Index임
    b INT UNIQUE,
    c INT UNIQUE,
    d INT PRIMARY KEY  -- Cluster Index
  );

SHOW INDEX FROM tbl5;
-- Key_name이 PRIMARY로 지정되면 Cluster Index임

-- testdb 생성
CREATE DATABASE IF NOT EXISTS testdb;

-- testdb 사용
USE testdb;

-- usertbl 생성 및 정의
DROP TABLE IF EXISTS usertbl;
CREATE TABLE usertbl 
( userID  char(8) NOT NULL PRIMARY KEY, 
  name    varchar(10) NOT NULL,
  birthYear   int NOT NULL,
  addr    nchar(2) NOT NULL 
);

-- usertbl 값 입력
INSERT INTO usertbl VALUES('LSG', '이승기', 1987, '서울');
INSERT INTO usertbl VALUES('KBS', '김범수', 1979, '경남');
INSERT INTO usertbl VALUES('KKH', '김경호', 1971, '전남');
INSERT INTO usertbl VALUES('JYP', '조용필', 1950, '경기');
INSERT INTO usertbl VALUES('SSK', '성시경', 1979, '서울');
-- 자동으로 클러스터 인덱스로 지정된 값으로 정렬
SELECT * FROM usertbl;

-- id에서 name으로 변경 시 클러스터 인덱스로 변경
ALTER TABLE usertbl DROP PRIMARY KEY;
ALTER TABLE usertbl
  ADD CONSTRAINT pk_name PRIMARY KEY(name);

SELECT * FROM usertbl;
-- 자동으로 클러스터 인덱스로 지정된 값으로 정렬함을 볼 수 있음
```

## 09-03 인덱스의 내부작동 1

### B-Tree(Balanced Tree, 균형 트리)

- B-Tree란 균형이 잡힌 트리, 인덱스를 표현할 때와 그 외에서도 많이 사용
- 노드(Node)란 트리 구조에서 데이터가 존재하는 공간
- 루트 노드(Root Node) -> 중간 수준 노드 -> 리프 노드(Leaf Node)
- MySQL에서는 Node를 페이지(Page)라고 부름
- **페이지**는 기본 16Kbyte의 크기, **최소한의 저장 단위**
- 페이지 크기를 4KB, 8KB, 16KB, 32KB, 64KB로 변경 가능
- 페이지 크기 검색: `SHOW VARIABLES LIKE 'innodb_page_size'`

### 페이지 분할

- 데이터 검색 시 B-Tree가 효율적
- 데이터 변경 작업 시 성능이 나빠짐, 이유는 **페이지 분할**이 일어나기 때문

## 09-04 인덱스의 내부작동 2

### 클러스터형 인덱스와 보조 인덱스의 구조

클러스터형 인덱스
- 테이블을 만들고 PRIMARY KEY를 지정하면 자동으로 정렬된 인덱스 페이지가 만들어짐
- 인덱스 페이지(루트 페이지(주솟값) -> 리프 페이지(데이터 페이지))
- 범위 검색에 빠름
- 수정에 있어서 페이지 분할이 비교적 많이 일어남

보조 인덱스
- 정렬이 안되어 있어 검색에 클러스터형 보단 조금 느림
- 인덱스 페이지(루트 페이지(주솟값) -> 리프 페이지(주솟값)) -> 데이터 페이지
- 범위 검색에 비교적 더 느림
- 수정에 있어서 페이지 분할이 비교적 적게 일어남

OLTP와 OLAP에 인덱스 생성
- OLTP(On-Line Transaction Processing): 변환용, 필요한 인덱스만 생성할 것
- OLAP(On-Line Analytical Processing): 분석용, 많이 생성해도 괜찮음

### 클러스터형 인덱스와 보조 인덱스가 혼합되어 있는 경우

```sql
-- db 생성
CREATE DATABASE IF NOT EXISTS testdb;
USE testdb;

-- mixedtbl 생성
DROP TABLE IF EXISTS mixedtbl;
CREATE TABLE mixedtbl
( userID  CHAR(8) NOT NULL,
  name    VARCHAR(10) NOT NULL,
  addr    char(2)
);

-- mixedtbl 데이터 삽입
INSERT INTO mixedtbl VALUES('LSG', '이승기', '서울');
INSERT INTO mixedtbl VALUES('KBS', '김범수', '경남');
INSERT INTO mixedtbl VALUES('KKH', '김경호', '전남');
INSERT INTO mixedtbl VALUES('JYP', '조용필', '경기');
INSERT INTO mixedtbl VALUES('SSK', '성시경', '서울');
INSERT INTO mixedtbl VALUES('LJB', '임재범', '서울');
INSERT INTO mixedtbl VALUES('YJS', '윤종신', '경남');
INSERT INTO mixedtbl VALUES('EJW', '은지원', '경북');
INSERT INTO mixedtbl VALUES('JKW', '조관우', '경기');
INSERT INTO mixedtbl VALUES('BBK', '바비킴', '서울');

-- PK 구성
ALTER TABLE mixedtbl
  ADD CONSTRAINT PK_mixedtbl_userID
    PRIMARY KEY (userID);

-- UNIQUE 구성
ALTER TABLE mixedtbl
  ADD CONSTRAINT UK_mixedtbl_name
    UNIQUE (name) ;

-- 클러스터형, 보조 인덱스 조회
SHOW INDEX FROM mixedtbl;

-- 검색 원리 보기
SELECT addr FROM mixedtbl WHERE name = '임재범';
-- 보조인덱스에서 접근 루트 페이지로 넘어가서 루트에서 데이터 페이지로 가서 주소를 찾음
```

- 인덱스 검색하기 위한 1차 조건은 WHERE절을 해당 인덱스에 사용
- WHERE절에 해당 인덱스를 생선한 열 이름이 나와도 인덱스를 사용하지 않는 경우도 많음

## 09-05 인덱스 실습과 결론

### 인덱스 생성/변경/삭제

제약 조건에서 자동으로 생성이 아닌 직접 인덱스를 생성하는 구문들

인덱스 생성

```sql
CREATE [UNIQUE | FULLTEXT | SPATIAL] INDEX index_name
  [index_type]
  ON tbl_name (key_part, ...)
  [index_option]
  [algorithm_option | lock_option] ...

-- key_part
{col_name [(length)] | (expr)} [ASC | DESC]

-- index_option
KEY_BLOCK_SIZE [=] value
| index_type
| WITH PARSER parser_name
| COMMENT 'string'
| {VISIBLE | INVISIBLE}

-- index_type
USING {BTREE | HASH}

-- algorithm_option
ALGORITHM [=] {DEFAULT | INPLACE | COPY}

-- lock_option
LOCK [=] {DEFAULT | NONE | SHARED | EXCLUSIVE}
```

인덱스 삭제

```sql
DROP INDEX 인덱스이름 ON 테이블이름;
```

- 클러스터형 인덱스 삭제 방법: 인덱스 이름 부분에 `PRIMARY`로 써주면 됨
- 인덱스를 모두 제거할 때는 보조 인덱스부터 삭제할 것
  - 클러스터형 부터 삭제하면 루트 페이지가 없어져 리프 페이지는 모두 루트 페이지를 지정, 재구성을 자동으로 함
  - 재구성 후 삭제하면 재구성 비용이 낭비되기 때문에
- 인덱스를 많이 생성해 놓은 테이블은 인덱스 용도를 잘 확인 후 활용도가 떨어지면 삭제할 것, 성능 저하 때문

인덱스 생성 및 사용 실습

```sql
-- sqldb의 usertbl 확인
USE sqldb;
SELECT * FROM usertbl;

-- 클러스터 인덱스 확인
SHOW INDEX FROM usertbl;

-- 인덱스 크기 보기
SHOW TABLE STATUS LIKE 'usertbl';
-- Data_length를 보면 16384(한 페이지)만 사용하는 것을 볼 수 있음

-- 중복이 허용되는 INDEX 생성
CREATE INDEX idx_usertbl_addr 
   ON usertbl (addr);

-- 보조 인덱스 생성 확인
SHOW INDEX FROM usertbl;
-- Non_unique가 1이면 중복 허용

-- 인덱스 적용 여부 보기
SHOW TABLE STATUS LIKE 'usertbl';
-- Index_length가 0임(페이지 생성 x)

-- 인덱스 적용하기
ANALYZE TABLE usertbl;
-- 인덱스 적용 여부 다시 보기
SHOW TABLE STATUS LIKE 'usertbl';
-- Index_length가 16384임(페이지 생성 o)

-- 중복 데이터 발생 때문에 INDEX 생성이 안됨
CREATE UNIQUE INDEX idx_usertbl_birtyYear
  ON usertbl (birthYear);

-- 중복 데이터 발생이 없기 때문에 만들어짐
CREATE UNIQUE INDEX idx_usertbl_name
  ON usertbl (name);

-- 고유 인덱스 생성 확인
SHOW INDEX FROM usertbl;

-- 오류 발생: 이름이 이미 있음
INSERT INTO usertbl VALUES('GPS', '김범수', 1983, '미국', NULL  , NULL  , 162, NULL);
-- UNIQUE 인덱스는 앞으로도 없는 값으로 넣어야 함

-- 2개의 열을 합쳐서 인덱스로 만듬
CREATE INDEX idx_usertbl_name_birthYear
  ON usertbl (name, birthYear);  -- 이 부분
DROP INDEX idx_usertbl_name ON usertbl;

-- Seq_in_index가 1, 2 순서로 되어있는 것을 볼 수 있음
SHOW INDEX FROM usertbl;

-- 인덱스 검색을 잘 했는지 확인
SELECT * FROM usertbl WHERE name = '윤종신' and birthYear = '1969';
-- Execution Plan을 보면 Non-Unique Key Lookup을 사용함

-- 인덱스 생성
CREATE INDEX idx_usertbl_mobile1
  ON usertbl (mobile1);

-- 인덱스 검색을 잘 했는지 확인
SELECT * FROM usertbl WHERE mobile1 = '011';
-- Execution Plan을 보면 Non-Unique Key Lookup을 사용함

-- 인덱스 목록 보기
SHOW INDEX FROM usertbl;

-- 보조 인덱스부터 삭제하길 바람
DROP INDEX idx_usertbl_addr ON usertbl;
DROP INDEX idx_usertbl_name_birthYear ON usertbl;
DROP INDEX idx_usertbl_mobile1 ON usertbl;

ALTER TABLE usertbl DROP INDEX idx_usertbl_addr;
ALTER TABLE usertbl DROP INDEX idx_usertbl_name_birthYear;
ALTER TABLE usertbl DROP INDEX idx_usertbl_mobile1;

-- 오류 발생: 회원tbl과 구매tbl이 pk, fk로 연결되어 있어서
ALTER TABLE usertbl DROP PRIMARY KEY;

-- fk가 연결되어 있는 table 확인하기
SELECT table_name, constraint_name
    FROM information_schema.referential_constraints
    WHERE constraint_schema = 'sqldb';

-- fk 삭제
ALTER TABLE buyTbl DROP FOREIGN KEY buytbl_ibfk_1;
-- pk 삭제
ALTER TABLE usertbl DROP PRIMARY KEY;
```

### 결론: 인덱스를 생성해야 하는 경우와 그렇지 않은 경우

1. 인덱스는 열 단위에 생성한다.
2. WHERE절에서 사용되는 열에 인덱스를 만들어야 한다.
3. WHERE절에 사용되더라도 자주 사용해야 가치가 있다.
4. 데이터의 중복도가 높은 열은 인덱스를 만들어도 별 효과가 없다.
5. 외래 키를 지정한 열에는 자동으로 외래 키 인덱스가 생성된다.
6. JOIN에 자주 사용되는 열에는 인덱스를 생성해 주는 것이 좋다.
7. INSERT/UPDATE/DELETE가 자주 일어나면 바람직하지 않다.
8. 클러스터형 인덱스는 테이블당 하나만 생성할 수 있다.
9. 클러스터형 인덱스가 테이블에 아에 없는 것이 좋은 경우도 있다.
10. 사용하지 않는 인덱스는 제거하는게 바람직하다.
