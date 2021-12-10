# Chapter 14 공간 데이터

## 14-01 GIS 개념과 MySQL의 공간데이터

지리정보시스템: 구글맵, 카카오맵 등

공간 데이터(파일) + 속성 데이터(테이블) -> GIS 응용 프로그램

MySQL 5.0부터 공간 데이터를 지원!

`Geometry`라는 데이터 형식을 지원

공간 데이터에서는 점(Point), 선(line), 면(Polygon)등이  있음

공간 데이터로 저장하게 된다면 건물, 강 등이 하나의 개체(공간 데이터)가 되어서 query로 조회가 가능

예전에는 GIS 소프트웨어로 검색을 해야 했었음

공간 쿼리 결과를 공간 뷰어를 통해 보기 좋게 출력 가능

워크벤치의 `Special View`로 보면 됨

## 14-02 공간 데이터 실습, 공간 함수

```sql
DROP DATABASE IF EXISTS GisDB;
CREATE DATABASE GisDB;

-- 공간 데이터 속성이 있는 하천 테이블 생성
USE GisDB;
CREATE TABLE StreamTbl (
   MapNumber CHAR(10),  -- 지도일련번호
   StreamName CHAR(20),  -- 하천이름
   Stream GEOMETRY ); -- 공간 데이터(하천개체)

-- 하천 삽입
INSERT INTO StreamTbl VALUES ( '330000001' ,  '한류천', 
	ST_GeomFromText('LINESTRING (-10 30, -50 70, 50 70)'));
INSERT INTO StreamTbl VALUES ( '330000001' ,  '안양천', 
	ST_GeomFromText('LINESTRING (-50 -70, 30 -10, 70 -10)'));
INSERT INTO StreamTbl VALUES ('330000002' ,  '일산천', 
	ST_GeomFromText('LINESTRING (-70 50, -30 -30, 30 -60)'));

-- 건물 테이블 생성
CREATE TABLE BuildingTbl (
   MapNumber CHAR(10),  -- 지도일련번호
   BuildingName CHAR(20),  -- 건물이름
   Building GEOMETRY ); -- 공간 데이터(건물개체)
   
-- 건물 삽입
INSERT INTO BuildingTbl VALUES ('330000005' ,  '하나은행', 
	ST_GeomFromText('POLYGON ((-10 50, 10 30, -10 10, -30 30, -10 50))'));
INSERT INTO BuildingTbl VALUES ( '330000001' ,  '우리빌딩', 
	ST_GeomFromText('POLYGON ((-50 -70, -40 -70, -40 -80, -50 -80, -50 -70))'));
INSERT INTO BuildingTbl VALUES ( '330000002' ,  '디티오피스텔', 
	ST_GeomFromText('POLYGON ((40 0, 60 0, 60 -20, 40 -20, 40 0))'));

-- 하천 조회
SELECT * FROM StreamTbl;

-- 건물 조회
SELECT * FROM BuildingTbl;

-- 하천 길이가 140보다 큰 값 조회
SELECT * FROM StreamTbl WHERE ST_Length(Stream) > 140 ;

-- 건물 면적이 500보다 작은 빌딩 이름 조회
SELECT BuildingName, ST_AREA(Building) FROM BuildingTbl 
	WHERE ST_AREA(Building) < 500;

-- 하천과 빌딩 합치기
SELECT * FROM StreamTbl
UNION ALL
SELECT * FROM BuildingTbl;
```

### 공간 데이터 형식의 함수

- 문자 형식을 Geometry 형식으로 반환 `ST_GeomFromText()`
- Geometry 형식을 문자 형식으로 반환 `ST_AsText()`

```sql
SELECT StreamName, BuildingName, Building, Stream
    FROM BuildingTbl , StreamTbl 
    -- 빌딩과 천이 겹치면(Intersects) 1로 반환
    WHERE ST_Intersects(Building, Stream) = 1 AND StreamName = '안양천';

-- 강을 5m로 출력해라
SELECT ST_Buffer(Stream,5) FROM StreamTbl;
```

## 14-03 GIS 응용 프로젝트

```sql
DROP DATABASE IF EXISTS KingHotDB;
CREATE DATABASE KingHotDB;

USE KingHotDB;
-- [왕매워 짬뽕] 체인점 테이블 (총 매출액 포함)
CREATE TABLE Restaurant
(restID int auto_increment PRIMARY KEY,  -- 체인점 ID
 restName varchar(50),	        -- 체인점 이름
 restAddr varchar(50),	        -- 체인점 주소
 restPhone varchar(15),	        -- 체인점 전화번호
 totSales  BIGINT,		        -- 총 매출액			
 restLocation geometry ) ;	        -- 체인점 위치

-- [왕매워 짬뽕] 1호점~9호점 입력
INSERT INTO Restaurant VALUES
(NULL, '왕매워 짬뽕 1호점', '서울 강서구 방화동', '02-111-1111', 1000, ST_GeomFromText('POINT(-80 -30)')),
(NULL, '왕매워 짬뽕 2호점', '서울 은평구 증산동', '02-222-2222', 2000, ST_GeomFromText('POINT(-50 70)')),
(NULL, '왕매워 짬뽕 3호점', '서울 중랑구 면목동', '02-333-3333', 9000, ST_GeomFromText('POINT(70 50)')),
(NULL, '왕매워 짬뽕 4호점', '서울 광진구 구의동', '02-444-4444', 250, ST_GeomFromText('POINT(80 -10)')),
(NULL, '왕매워 짬뽕 5호점', '서울 서대문구 북가좌동', '02-555-5555', 1200, ST_GeomFromText('POINT(-10 50)')),
(NULL, '왕매워 짬뽕 6호점', '서울 강남구 논현동', '02-666-6666', 4000, ST_GeomFromText('POINT(40 -30)')),
(NULL, '왕매워 짬뽕 7호점', '서울 서초구 서초동', '02-777-7777', 1000, ST_GeomFromText('POINT(30 -70)')),
(NULL, '왕매워 짬뽕 8호점', '서울 영등포구 당산동', '02-888-8888', 200, ST_GeomFromText('POINT(-40 -50)')),
(NULL, '왕매워 짬뽕 9호점', '서울 송파구 가락동', '02-999-9999', 600, ST_GeomFromText('POINT(60 -50)'));

-- 체인점 점을 3칸으로 표현
SELECT restName, ST_Buffer(restLocation, 3) as '체인점' FROM Restaurant;

-- 지역 관리자 테이블
CREATE TABLE Manager
 (ManagerID int auto_increment PRIMARY KEY,   -- 지역관리자 id
  ManagerName varchar(5),	              -- 지역관리자 이름
  MobilePhone varchar(15),	              -- 지역관리자 전화번호
  Email varchar(40),                      -- 지역관리자 이메일
  AreaName varchar(15),                 -- 담당지역명
  Area geometry SRID 0) ;                       -- 담당지역 폴리곤

INSERT INTO Manager VALUES
 (NULL, '존밴이', '011-123-4567', 'johnbann@kinghot.com',  '서울 동/북부지역',
   ST_GeomFromText('POLYGON((-90 0, -90 90, 90 90, 90 -90, 0 -90, 0  0, -90 0))')) ,
 (NULL, '당탕이', '019-321-7654', 'dangtang@kinghot.com', '서울 서부지역',
   ST_GeomFromText('POLYGON((-90 -90, -90 90, 0 90, 0 -90, -90 -90))'));

SELECT ManagerName, Area as '당탕이' FROM Manager WHERE ManagerName = '당탕이';
SELECT ManagerName, Area as '존밴이' FROM Manager WHERE ManagerName = '존밴이';

-- 서울시의 도로 테이블
CREATE TABLE Road
 (RoadID int auto_increment PRIMARY KEY,  -- 도로 ID
  RoadName varchar(20),           -- 도로 이름
  RoadLine geometry );              -- 도로 선

INSERT INTO Road VALUES
 (NULL, '강변북로',
   ST_GeomFromText('LINESTRING(-70 -70 , -50 -20 , 30 30,  50 70)'));

SELECT RoadName, ST_BUFFER(RoadLine, 1) as '강변북로' FROM Road;

-- 지도로 보기
SELECT ManagerName, Area as '당탕이' FROM Manager WHERE ManagerName = '당탕이';
SELECT ManagerName, Area as '존밴이' FROM Manager WHERE ManagerName = '존밴이';
SELECT restName, ST_Buffer(restLocation, 3) as '체인점' FROM Restaurant;
SELECT RoadName, ST_BUFFER(RoadLine,1) as '강변북로' FROM Road;

-- 담당자 별로 면적 보기
SELECT ManagerName, AreaName, ST_Area(Area) as "면적 m2" FROM Manager;

-- 담당자별 관리 지점 출력
SELECT M.ManagerName,
       R.restName,
       R.restAddr,
       M.AreaName 
FROM Restaurant R, Manager M
WHERE ST_Contains(M.AREA, R.restLocation) = 1 
ORDER BY M.ManagerName;
-- 당탕이 4개, 존밴이 7개


SELECT R2.restName,
       R2.restAddr,
       R2.restPhone, 
       ST_Distance(R1.restLocation, R2.restLocation) AS "1호점에서 거리"
FROM Restaurant R1, Restaurant R2
WHERE R1.restName='왕매워 짬뽕 1호점'
ORDER BY ST_Distance(R1.restLocation, R2.restLocation) ;

-- 합친 지역 표시하기
SELECT Area INTO @eastNorthSeoul FROM Manager WHERE AreaName = '서울 동/북부지역';
SELECT Area INTO @westSeoul FROM Manager WHERE AreaName = '서울 서부지역';
SELECT ST_Union(@eastNorthSeoul, @westSeoul) AS  "모든 관리지역을 합친 범위" ;

-- 겹쳐지는 지역 표시하기
SELECT  Area INTO @eastNorthSeoul FROM Manager WHERE ManagerName = '존밴이';
SELECT  Area INTO @westSeoul FROM Manager WHERE ManagerName = '당탕이';
SELECT  ST_Intersection(@eastNorthSeoul, @westSeoul) INTO @crossArea ;
SELECT DISTINCT R.restName AS "중복 관리 지점"
    FROM Restaurant R, Manager M
    WHERE ST_Contains(@crossArea, R.restLocation) = 1;

-- 강변 북로 30M 이내 지점
SELECT  ST_Buffer(RoadLine, 30) INTO @roadBuffer FROM Road;
SELECT R.restName AS "강변북로 30M 이내 지점"
   FROM Restaurant R
   WHERE ST_Contains(@roadBuffer,R.restLocation) = 1;

-- 공간뷰로 보기
SELECT  ST_Buffer(RoadLine, 30) INTO @roadBuffer FROM Road;
SELECT  ST_Buffer(RoadLine, 30) as '강변북로 30m' FROM Road;
SELECT  ST_Buffer(R.restLocation, 5) as '체인점' -- 지점을 약간 크게 출력
   FROM Restaurant R
   WHERE ST_Contains(@roadBuffer, R.restLocation) = 1;
SELECT RoadLine as '강변북로' FROM Road;
```
