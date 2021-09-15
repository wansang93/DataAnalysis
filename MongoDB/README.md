# MongoDB

- manual

MongoDB에서는 테이블을 컬렉션이라고 호칭

select는 find로 실행

CURD, 기존 사용하는 고급 쿼리를 어떻게 할지

index 설정을 어떻게 할지

MongoDB의 유래 -> 휴몽고스(거대한)에서 유래

사용 쉽고, 데이터 구조가 유연, 분산서버(샤딩) -> 속도를 요하는 시스템에 적합

대부분의 DB 툴들이 데이터 폴더를 따로 지정 가능 -> 백업, 복구시 활용

## MongoDB 환경변수 설정

1. 다운로드 링크 -> [https://www.mongodb.com/try/download/community](https://www.mongodb.com/try/download/community)
2. 환경변수 설정
   - 실행창에서 단축키:  `sysdm.cpl ,3`
   - Path에 다음 추가 `C:\Program Files\MongoDB\Server\5.0\bin`
3. MongoDB 폴더에 DB폴더를 생성(추천) `C:\Program Files\MongoDB\data`
4. MongoDB 폴더에 log폴더를 생성(추천) `C:\Program Files\MongoDB\log`
5. MongoDB에서 데이터 파일 경로 지정
   - powershell 관리자 권한에서 아래를 실행
   - `"C:\Program Files\MongoDB\Server\5.0\bin\mongo.exe" --dbpath "C:\Program Files\MongoDB\data"`
   - `mongod --dbpath C:\Program Files\MongoDB\data"`

dbpath = C:\Program Files\MongoDB\data

MongoDB는 C++로 개발되었음, 많은 DB가 C++로 작성됨, 왜냐면 Windows나 Unix, Ninux가 C로 작성되었음

NoSQL DB중 인지도 1위, 가장 유명하고 많이쓰고 안정화 되있음

10GEN 회사에서 2007년 처음 개발, 2009년 오픈소스로 전향한 DB, 관리는 10GEN에서 관리

대용량 빅데이터에 적합

구조는 내부 로직이 JSON 형태의 데이터 구조를 가짐

NoSQL = No Structured Query Language(구조가 없는 질의 언어)

Table에서 스키마(Schema)를 가지고 있지 않음

몽고DB 테이블(Table)을 콜렉션(Collection), 데이터(Row)를 도큐먼트(Document)라고 부림

데이터라고 표현하지 않고 도큐멘트라고 이유는? 지정된 형태(Data의 특징)로 넣는것이 아니라 다양한 형태로 넣을 수 있기 때문에

RDBMS Table의 구조: 스키마(Schema)를 가지고 있고 고정된 구조

MongoDB는 자유로운 구조임

## Robomongo 관리툴 사용하기

난해한 구조인 Json 형태의 데이터를 일목요연하게 보여줌

컬렉션, 도큐먼트 관리가 쉬움

## Database, Collections 관리

```
show dbs
use testdb 
db.createCollection("test")
show tables
```
