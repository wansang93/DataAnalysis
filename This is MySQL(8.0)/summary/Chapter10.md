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

### 스토어드 함수

### 커서

## 10-03 트리거 개념과 실습

## 10-04 트리거 임시 테이블, 중첩 트리거
