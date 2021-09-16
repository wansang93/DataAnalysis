# Chapter 01 DBMS 개요와 MySQL 소개

## 01-00 Orientation

### 이 강의는 ...  

MySQL or 데이터베이스를 처음 공부하는 사용자
초보자 -> 중급자로 올리는 개념  
추가적으로 PHP 기본, MySQL과 연동, Python, 공간데이터베이스를 배움

권장 실습 사항
- Intel Core 2세대 이후, RAM 8GB 이상  
- 내장 SSD 20GB 이상 여유

## 01-01 DBMS 개요

- 데이터베이스(DB): 데이터의 집합, 여러 명의 사용자가 동시에 접근 가능
- DBMS: 데이터베이스를 관리
- MySQL: DBMS 소프트웨어의 일종으로 Oracle사에서 제작한 툴

### 유명한 DBMS

MySQL, MariaDB, PostgreSQL, Oracle, SQL Server, DB2, Access, SQLite 가 있다.

### DBMS의 중요한 특징

- 데이터의 무결성
- 데이터의 독립성
- 보안
- 데이터 중복의 최소화

### DBMS의 탄생

파일시스템의 단점을 보완, 대량의 데이터를 관리 가능, SQL을 잘 해야함

## 01-02 DBMS 분류와 MySQL 소개

### DBMS 분류

- 계층형(Tree 구조): 빠르다, 접근의 유연성이 부족
- 망형(복잡한 포인터 구조): 1970년대 시작, ~~효과적~~, ~~빠르게 데이터 추출 가능~~, 복잡
- 객체지향형(계층에 따라 데이터 구조 표현, 비정형 정보타입 지원, 안정성 성능이 떨어짐)
- 객체관계형(객체지향 + 관계형 장점 활용)
- 관계형(대부분의 DBMS 사용)

### SQL 개요

초기 DB 회사들이 많이 생김, 회사마다 SQL이 멋대로야!

-> 표준 만들자! 표준 SQL 생김

## 나머지는 다 알아서 패스!