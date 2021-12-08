# Chapter 10 스토어드 프로그램

## 10-01 스토어드 프로시저

### 스토어드 프로시저(Stored Procedure)

- MySQL에서 제공되는 프로그래밍 기능
- 쿼리문의 집합

형식

```sql
USE sqlDB;
DROP PROCEDURE IF EXISTS userProc;

-- 프로시저 정의
DELIMITER $$
CREATE PROCEDURE userProc()
BEGIN
    SELECT * FROM userTBL; -- 스토어드 프로시저 내용
END $$
DELIMITER ;

-- 프로시저 사용
CALL userProc();
```

스토어드 프로시저 내용 실습

```sql
USE sqlDB;

-- userProc1 삭제
DROP PROCEDURE IF EXISTS userProc1;

-- userProc1 정의 및 생성
DELIMITER $$
CREATE PROCEDURE userProc1(IN userName VARCHAR(10))
BEGIN
  SELECT * FROM userTBL WHERE name = userName; 
END $$
DELIMITER ;

-- userProc1 사용
CALL userProc1('조관우');

-- userProc2 삭제
DROP PROCEDURE IF EXISTS userProc2;

-- userProc2 정의 및 생성
DELIMITER $$
CREATE PROCEDURE userProc2(
    IN userBirth INT, 
    IN userHeight INT
)
BEGIN
  SELECT * FROM userTBL 
    WHERE birthYear > userBirth AND height > userHeight;
END $$
DELIMITER ;

-- userProc2 사용
CALL userProc2(1970, 178);

-- userProc3 삭제
DROP PROCEDURE IF EXISTS userProc3;

-- userProc3 정의 및 생성
DELIMITER $$
CREATE PROCEDURE userProc3(
    IN txtValue CHAR(10),
    OUT outValue INT
)
BEGIN
  INSERT INTO testTBL VALUES(NULL,txtValue);
  SELECT MAX(id) INTO outValue FROM testTBL;
END $$
DELIMITER ;
-- 테이블이 없어도 정의가 가능

-- 테이블 생성
CREATE TABLE IF NOT EXISTS testTBL(
    id INT AUTO_INCREMENT PRIMARY KEY,
    txt CHAR(10)
);

-- userProc3 사용
-- out Parameter는 변수 값으로 기입
CALL userProc3 ('테스트값', @myValue);
SELECT CONCAT('현재 입력된 ID 값 ==>', @myValue);

-- ifelse 프로시저
DROP PROCEDURE IF EXISTS ifelseProc;
DELIMITER $$
CREATE PROCEDURE ifelseProc(
    IN userName VARCHAR(10)
)
BEGIN
    DECLARE bYear INT; -- 변수 선언
    SELECT birthYear into bYear FROM userTBL
        WHERE name = userName;
    IF (bYear >= 1980) THEN
            SELECT '아직 젊군요..';
    ELSE
            SELECT '나이가 지긋하네요..';
    END IF;
END $$
DELIMITER ;

CALL ifelseProc ('조용필');

-- case 프로시저
DROP PROCEDURE IF EXISTS caseProc;
DELIMITER $$
CREATE PROCEDURE caseProc(
    IN userName VARCHAR(10)
)
BEGIN
    DECLARE bYear INT; 
    DECLARE tti  CHAR(3);-- 띠
    SELECT birthYear INTO bYear FROM userTBL
        WHERE name = userName;
    CASE 
        WHEN ( bYear%12 = 0) THEN    SET tti = '원숭이';
        WHEN ( bYear%12 = 1) THEN    SET tti = '닭';
        WHEN ( bYear%12 = 2) THEN    SET tti = '개';
        WHEN ( bYear%12 = 3) THEN    SET tti = '돼지';
        WHEN ( bYear%12 = 4) THEN    SET tti = '쥐';
        WHEN ( bYear%12 = 5) THEN    SET tti = '소';
        WHEN ( bYear%12 = 6) THEN    SET tti = '호랑이';
        WHEN ( bYear%12 = 7) THEN    SET tti = '토끼';
        WHEN ( bYear%12 = 8) THEN    SET tti = '용';
        WHEN ( bYear%12 = 9) THEN    SET tti = '뱀';
        WHEN ( bYear%12 = 10) THEN    SET tti = '말';
        ELSE SET tti = '양';
    END CASE;
    SELECT CONCAT(userName, '의 띠 ==>', tti);
END $$
DELIMITER ;

CALL caseProc ('김범수');

-- 구구단 테이블 생성
DROP TABLE IF EXISTS guguTBL;
CREATE TABLE guguTBL (txt VARCHAR(100)); -- 구구단 저장용 테이블

-- 반복문 프로시저
DROP PROCEDURE IF EXISTS whileProc;
DELIMITER $$
CREATE PROCEDURE whileProc()
BEGIN
    DECLARE str VARCHAR(100); -- 각 단을 문자열로 저장
    DECLARE i INT; -- 구구단 앞자리
    DECLARE k INT; -- 구구단 뒷자리
    SET i = 2; -- 2단부터 계산
    
    WHILE (i < 10) DO  -- 바깥 반복문. 2단~9단까지.
        SET str = ''; -- 각 단의 결과를 저장할 문자열 초기화
        SET k = 1; -- 구구단 뒷자리는 항상 1부터 9까지.
        WHILE (k < 10) DO
            SET str = CONCAT(str, '  ', i, 'x', k, '=', i*k); -- 문자열 만들기
            SET k = k + 1; -- 뒷자리 증가
        END WHILE;
        SET i = i + 1; -- 앞자리 증가
        INSERT INTO guguTBL VALUES(str); -- 각 단의 결과를 테이블에 입력.
    END WHILE;
END $$
DELIMITER ;

CALL whileProc();
SELECT * FROM guguTBL;

-- 오류처리 프로시저
DROP PROCEDURE IF EXISTS errorProc;
DELIMITER $$
CREATE PROCEDURE errorProc()
BEGIN
    DECLARE i INT; -- 1씩 증가하는 값
    DECLARE hap INT; -- 합계 (정수형). 오버플로 발생시킬 예정.
    DECLARE saveHap INT; -- 합계 (정수형). 오버플로 직전의 값을 저장.

    DECLARE EXIT HANDLER FOR 1264 -- INT형 오버플로가 발생하면 이 부분 수행
    BEGIN
        SELECT CONCAT('INT 오버플로 직전의 합계 --> ', saveHap);
        SELECT CONCAT('1+2+3+4+...+', i, '=오버플로');
    END;

    SET i = 1; -- 1부터 증가
    SET hap = 0; -- 합계를 누적

    WHILE (TRUE) DO  -- 무한 루프.
        SET saveHap = hap; -- 오버플로 직전의 합계를 저장
        SET hap = hap + i;  -- 오버플로가 나면 11, 12행을 수행함
        SET i = i + 1; 
    END WHILE;
END $$
DELIMITER ;

-- 오류처리 프로시저 실행
CALL errorProc();

-- sqldb의 프로시저 목록 조회
SELECT routine_name, routine_definition FROM INFORMATION_SCHEMA.ROUTINES
    WHERE routine_schema = 'sqldb' AND routine_type = 'PROCEDURE';

-- sqldb의 프로시저 파라미터 조회
SELECT param_list, body FROM MYSQL.PROC 
    WHERE db='sqldb' AND type='PROCEDURE' AND name='userProc3';

-- 프로시저 상태 보기
SHOW CREATE PROCEDURE sqldb.userProc3;
-- 마우스 오른쪽 Open Value in Viewer를 누르면 sql을 볼 수 있음

-- 시도1: 테이블 이름을 넘겨 받아서 테이블을 조회할 때(실패함)
DROP PROCEDURE IF EXISTS nameProc;
DELIMITER $$
CREATE PROCEDURE nameProc(
    IN tblName VARCHAR(20)
)
BEGIN
 SELECT * FROM tblName;
END $$
DELIMITER ;

-- 테이블 이름 조회
CALL nameProc ('userTBL');
-- 에러이유: 테이블은 파라미터로 불가능

-- 시도2: 테이블 이름을 넘겨 받아서 테이블을 조회할 때(성공함)
DROP PROCEDURE IF EXISTS nameProc;
DELIMITER $$
CREATE PROCEDURE nameProc(
    IN tblName VARCHAR(20)
)
BEGIN
  SET @sqlQuery = CONCAT('SELECT * FROM ', tblName); -- FROM 뒤 공백 1개
  PREPARE myQuery FROM @sqlQuery;  -- 쿼리 준비하기
  EXECUTE myQuery;  -- 쿼리 실행하기
  DEALLOCATE PREPARE myQuery;  -- 쿼리 해제하기
END $$
DELIMITER ;

-- 테이블 호줄하기
CALL nameProc ('userTBL');
```

### 스토어드 프로시저를 많이 만들면 장점

1. MySQL 성능을 향상
   - 긴 쿼리문을 서버로 보내면 모든 텍스트 전송, but 매개 변수 등 글자만 보낼 수 있음
2. 유지관리가 가능, 모듈식 프로그래밍이 가능
   - 클라이언트에서 SQL문 작성하지 않고 이름만 호출해 유지가 편함
   - 모듈화가 되어 쉽게 관리 가능
3. 보안을 강화 가능
   - 사용자 별로 테이블에 접근 권한을 주지 않고 프로시저에만 접근 권한을 줘 보안 강화
   - 예) userTbl에 접근을 주지 않고 스토어드 프로시저에만 접근 권한을 줘서 쉽게 보안 관리

## 10-02 스토어드 함수, 커서

### 스토어드 함수(사용자 정의 함수)

스토어드 함수의 특징

- 스토어드 함수의 파라미터는 모두 입력 파라미터로 사용
- 반환할 값의 데이터 형식 지정하고 본문에서 RETURN으로 값 하나를 반환
- `SELECT` 문장 안에서 호출
- 함수 안에서 집합 결과를 반환하는 SELECT를 사용할 수 없음
- 스토어드 함수는 어떤 계산을 통해서 하나의 값을 반환하는데 사용

스토어드 프로시저의 특징

- 스토어드 프로시저는 IN, OUT을 사용
- 스토어드 프로시저는 반환구문이 없음, OUT을 통해 값 여러개 반환 가능
- `Call`로 호출
- SELECT문을 사용할 수 있음

형식

```sql
-- 생성
DELIMITER $$
CREATE FUNCTION 스토어드 함수이름( 파라미터 )
  RETURN 반환형식
BEGIN
  프로그래밍 코딩
  RETURN 반환값;
END $$
DELIMITER ;

-- 실행
SELECT 스토어드_함수이름();
```

권한 설정

```sql
SET GLOBAL log_bin_trust_function_creators = 1;
```

스토어드 함수 실습

```sql
USE sqlDB;
DROP FUNCTION IF EXISTS getAgeFunc;

-- 출생년도를 받아서 현재 나이를 반환하는 함수
DELIMITER $$
CREATE FUNCTION getAgeFunc(bYear INT)
    RETURNS INT
BEGIN
    DECLARE age INT;
    SET age = YEAR(CURDATE()) - bYear;
    RETURN age;
END $$
DELIMITER ;

-- 사용
SELECT getAgeFunc(1979);

-- 함수의 결과를 변수로 저장
SELECT getAgeFunc(1979) INTO @age1979;
SELECT getAgeFunc(1997) INTO @age1997;

-- 나이차이
SELECT CONCAT('1997년과 1979년의 나이차 ==> ', (@age1979-@age1997));

-- 만 나이를 출력
SELECT userID, name, getAgeFunc(birthYear) AS '만 나이' FROM userTBL;

-- 함수의 내용 보기
SHOW CREATE FUNCTION getAgeFunc;

-- 함수 삭제
DROP FUNCTION getAgeFunc;
```

### 커서

스토어드 프로시저 내부에 커서를 사용 할 수 있음

파일포인터처럼 한 줄씩 읽어올 때 사용

처리 순서

1. 커서의 선언(DECLARE CURSOR)
2. 반복 조건 선언(DECLARE CONTINUE HANDLER): 더 이상 읽을 행이 없을 경우에 실행할 내용 설정
3. 커서 열기(OPEN)
4. 커서에서 데이터 가져오기(FETCH) 
5. 데이터 처리(PROCESSING)
6. 커서 닫기(CLOSE)

커서 실습

```sql
DROP PROCEDURE IF EXISTS cursorProc;

-- 고객의 키의 평균을 구하는 커서
DELIMITER $$
CREATE PROCEDURE cursorProc()
BEGIN
    DECLARE userHeight INT; -- 고객의 키
    DECLARE cnt INT DEFAULT 0; -- 고객의 인원 수(=읽은 행의 수)
    DECLARE totalHeight INT DEFAULT 0; -- 키의 합계

    DECLARE endOfRow BOOLEAN DEFAULT FALSE; -- 행의 끝 여부(기본을 FALSE)

    -- 커서 선언
    DECLARE userCuror CURSOR FOR
        SELECT height FROM userTBL;

    DECLARE CONTINUE HANDLER -- 행의 끝이면 endOfRow 변수에 TRUE를 대입 
        FOR NOT FOUND SET endOfRow = TRUE;

    -- 커서 열기
    OPEN userCuror;

    cursor_loop: LOOP
        FETCH  userCuror INTO userHeight;  -- 고객 키 1개를 대입

        IF endOfRow THEN  -- 더이상 읽을 행이 없으면 Loop를 종료
            LEAVE cursor_loop;
        END IF;

        SET cnt = cnt + 1;
        SET totalHeight = totalHeight + userHeight;        
    END LOOP cursor_loop;
    
    -- 고객 키의 평균을 출력한다.
    SELECT CONCAT('고객 키의 평균 ==> ', (totalHeight/cnt));

    CLOSE userCuror;  -- 커서 닫기
END $$
DELIMITER ;

-- 커서 부르기
CALL cursorProc();

-- 고객 등급 정하기
USE sqlDB;
ALTER TABLE userTBL ADD grade VARCHAR(5);  -- 고객 등급 열 추가

-- 등급 만들기
DROP PROCEDURE IF EXISTS gradeProc;
DELIMITER $$
CREATE PROCEDURE gradeProc()
BEGIN
    DECLARE id VARCHAR(10);  -- 사용자 아이디를 저장할 변수
    DECLARE hap BIGINT;  -- 총 구매액을 저장할 변수
    DECLARE userGrade CHAR(5);  -- 고객 등급 변수
    
    DECLARE endOfRow BOOLEAN DEFAULT FALSE; 

    DECLARE userCuror CURSOR FOR  -- 커서 선언
        SELECT U.userid, sum(price*amount)
            FROM buyTBL B
                RIGHT OUTER JOIN userTBL U  -- 유령 고객도 찾으려고 OUTER JOIN
                ON B.userid = U.userid
            GROUP BY U.userid, U.name ;

    DECLARE CONTINUE HANDLER 
        FOR NOT FOUND SET endOfRow = TRUE;

    OPEN userCuror;  -- 커서 열기
    grade_loop: LOOP
        FETCH  userCuror INTO id, hap;  -- 첫 행 값을 대입
        IF endOfRow THEN
            LEAVE grade_loop;
        END IF;

        CASE  
            WHEN (hap >= 1500) THEN SET userGrade = '최우수고객';
            WHEN (hap  >= 1000) THEN SET userGrade ='우수고객';
            WHEN (hap >= 1) THEN SET userGrade ='일반고객';
            ELSE SET userGrade ='유령고객';
         END CASE;

        UPDATE userTBL SET grade = userGrade WHERE userID = id;
    END LOOP grade_loop;

    CLOSE userCuror;  -- 커서 닫기
END $$
DELIMITER ;

-- 호출
CALL gradeProc();

-- 조회
SELECT * FROM userTBL;
```

## 10-03 트리거 개념과 실습

### 트리거

- 정의: 이벤트가 발생 시 자동으로 작동되는 개체
- 활용사례: A테이블의 행 삭제되는 순간 B테이블에 로그를 남김
- 데이터 무결성을 위해 MySQL에서 사용할 수 있는 또 다른 기능
- DML의 이벤트가 발생될 때 자동으로 작동하는 데이터베이스 개체 중 하나
- 트리거는 테이블에 부착되는 프로그램 코드
- 스토어드 프로시저와 거의 비슷한 문법을 가짐
- 다른 DBMS는 View에도 트리거 부착 가능, MySQL은 View에는 불가능
- 트리거는 해당 테이블이 이벤트가 발생한 경우에만 실행
- 스토어드 프로시저와 다르게 IN, OUT 매개 변수를 사용할 수 없음
- 트리거 삭제는 `DROP TRIGGER`, 트리거는 수정이 불가 ~~`ALTER TRIGGER`~~

```sql
REATE DATABASE IF NOT EXISTS testDB;
USE testDB;
CREATE TABLE IF NOT EXISTS testTbl (id INT, txt VARCHAR(10));
INSERT INTO testTbl VALUES(1, '레드벨벳');
INSERT INTO testTbl VALUES(2, '잇지');
INSERT INTO testTbl VALUES(3, '블랙핑크');

DROP TRIGGER IF EXISTS testTrg;
DELIMITER // 
CREATE TRIGGER testTrg  -- 트리거 이름
    AFTER  DELETE -- 삭제후에 작동하도록 지정
    ON testTbl -- 트리거를 부착할 테이블
    FOR EACH ROW -- 각 행마다 적용시킴
BEGIN
	SET @msg = '가수 그룹이 삭제됨' ; -- 트리거 실행시 작동되는 코드들
END // 
DELIMITER ;

SET @msg = '';
INSERT INTO testTbl VALUES(4, '마마무');
SELECT @msg;
UPDATE testTbl SET txt = '블핑' WHERE id = 3;
SELECT @msg;
DELETE FROM testTbl WHERE id = 4;
SELECT @msg;
```

### 트리거의 종류(DML 트리거)

- trigger_time: AFTER, BEFORE
- trigger_event: INSERT, UPDATE, DELETE
- trigger_order: FOLLOWS, PRECEDES

```sql
USE sqlDB;
DROP TABLE buyTbl; -- 구매테이블은 실습에 필요없으므로 삭제.

-- 백업 테이블 생성
CREATE TABLE backup_userTbl
( userID  CHAR(8) NOT NULL PRIMARY KEY, 
  name    VARCHAR(10) NOT NULL, 
  birthYear   INT NOT NULL,  
  addr	  CHAR(2) NOT NULL, 
  mobile1	CHAR(3), 
  mobile2   CHAR(8), 
  height    SMALLINT,  
  mDate    DATE,
  modType  CHAR(2), -- 변경된 타입. '수정' 또는 '삭제'
  modDate  DATE, -- 변경된 날짜
  modUser  VARCHAR(256) -- 변경한 사용자
);

-- 업데이트 되면 백업테이블에 저장할 트리거 생성
DROP TRIGGER IF EXISTS backUserTbl_UpdateTrg;
DELIMITER // 
CREATE TRIGGER backUserTbl_UpdateTrg  -- 트리거 이름
    AFTER UPDATE -- 변경 후에 작동하도록 지정
    ON userTBL -- 트리거를 부착할 테이블
    FOR EACH ROW 
BEGIN
    INSERT INTO backup_userTbl VALUES( OLD.userID, OLD.name, OLD.birthYear, 
        OLD.addr, OLD.mobile1, OLD.mobile2, OLD.height, OLD.mDate, 
        '수정', CURDATE(), CURRENT_USER() );
END // 
DELIMITER ;

-- 삭제 되면 백업테이블에 저장할 트리거 생성
DROP TRIGGER IF EXISTS backUserTbl_DeleteTrg;
DELIMITER // 
CREATE TRIGGER backUserTbl_DeleteTrg  -- 트리거 이름
    AFTER DELETE -- 삭제 후에 작동하도록 지정
    ON userTBL -- 트리거를 부착할 테이블
    FOR EACH ROW 
BEGIN
    INSERT INTO backup_userTbl VALUES( OLD.userID, OLD.name, OLD.birthYear, 
        OLD.addr, OLD.mobile1, OLD.mobile2, OLD.height, OLD.mDate, 
        '삭제', CURDATE(), CURRENT_USER() );
END // 
DELIMITER ;

-- 트리거 자동 동작
UPDATE userTbl SET addr = '몽고' WHERE userID = 'JKW';
DELETE FROM userTbl WHERE height >= 177;

-- 백업테이블 조회
SELECT * FROM backup_userTbl;

-- 행 모두 지우기(DDL문이여서 트리거 작동x)
TRUNCATE TABLE userTbl;

SELECT * FROM backup_userTbl;

-- 수정시 오류 강제 발생
DROP TRIGGER IF EXISTS userTbl_InsertTrg;
DELIMITER // 
CREATE TRIGGER userTbl_InsertTrg  -- 트리거 이름
    AFTER INSERT -- 입력 후에 작동하도록 지정
    ON userTBL -- 트리거를 부착할 테이블
    FOR EACH ROW 
BEGIN
    SIGNAL SQLSTATE '45000'  -- 사용자 오류 강제 발생
        SET MESSAGE_TEXT = '데이터의 입력을 시도했습니다. 귀하의 정보가 서버에 기록되었습니다.';
END // 
DELIMITER ;

-- 오류 발생(적용 안됨)
INSERT INTO userTbl VALUES('ABC', '에비씨', 1977, '서울', '011', '1111111', 181, '2019-12-25');
```

## 10-04 트리거 임시 테이블, 중첩 트리거

INSERT, UPDATE, DELETE 작업이 수행되면 임시로 사용되는 시스템 테이블이 2개 있음

`NEW`, `OLD` 테이블임

```sql
USE sqlDB;
DROP TRIGGER IF EXISTS userTbl_BeforeInsertTrg;

-- 출생년도가 1900보다 작으면 0으로 현재보다 크면 현재날짜로 하는 트리거
DELIMITER // 
CREATE TRIGGER userTbl_BeforeInsertTrg  -- 트리거 이름
    BEFORE INSERT -- 입력 전에 작동하도록 지정
    ON userTBL -- 트리거를 부착할 테이블
    FOR EACH ROW 
BEGIN
    IF NEW.birthYear < 1900 THEN
        SET NEW.birthYear = 0;
    ELSEIF NEW.birthYear > YEAR(CURDATE()) THEN
        SET NEW.birthYear = YEAR(CURDATE());
    END IF;
END // 
DELIMITER ;

-- 출생년도 1877, 2977 넣기
INSERT INTO userTbl VALUES
  ('AAA', '에이', 1877, '서울', '011', '1112222', 181, '2022-12-25');
INSERT INTO userTbl VALUES
  ('BBB', '비이', 2977, '경기', '011', '1113333', 171, '2019-3-25');

-- 잘 들어간 값을 볼 수 있음
SELECT * FROM userTBL;

-- 트리거 목록 보기
SHOW TRIGGERS FROM sqlDB;

-- 트리거 삭제
DROP TRIGGER userTbl_BeforeInsertTrg;
```

### 다중 트리거(Multiple Triggers)

- 하나의 테이블에 동일한 트리거가 여러 개 부착되어 있는 것을 말함
- 예: AFTER INSERT 트리거가 한 개 테이블에 2개 이상 부착되어 있을 수도 있음

### 중첩 트리거(Nested Triggers)

- 트리거 안에 다른 트리거를 작동 시키는 것
- 고객이 구매 테이블 추가(+1) -> 물품 테이블 감소(-1) -> 배송 테이블 건수 추가

```sql
-- 트리거 DB 생성
DROP DATABASE IF EXISTS triggerDB;
CREATE DATABASE IF NOT EXISTS triggerDB;

USE triggerDB;
-- 테이블 3개 만들기
CREATE TABLE orderTbl -- 구매 테이블
	(orderNo INT AUTO_INCREMENT PRIMARY KEY, -- 구매 일련번호
          userID VARCHAR(5), -- 구매한 회원아이디
	 prodName VARCHAR(5), -- 구매한 물건
	 orderamount INT );  -- 구매한 개수
CREATE TABLE prodTbl -- 물품 테이블
	( prodName VARCHAR(5), -- 물건 이름
	  account INT ); -- 남은 물건수량
CREATE TABLE deliverTbl -- 배송 테이블
	( deliverNo  INT AUTO_INCREMENT PRIMARY KEY, -- 배송 일련번호
	  prodName VARCHAR(5), -- 배송할 물건		  
	  account INT UNIQUE); -- 배송할 물건개수

-- 물품 갯수 추가
INSERT INTO prodTbl VALUES('사과', 100);
INSERT INTO prodTbl VALUES('배', 100);
INSERT INTO prodTbl VALUES('귤', 100);

-- 물품 테이블에서 개수를 감소시키는 트리거
DROP TRIGGER IF EXISTS orderTrg;
DELIMITER // 
CREATE TRIGGER orderTrg  -- 트리거 이름
    AFTER  INSERT 
    ON orderTBL -- 트리거를 부착할 테이블
    FOR EACH ROW 
BEGIN
    UPDATE prodTbl SET account = account - NEW.orderamount 
        WHERE prodName = NEW.prodName ;
END // 
DELIMITER ;

-- 배송테이블에 새 배송 건을 입력하는 트리거
DROP TRIGGER IF EXISTS prodTrg;
DELIMITER // 
CREATE TRIGGER prodTrg  -- 트리거 이름
    AFTER  UPDATE 
    ON prodTBL -- 트리거를 부착할 테이블
    FOR EACH ROW 
BEGIN
    DECLARE orderAmount INT;
    -- 주문 개수 = (변경 전의 개수 - 변경 후의 개수)
    SET orderAmount = OLD.account - NEW.account;
    INSERT INTO deliverTbl(prodName, account)
        VALUES(NEW.prodName, orderAmount);
END // 
DELIMITER ;

-- JOHN이 배 5개 주문
INSERT INTO orderTbl VALUES (NULL,'JOHN', '배', 5);

-- JOHN이 배 5개 주문
SELECT * FROM orderTbl;
-- 상품에서 배가 5개 줄어듬(95개)
SELECT * FROM prodTbl;
-- 배송에는 배가 5개 행 추가
SELECT * FROM deliverTbl;

-- 열 이름을 바꿔봄
ALTER TABLE deliverTBL CHANGE prodName productName VARCHAR(5);

-- 트리거는 여전히 존재하지만 열 이름이 바꼈음
INSERT INTO orderTbl VALUES (NULL, 'DANG', '사과', 9);

-- 조회해보면 트리거 모두가 동작되지 않고 롤백되어서 모두 작동 안한 것을 볼 수 있음
SELECT * FROM orderTbl;
SELECT * FROM prodTbl;
SELECT * FROM deliverTbl;
```
