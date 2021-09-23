# Chapter 05 데이터베이스 모델링

## 05-01 MySQL Workbench 사용법 - 관리툴

### MySQL Workbench 특징

MySQL Workbench를 쓰면 vscode처럼 손쉽게 DB 관리 가능, 다양한 기능들

- DB 연결 기능
- SQL 편집기
- 모델링 기능
- 포워드/리버스 엔지니어링 기능
- 인스턴스 관리
  - DB 운용 측면에서 많이 유용함
  - MySQL 연결 정보 관리
  - MySQL 인스턴스의 중지, 시작
  - Server에 기록된 로그 조회
  - MySQL 옵션 파일 설정 정보 확인 및 변경
- 데이터베이스 내보내기/가져오기
- 데이터베이스 계정 관리

### MySQL Workbench 좋은 기능들

- `Table 우클릭` -> `Send to SQL Editor` 하면 빠르게 쿼리 작성

    생성된 view를 SQL로 만드는 법

    ```sql
    CREATE 
        ALGORITHM = UNDEFINED 
        DEFINER = `root`@`localhost` 
        SQL SECURITY DEFINER
    VIEW `shopdb`.`uv_membertbl` AS
        SELECT 
            `shopdb`.`membertbl`.`memberName` AS `memberName`,
            `shopdb`.`membertbl`.`memberAddress` AS `memberAddress`
        FROM
            `shopdb`.`membertbl`;
    ```

## 05-02 MySQL Workbench 사용법 - 쿼리창

1. 쿼리 실행: `Ctrl` + `Shift` + `Enter`
2. Fomatting: `Ctrl` + `B`
3. 주석: `Ctrl` + `/`
4. Limit to 1000 rows 를 바꿔주면 많이 볼 수 있음
5. `Edit` -> `Preferences`: 다양한 설정 가능(대문자, 글씨 크기 등)
6. 검색 후 Filter와 저장 가능

## 05-03 외부 MySQL 서버 관리 - Linux 서버

1. 서버에 MySQL 다운, 방화벽을 열어야 함!
2. IP번호, Port: 3306, 계정 이름 적고 로그인

## 05-04 사용자 관리

사용자 별 권한을 설정해서 DB를 관리할 것!

### 사용자 생성 및 권한 설정

1. `Administration` -> `User and Privileges` -> `Add Account`
2. `Administrative Roles`
   - `DBA`: DB관리자(DB Administrator)
   - `Global Privileges` -> `Select`: 읽기 권한만
3. `Schema Privileges` -> `Add Entry` -> 원하는 DB 선택 -> 권한 선택
   - `SELECT`, `INSERT`, ...
   - `CREATE ROUTINE`, `ALTER ROUTINE`, ...
