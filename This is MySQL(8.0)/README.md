# 이것이 MySQL이다(8.0)

- 2020-11-09 (Finish Chapter01)
- 2021-09-15 ~ 17 (Finish Chapter02~04)
- 2021-09-23 ~ 27 (Finish Chapter05~06)
- 2021-10-22 ~ 2021-11- (Finish Chapter07~11)

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
- ~~[Chapter 12 PHP 기본](./summary/Chapter12.md)~~
  - 12-01 웹 개발환경 구축
  - 12-02 HTML 태그, PHP 기초문법
  - 12-03 PHP 문법 및 내장 함수
  - 12-04 HTML과 PHP의 관계
- ~~[Chapter 13 PHP와 MySQL 연동](./summary/Chapter13.md)~~
  - 13-01 기본 연동 방법
  - 13-02 회원 관리 시스템 구현
- ~~[Chapter 14 공간 데이터](./summary/Chapter14.md)~~
  - 14-01 GIS 개념과 MySQL의 공간데이터
  - 14-02 공간 데이터 실습, 공간 함수
  - 14-03 GIS 응용 프로젝트
- ~~[Chapter 15 파이썬과 MySQL 응용](./summary/Chapter15.md)~~
  - 15-01 파이썬 개발 환경 구축
  - 15-02 파이썬 기초 문법
  - 15-03 리스트(배열), 문자열, map함수
  - 15-04 함수, 윈도 프로그래밍
  - 15-05 파이썬과 MySQL 연동
  - 15-06 공간데이터 조회 응용프로그램

## Quick Start

### DB를 시작하자마자 DB와 Table 조회하기

```sql
SHOW DATABASES;  -- DATABASES 목록 보여주세요
USE db_name;  -- 사용할 DB를 선택하세요
SHOW TABLE STATUS;  -- table 상세보기로 보여주세요
DESCRIBE table_name;  -- 해당 table의 field 타입을 보여주세요
-- DESC table_name;
```

## 마지막으로

이런 좋은 강의를 무료로 제공해주신 한빛미디어 및 우재남선생님께 감사드립니다.😊
