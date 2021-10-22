# Chapter 07 SQL고급

## 07-01 데이터 형식, 변수

### 데이터 형식

#### 숫자

|  데이터 형식   | 바이트 수 | 타입  |              설명              |
| :------------: | :-------: | :---: | :----------------------------: |
|    TINYINT     |     1     | 정수  |           -128 ~ 127           |
|    SMALLINT    |     2     | 정수  |        -32,768 ~ 32,767        |
|  INT(INTEGER)  |     4     | 정수  |        약 -21억 ~ 21억         |
|     BIGINT     |     8     | 정수  |       약 -900경 ~ 900경        |
|     FLOAT      |     4     | 실수  |      -3.40E+38 ~ 1.17E-38      |
| DECIMAL(m.[d]) |   5~17    | 실수  |       -10E38+1 ~ 10E38-1       |
| NUMERIC(m.[d]) |     -     |   -   | 전체자릿수(m), 소숫점자릿수(d) |

#### 문자

| 데이터 형식 |  바이트 수   |                    설명                     |
| :---------: | :----------: | :-----------------------------------------: |
|   CHAR(n)   |    1~255     | 고정길이 문자형, CHAR만 쓰면 CHAR(1)과 동일 |
| VARCHAR(n)  |   1~65535    |     가변길이 문자형, 메모리 절약에 좋음     |
|  LONGTEXT   | 1~4294967295 |       최대 4GB 크기의 TEXT 데이터 값        |
|  LONGBLOB   | 1~4294967295 |       최대 4GB 크기의 BLOB 데이터 값        |

#### 날짜

| 데이터 형식 | 바이트 수 |         형식          |                   범위                    |
| :---------: | :-------: | :-------------------: | :---------------------------------------: |
|    DATE     |     3     |     'YYYY-MM-DD'      |          1001-01-01 ~ 9999-12-31          |
|    TIME     |     3     |      'HH:MM:SS'       |   -838:59:59.000000 ~ 838:59:59.000000    |
|  DATETIME   |     8     | 'YYYY-MM-DD HH:MM:SS' | 1001-01-01 00:00:00 ~ 9999-12-31 23:59:59 |

#### 기타

| 데이터 형식 | 바이트 수 |                                    설명                                     |
| :---------: | :-------: | :-------------------------------------------------------------------------: |
|  GEOMETRY   |    N/A    | 공간 데이터 형식으로 선, 점 및 다각형 같은 공간 데이터 개체를 저장하고 조작 |
|    JSON     |     8     |                JSON(JavaScript Object Notation) 문서를 저장                 |

MySQL에서는 문자는 utf-8로 기본적으로 설정해 있음

만약 아니라면 `my.ini` 나 `my.cnf` 파일에 utf-8로 기본적 설정으로 추천

```bash
# 클라이언트 문자 세트
[mysql]
default-character-set=utf-8
# 서버 문자 세트
[mysqld]
character-set-server=utf-8
```

### 변수의 사용

변수는 메모리에 저장되기 때문에 Workbanch를 닫으면 다 날라감

기본 방법

```sql
SET @변수이름 = 변수의 값;
SELECT @변수이름;
```

```sql
SET @myVar1 = 3;
PREPARE myQuery
    FROM 'SELECT name, height FROM usertbl ORDER BY height LIMIT ?';
EXECUTE myQuery USING @myVar1;
```

## 07-02 데이터형 변환, 내장 함수

### 데이터 형식 변환 함수

기본 형식

```sql
CAST(expression AS 데이터형식 [(길이)])
CONVERT(expression, 데이터형식 [(길이)])
```

형 변환하기

```sql
USE sqldb;
SELECT AVG(amount) AS '평균 구매 갯수' FROM buytbl;

-- 정수로 바꾸기
-- CAST(변환할 값 AS SIGNED INTEGER);
SELECT CAST(AVG(amount) AS SIGNED INTEGER) AS '평균 구매 갯수' FROM buytbl;
SELECT CONVERT(AVG(amount), SIGNED INTEGER) AS '평균 구매 갯수' FROM buytbl;

-- 날짜 바꾸기
-- CAST(날짜 문자 AS DATE);
SELECT CAST('2021/10/01' AS DATE);
SELECT CONVERT('2021/10/01', DATE);

-- 문자열로 이어주기
-- CONCAT(문자1, 문자2, ...)
SELECT num,
    CONCAT(CAST(price AS CHAR(10)), 'X', CAST(amount AS CHAR(4)), '=') AS '단가X수량',
    price*amount AS '구매액'
FROM buytbl;
```

### 암시적 형변환

```sql
SELECT '100' + '200' ; -- 문자와 문자를 더함 (정수로 변환되서 연산됨)
SELECT CONCAT('100', '200'); -- 문자와 문자를 연결 (문자로 처리)
SELECT CONCAT(100, '200'); -- 정수와 문자를 연결 (정수가 문자로 변환되서 처리)
SELECT 1 > '2mega'; -- 정수인 2로 변환되어서 비교
SELECT 3 > '2MEGA'; -- 정수인 2로 변환되어서 비교
SELECT 0 = 'mega2'; -- 문자는 0으로 변환됨
```

### MySQL 내장 함수

```sql
-- 참 거짓에 따른 문자열 반환
-- IF(수식, 참, 거짓)
SELECT IF(100>200, '참이다', '거짓이다');

-- NULL이면 수식2 반환, NULL이 아니면 수식1 반환
-- IFNULL(수식1, 수식2)
SELECT IFNULL(NULL, '널이군요'), IFNULL(100, '널이군요');

-- IFNULL(수식1, 수식2)
SELECT NULLIF(100,100), IFNULL(200,100);

-- CASE의 값에 따라 값 반환
-- CASE 경우
--     WHEN 보기1 THEN 반환1
--     WHEN 보기2 THEN 반환2
--     ELSE 그밖의 경우
-- END
SELECT CASE 10
    WHEN 1 THEN '일'
    WHEN 5 THEN '오'
    WHEN 10 THEN '십'  -- 여기 출력
    ELSE '모름'
END AS 'CASE연습';  -- 필드 이름

-- ASCII 번호로 반환, 문자로 반환
-- ASCII(문자), CHAR(숫자);
SELECT ASCII('A'), CHAR(65);

-- 문자열의 길이를 반환, 내부적 bit, 글자의 갯수, 내부적 byte
-- 내부 크기보다 글자의 길이만 보는 경우가 일반적 -> CHAR_LENGTH
-- BIT_LENGTH(문자열), CHAR_LENGTH(문자열), LENGTH(문자열)
SELECT BIT_LENGTH('abc'), CHAR_LENGTH('abc'), LENGTH('abc');  -- 24, 3, 3
SELECT BIT_LENGTH('가나다'), CHAR_LENGTH('가나다'), LENGTH('가나다');  -- 72, 3, 9

-- 구분자와 함께 이어줌
-- CONCAT_WS(구분자, 문자1, 문자2, ...)
SELECT CONCAT_WS('/', '2025', '01', '01');

-- ELT(인덱스, 문자1, 문자2, ...): 인덱스에 해당하는 문자를 출력, 없으면 NULL
-- FIELD(검색어, 문자1, 문자2, ...): 검색어의 인덱스를 숫자로 출력, 없으면 0 출력
-- FIND_IN_SET(검색어, '문자1,문자2,...'): ','기준으로 인덱스를 숫자로 출력, 없으면 0 출력
-- INSTR('문자열', 부분 문자열): 부분문자열의 시작 인덱스를 숫자로 출력, 없으면 0 출력
-- LOCATE(부분 문자열, '문자열'): 부분문자열의 시작 인덱스를 숫자로 출력, 없으면 0 출력
SELECT ELT(2, '하나', '둘', '셋'), FIELD('둘', '하나', '둘', '셋'), FIND_IN_SET('둘', '하나, 둘, 셋'), INSTR('하나둘셋', '둘'), LOCATE('둘', '하나둘셋');

-- 숫자를 자릿수 만큼 표현하고 싶을 때
-- FORMAT(숫자, 자릿수)
SELECT FORMAT(123456.123456, 4);

-- 진법 변환: BIN(숫자), HEX(숫자), OCT(숫자);
SELECT BIN(31), HEX(31), OCT(31);
-- 11111, 1F, 37

-- INSERT(문자열, 시작인덱스, 갯수, 넣을 문자열)
SELECT INSERT('abcdefghi', 3, 4, '@@@@'), INSERT('abcdefghi', 3, 2, '@@@@');
-- ab@@@@ghi, abc@@@@fghi

-- 왼쪽에서 3개 출력: LEFT(문자열, 3)
-- 오른쪽에서 2개 출력: RIGHT(문자열, 2)
SELECT LEFT('abcdefghi', 3), RIGHT('abcdefghi', 2);

-- 대문자로: UPPER(문자열)
-- 소문자로: LOWER(문자열)
SELECT LOWER('abcdEFGH'), UPPER('abcdEFGH');

-- 왼쪽 패딩 총 길이 5: LPAD(문자열, 5, 패딩할 문자열)
-- 오른쪽 패딩 총 길이 8: LPAD(문자열, 5, 패딩할 문자열)
SELECT LPAD('이것이', 5, '#'), RPAD('이것이', 8, '!#');
-- ##이것이, 이것이!#!#!

-- 오른쪽 공백 삭제: RTRIM(문자열)
-- 왼쪽 공백 삭제: LTRIM(문자열)
-- 양쪽 공백 삭제: TRIM(문자열)
-- 양쪽 특정 문자열 삭제: TRIM(BOTH 삭제할 문자 FROM 문자열)
SELECT LTRIM('   이것이'), RTRIM('이것이   ');
SELECT TRIM('   이것이   ')
SELECT TRIM(BOTH 'ㅋ' FROM 'ㅋㅋㅋ재밌어요.ㅋㅋㅋ');
SELECT TRIM('ㅋ' FROM 'ㅋㅋㅋ재밌어요.ㅋㄴㅋㅋ');
-- '재밌어요.ㅋㄴ'

-- 문자열 횟수 반복: REPEAT(문자열, 횟수)
SELECT REPEAT('이것이', 3);

-- 문자열 치환: REPLACE(문자열, 바꿀 문자열, 바뀐 문자열)
SELECT REPLACE('이것이 MySQL이다', '이것이', 'This is');
SELECT REPLACE('삭제해줘', '삭제', '수정');

-- 거꾸로
SELECT REVERSE('MySQL');

-- 띄어쓰기 넣기
SELECT CONCAT('이것이', SPACE(10), 'MySQL이다');

-- 부분 문자열 3번째~2개까지(민국)
SELECT SUBSTRING('대한민국만세', 3, 2);

-- 부분문자열 인덱싱
-- SUBSTRING_INDEX(문자열, 구분자, 순번(시작부터))
SELECT SUBSTRING_INDEX('cafe.naver.com', '.', 2), SUBSTRING_INDEX('cafe.naver.com', '.', -2);

-- 절댓값
SELECT ABS(-100);

-- 올림, 내림, 반올림: CEILING, FLOOR, ROUND
SELECT CEILING(4.7), FLOOR(4.7), ROUND(4.7);
-- 5, 4, 5

-- CONV(문자 또는 숫자, 원래 진법, 바꿀 진법)
SELECT CONV('AA', 16, 2), CONV(100, 10, 8);
-- 10101010, 144

-- 각도 변환
SELECT DEGREES(PI()), RADIANS(180);
-- 180, 3.141592...

-- 나머지
SELECT MOD(157, 10), 157 % 10, 157 MOD 10;
-- 7, 7, 7

-- 제곱과 제곱근
SELECT POW(2,3), SQRT(9);
-- 8, 3

-- 랜덤(0~1), 1~6까지 랜덤
SELECT RAND(), FLOOR(1 + (RAND() * (6-1)) );

-- 삼각함수
SELECT SIGN(100), SIGN(0), SIGN(-100.123);
-- 1, 0, -1

-- 자릿수 소숫점 2번째까지, 둘쨋자리까지 버림
SELECT TRUNCATE(12345.12345, 2), TRUNCATE(12345.12355, -2);
-- 12345.12, 12300

-- 날짜 더하기 빼기
SELECT ADDDATE('2025-01-01', INTERVAL 31 DAY), ADDDATE('2025-01-01', INTERVAL 1 MONTH);
SELECT SUBDATE('2025-01-01', INTERVAL 31 DAY), SUBDATE('2025-01-01', INTERVAL 1 MONTH);
-- 2025-02-01, 2025-02-01
-- 2024-12-01, 2024-12-01

-- 시간 더하기 빼기
SELECT ADDTIME('2025-01-01 23:59:59', '1:1:1'), ADDTIME('15:00:00', '2:10:10');
SELECT SUBTIME('2025-01-01 23:59:59', '1:1:1'), SUBTIME('15:00:00', '2:10:10');

-- 현재 날짜, 시간 보기
SELECT YEAR(CURDATE()), MONTH(CURDATE()), DAYOFMONTH(CURDATE());
SELECT HOUR(CURTIME()), MINUTE(CURRENT_TIME()), SECOND(CURRENT_TIME()), MICROSECOND(CURRENT_TIME());

-- 현재 날짜, 시간 보기
SELECT DATE(NOW()), TIME(NOW());
SELECT DATE(CURTIME()), TIME(CURTIME());

-- 날짜, 시간 차이 구하기
SELECT DATEDIFF('2025-01-01', NOW()), TIMEDIFF('23:23:59', '12:11:10');

-- 요일(일월화수목금토, 1234567), 월을 글자로 출력, 1년 중 몇번째 날짜?
-- DAYOFWEEK(날짜), MONTHNAME(날짜), DAYOFYEAR(날짜)
SELECT DAYOFWEEK(CURDATE()), MONTHNAME(CURDATE()), DAYOFYEAR(CURDATE());
-- 4, October 279

-- 해당 달의 마지막 날짜 구하기
SELECT LAST_DAY('2024-02-01');
-- 2025-02-29

-- 날짜 만들기
-- MAKEDATE(년도, 일수)
SELECT MAKEDATE(2021, 279);
-- 2021-10-06

-- 시간 만들기
-- MAKETIME(시, 분, 초)
SELECT MAKETIME(12, 11, 10);
-- 12:11:10

-- 기간 더하기, 기간 빼기
-- PERIOD_ADD(yymm 또는 yyyymm 형식, 달수), PERIOD_DIFF(yymm 또는 yyyymm 형식, yymm 또는 yyyymm 형식)
SELECT PERIOD_ADD(202501, 11), PERIOD_DIFF(202501, 202312);
-- 202512, 13

-- 분기 계산(3분기)
SELECT QUARTER('2025-07-07');

--초로 바꾸기(43870초)
SELECT TIME_TO_SEC('12:11:10');

-- 현재 유저, 사용중인 DB 보기
SELECT CURRENT_USER(), DATABASE();

USE sqldb;
SELECT * FROM usertbl;
-- 조회된 행갯수 보기
SELECT FOUND_ROWS();

USE sqldb;
UPDATE buytbl SET price=price*2;
-- 변화된 행 갯수 보기
SELECT ROW_COUNT();

-- 타이머 작동(쿼리문)
SELECT SLEEP(5);
SELECT '5초후에 이게 보여요';
```

## 07-03 영화사이트(대용량 데이터) 구축 실습

```sql
-- 1. DB 생성
CREATE DATABASE DB_NAME;

-- 1-1. DB 확인하기
SHOW DATABASES;

-- 1-2. DB 사용
USE DB_NAME;

-- 1-3. 실수로 DB 잘못 생성시 삭제
DROP DATABASE DB_NAME;

-- 2-1. TABLE 생성
CREATE TABLE movietbl 
  (movie_id        INT,
   movie_title     VARCHAR(30),
   movie_director  VARCHAR(20),
   movie_star      VARCHAR(20),
   movie_script    LONGTEXT,
   movie_film      LONGBLOB
) DEFAULT CHARSET=utf8mb4;

-- 2-2. TABLE 보기
SHOW TABLES;
DESC TABLE_NAME;

-- 3. 값 넣기
INSERT INTO movietbl VALUES ( 1, '쉰들러 리스트', '스필버그', '리암 니슨',  
LOAD_FILE('C:/SQL/Movies/Schindler.txt'), LOAD_FILE('C:/SQL/Movies/Schindler.mp4') );

-- 3-1. 값 확인하기
SELECT * FROM movietbl;

-- 값이 안들어 가는 이유: 용량 크기 설정, 보안 설정이 필요!
```

중요! **환경 설정하기**

cmd 창에서

```powershell
# 1. 설정파일 들어가기
cd %PROGRAMDATA%
# 2. MySQL Server 8.0 폴더 들어가기
cd "MySQL\MySQL Server 8.0"
# 3. 메모장으로 my.ini 열기
NOTEPAD my.ini
```

```text
# 3-1 변경: max_allowed_packet=1024M
# 3-2 추가: secure-file-priv="C:\SQL\Movies"
# 수정 후 저장이 안된다면 권한을 열어줘야 한다.
```

```powershell
# 4. 재시작
NET STOP MySQL
NET START MySQL
# "시스템 오류 5이(가) 생겼습니다." powershell 관리자 권한으로 실행하여 해결!
```

MySQL WorkBench에 들어가서

```sql
-- 4-1. 변경된 데이터 보기
SHOW variables LIKE 'max_allowed_packet';
SHOW variables LIKE 'secure_file_priv';

USE moviedb;
-- 4-2. 기존 행 모두 제거
TRUNCATE movietbl;

INSERT INTO movietbl VALUES ( 1, '쉰들러 리스트', '스필버그', '리암 니슨',  
LOAD_FILE('C:/SQL/Movies/Schindler.txt'), LOAD_FILE('C:/SQL/Movies/Schindler.mp4') );
INSERT INTO movietbl VALUES ( 2, '쇼생크 탈출', '프랭크 다라본트', '팀 로빈스',  
LOAD_FILE('C:/SQL/Movies/Shawshank.txt'), LOAD_FILE('C:/SQL/Movies/Shawshank.mp4') );    
INSERT INTO movietbl VALUES ( 3, '라스트 모히칸', '마이클 만', '다니엘 데이 루이스',
LOAD_FILE('C:/SQL/Movies/Mohican.txt'), LOAD_FILE('C:/SQL/Movies/Mohican.mp4') );

SELECT * FROM movietbl;

-- 5. 다운로드
-- TEXT 다운로드
SELECT movie_script FROM movietbl WHERE movie_id=1 
INTO OUTFILE 'C:/SQL/Movies/Schindler_out.txt'  
LINES TERMINATED BY '\\n';

-- 영화 다운로드
SELECT movie_film FROM movietbl  WHERE movie_id=3 
INTO DUMPFILE 'C:/SQL/Movies/Mohican_out.mp4';
```

## 07-04 피벗, JSON

### 피벗

피벗(Pivot)테이블이란 많은 양의 데이터에서 필요한 자료만을 뽑아 새롭게 표를 작성해 주는 기능

```sql
USE sqldb;
CREATE TABLE pivotTest(uName CHAR(3), season CHAR(2), amount INT);

INSERT  INTO  pivotTest VALUES
('김범수', '겨울',  10), ('윤종신', '여름',  15), ('김범수', '가을',  25), ('김범수', '봄',     3),
('김범수', '봄',    37), ('윤종신', '겨울',  40), ('김범수', '여름',  14), ('김범수', '겨울',  22),
('윤종신', '여름',  64);
SELECT * FROM pivotTest;

SELECT uName, 
  SUM(IF(season='봄', amount, 0)) AS '봄', 
  SUM(IF(season='여름', amount, 0)) AS '여름',
  SUM(IF(season='가을', amount, 0)) AS '가을',
  SUM(IF(season='겨울', amount, 0)) AS '겨울',
  SUM(amount) AS '합계' FROM pivotTest GROUP BY uName;
```

### JSON

JSON을 MySQL에서 조작해보자!

```sql
USE sqldb;
SELECT JSON_OBJECT('name', name, 'height', height) AS 'JSON 값'
FROM usertbl 
WHERE height >= 180;

SET @json='{ "usertbl" :
   [
      {"name":"임재범","height":182},
      {"name":"이승기","height":182},
      {"name":"성시경","height":186}
   ]
}';
-- json이 유효한지 1/0 리턴
SELECT JSON_VALID(@json) AS JSON_VALID;

-- JSON_SEARCH(데이터, one/all, 찾을것)
-- 데이터 내에서 찾을꺼 첫번째 인덱스 반환(0부터 시작)
SELECT JSON_SEARCH(@json, 'one', '성시경') AS JSON_SEARCH;

-- 인덱스 기반으로 검색
SELECT JSON_EXTRACT(@json, '$.usertbl[2].name') AS JSON_EXTRACT;

-- 입력해라(Create)
SELECT JSON_INSERT(@json, '$.usertbl[0].mDate', '2009-09-09') AS JSON_INSERT;

-- 변경해라(Update)
SELECT JSON_REPLACE(@json, '$.usertbl[0].name', '홍길동') AS JSON_REPLACE;

-- 삭제해라(Delete)
SELECT JSON_REMOVE(@json, '$.usertbl[0]') AS JSON_REMOVE;
```

## 07-05 조인 개념과 Inner Join

### 조인(Join)

조인이란 두 개 이상의 테이블을 서로 묶어서 하나의 결과 집합으로 만들어 냄

### Inner Join

기본 형식

```sql
SELECT <열 목록>
FROM <첫 번째 테이블>
  INNER JOIN <두 번째 테이블>
    ON <조인될 조건>
[WHERE 검색조건]
```

아이디가 JYP인 회원의 구매내역을 보고싶어요

```sql
SELECT *
FROM buytbl
  INNER JOIN usertbl
    ON buytbl.userID = usertbl.userID
WHERE buytbl.userID = 'JYP';
```

INNER JOIN 시에 겹치는 필드명이 있으면 해당 `테이블.겹치는 필드명`으로 작성해야 한다.

```sql
-- DISTINCT(중복제거)
SELECT DISTINCT U.serID, U.name, U.addr
FROM usertbl U
  INNER JOIN buytbl B
    ON U.userID = B.userID
ORDER BY U.userID;
```

## 07-06 Outer, Cross, Self Join 및 Union 등

### Outer Join

### Cross Join

### Self Join

### Union

## 07-07 SQL 프로그래밍
