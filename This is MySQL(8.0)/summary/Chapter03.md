# Chapter 03 MySQL 설치

## 03-01 데이터베이스 필수 용어

거의 다 아는 내용 PASS

### 요구사항 분석, 시스템 설계, 모델링

DBMS(MySQL) -> Table -> Data

## 03-02 데이터베이스 구축 절차 실습

DBMS 설치 -> DB 구축(DB 생성, 테이블 생성, 데이터 입력, 데이터 조회/활용)

테이블 외의 DB 개체 활용 -> 데이터 백업 및 관리

응용 프로그램에서 구축된 데이터 활용(웹 서비스/애플리케이션)

```SQL
CREATE TABLE `shopdb`.`membertbl` (
  `memberID` CHAR(8) NOT NULL,
  `memberName` CHAR(5) NOT NULL,
  `memberAddress` CHAR(20) NULL,
  PRIMARY KEY (`memberID`));
```

```SQL
CREATE TABLE `shopdb`.`producttbl` (
    `productName` CHAR(4) NOT NULL,
    `cost` INT NOT NULL,
    `makeDate` DATE NULL,
    `company` CHAR(5) NULL,
    `amount` INT NOT NULL,
PRIMARY KEY (`productName`));
```

## 03-03 데이터베이스 개체 - 인덱스

### 인덱스란

책 뒤에 찾아보기같은 개념, 빠르게 찾을 수 있음

index를 만들면 굉장히 빠르게 Data를 불러옴

```sql
-- 테이블 생성
CREATE TABLE shopdb.indexTBL (
    first_name VARCHAR(14),
    last_name VARCHAR(16),
    hire_date DATE
);

-- 테이블에 다른 D(employees)B의 다른 테이블(employees)의 성, 이름, 고용일 500개만 추가
INSERT INTO shopdb.indexTBL
	SELECT first_name, last_name, hire_date
	FROM employees.employees
	LIMIT 500;

SELECT * FROM indexTBL;
SELECT * FROM shopdb.indexTBL WHERE first_name = 'Mary';

-- INDEX 를 생성하면 빠르게 불러올 수 있다!
CREATE INDEX idx_indexTBL_firstname ON shopdb.indexTBL(first_name);
SELECT * FROM shopdb.indexTBL WHERE first_name = 'Mary';
```

## 03-04 데이터베이스 개체 - 뷰, 저장프로시저, 트리거

### 뷰(View)

뷰를 만들면 읽기 전용 느낌이여서 데이터 보안과 안정성에 좋다.

### 저장프로시저(Stored Procedure)

### 트리거(Trigger)

트리거를 만들면

## 03-05 백업과 복원

DB관리자가 해야할 일!

## 03-06 MySQL과 응용프로그램 연동

