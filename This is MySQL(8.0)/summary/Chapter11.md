# Chapter 11 전체 텍스트검색과 파티션

## 11-01 전체 텍스트 검색 개념과 실습

### 전체 텍스트 검색(FULLTEXT Index)이란

- 구조화 되지 않은 텍스트 데이터(신문 기사 등)을 빠르게 검색하기 위한 부가 기능
- 첫 글짜뿐 아니라 중간의 단어나 문장으로도 인덱스를 생성해줘 검색 결과가 빨라짐
- 신문기사와 같이 텍스트로 이루어진 문자열 데이터의 내용을 가지고 생성한 인덱스

특징

- 전체 텍스트 인덱슨느 **InnoDB**와 **MyISAM 테이블**만 지원
- char, varchar, text 열에서만 생성 가능
- 인덱스 힌트의 사용이 일부 제한
- 여러 개 열에 FULL TEXT 인덱스를 지정 가능

형식

```sql
-- 전체 텍스트 생성
-- 형식 1
CREATE TABLE 테이블이름 (
    ...
    열 이름 데이터형식,
    ...,
    FULL TEXT 인덱스이름 (열 이름)
);

-- 형식 2
CREATE TABLE 테이블이름 (
    ...
    열 이름 데이터형식,
    ...,
    FULL TEXT 인덱스이름 (열 이름)
);
ALTER TABLE 테이블이름
    ADD FULLTEXT(열 이름);

-- 형식 3
CREATE TABLE 테이블이름 (
    ...
    열 이름 데이터형식,
    ...,
    FULL TEXT 인덱스이름 (열 이름)
);
CREATE FULLTEXT INDEX 인덱스이름
ON 테이블이름 (열 이름);

-- 전체 텍스트 삭제
ALTER TABLE 테이블이름
DROP INDEX FULLTEXT(열 이름);
```

- 중지 단어
  - 무시할 만한 단어(아주, 모두, 꼭 등)들은 아에 전체 텍스트 인텍스로 생성하지 않게 함
  - INFORMATION_SCHEMA.INNODB_FT_DEFAULTS_STOPWORD 테이블에 36개(a, about, an ...)가 들어가있음

### 전체 텍스트 검색을 위한 쿼리

전체 텍스트 검색을 위한 쿼리

```sql
MATCH (col1, col2, ...) AGAINST (expr [search_modifier])

search_modifier:
{
    IN NATURAL LANGUAGE MODE  -- 자연어 검색
  | IN NATURAL LANGUAGE MODE WITH QUERY EXPANSION
  | IN BOOLEAN NODE  -- 불린 모드 검색
  | WITH QUERY EXPANSION
}
```

- 자연어 검색
  - 자연어 검색이란 단어가 정확한 것을 검색해 줌
  - 특현한 옵션을 지정하지 않거나 IN NATURAL LANGUAGE MODE를 붙이면 자연어 검색을 함
  - 예시) `영화`로 검색하면 `영화가`, `영화는` 등을 검색하지 않음
    ```sql
    SELECT * FROM newspaper
    WHERE MATCH(article) AGANIST('영화 배우')
    ```

- 불린 모드 검색
  - 불린 모드 검색은 단어나 문장이 정확히 일치하지 않는 것도 검색 가능
  - IN BOOLEAN MODE 옵션을 붙여야 함
  - 예시) `영화*`로 검색하면 `영화가`, `영화는` 등을 검색 함
    ```sql
    SELECT * FROM newspaper
    WHERE MATCH(article) AGANIST('영화*' IN BOOLEAN MODE)
    
    -- 반드시(+) 공포 포함 검색
    SELECT * FROM newspaper
    WHERE MATCH(article) AGANIST('영화 +공포' IN BOOLEAN MODE)

    -- 선택안함(-) 남자 비포함 검색
    SELECT * FROM newspaper
    WHERE MATCH(article) AGANIST('영화 -남자' IN BOOLEAN MODE)
    ```

### 전체 실습

기본적으로 전체 텍스트 검색은 3글자 이상부터 검색이 가능함

2글자로 바꾸기 위해서 설정을 변경

```powershell
# powershell 관리자모드로 들어가서 cmd를 열면 관리자로 들어감
cmd
cd %programdata%
cd MySQL
cd "MySQL Server 8.0"
notepad my.ini
# 제일 밑에 다음을 추가하고 저장
innodb_ft_min_token_size=2
# MySQL 재시작
net stop mysql80
net start mysql80
```

다시 SQL로 돌아와서

```sql
-- 검색어 최소 갯수(2)
SHOW VARIABLES LIKE 'innodb_ft_min_token_size';

-- DB 생성
CREATE DATABASE IF NOT EXISTS FulltextDB;
USE FulltextDB;

--TB 생성
DROP TABLE IF EXISTS FulltextTbl;
CREATE TABLE FulltextTbl ( 
  id int AUTO_INCREMENT PRIMARY KEY,  -- 고유 번호
  title VARCHAR(15) NOT NULL,   -- 영화 제목
  description VARCHAR(1000)   -- 영화 내용 요약
);

-- 데이터 삽입
INSERT INTO FulltextTbl VALUES
(NULL, N'광해, 왕이 된 남자', N'왕위를 둘러싼 권력 다툼과 당쟁으로 혼란이 극에 달한 광해군 8년'),
(NULL, N'간첩', N'남한 내에 고장간첩 5만 명이 암약하고 있으며 특히 권력 핵심부에도 침투해있다.'),
(NULL, N'남자가 사랑할 때', N'대책 없는 한 남자이야기. 형 집에 얹혀 살며 조카한테 무시당하는 남자'),
(NULL, N'레지던트 이블 5', N'인류 구원의 마지막 퍼즐, 이 여자가 모든 것을 끝낸다.'),
(NULL, N'파괴자들', N'사랑은 모든 것을 파괴한다! 한 여자를 구하기 위한, 두 남자의 잔인한 액션 본능!'),
(NULL, N'킹콩을 들다', N' 역도에 목숨을 건 시골소녀들이 만드는 기적 같은 신화.'),
(NULL, N'테드', N'지상최대 황금찾기 프로젝트! 500년 전 사라진 황금도시를 찾아라!'),
(NULL, N'타이타닉', N'비극 속에 침몰한 세기의 사랑, 스크린에 되살아날 영원한 감동'),
(NULL, N'8월의 크리스마스', N'시한부 인생 사진사와 여자 주차 단속원과의 미묘한 사랑'),
(NULL, N'늑대와 춤을', N'늑대와 친해져 모닥불 아래서 함께 춤을 추는 전쟁 영웅 이야기'),
(NULL, N'국가대표', N'동계올림픽 유치를 위해 정식 종목인 스키점프 국가대표팀이 급조된다.'),
(NULL, N'쇼생크 탈출', N'그는 누명을 쓰고 쇼생크 감옥에 감금된다. 그리고 역사적인 탈출.'),
(NULL, N'인생은 아름다워', N'귀도는 삼촌의 호텔에서 웨이터로 일하면서 또 다시 도라를 만난다.'),
(NULL, N'사운드 오브 뮤직', N'수녀 지망생 마리아는 명문 트랩가의 가정교사로 들어간다'),
(NULL, N'매트릭스', N' 2199년.인공 두뇌를 가진 컴퓨터가 지배하는 세계.');

-- 남자가 있는거 검색
SELECT * FROM FulltextTbl WHERE description LIKE '%남자%' ;
-- FULL Table Scan(속도가 엄청 걸림)

-- FULLTEXT 인덱스 생성
CREATE FULLTEXT INDEX idx_description ON FulltextTbl(description);

-- FULLTEXT 인덱스 생성한지 보기
SHOW INDEX FROM FulltextTbl;

-- 전체 텍스트 검색함
SELECT * FROM FulltextTbl WHERE MATCH(description) AGAINST('남자*' IN BOOLEAN MODE);
-- Fulltext index Search(속도가 빠름)


EXPLAIN SELECT * FROM FulltextTbl WHERE MATCH(description) AGAINST('남자*' IN BOOLEAN MODE);

-- 남자 여자가 얼마나 나왔는지 점수로 표현
SELECT *, MATCH(description) AGAINST('남자* 여자*' IN BOOLEAN MODE) AS 점수 
  FROM FulltextTbl WHERE MATCH(description) AGAINST('남자* 여자*' IN BOOLEAN MODE);

-- 남자 여자 둘다 있는 것만 검색
SELECT * FROM FulltextTbl 
  WHERE MATCH(description) AGAINST('+남자* +여자*' IN BOOLEAN MODE);

-- 남자가 들어있고 여자는 안들어있는 검색
SELECT * FROM FulltextTbl 
  WHERE MATCH(description) AGAINST('남자* -여자*' IN BOOLEAN MODE);

-- 전체 텍스트 인덱스로 만들어진 단어들 확인하기
SET GLOBAL innodb_ft_aux_table = 'fulltextdb/fulltexttbl'; -- 모두 소문자

-- 전체 텍스트 인덱스 조회
SELECT word, doc_count, doc_id, position 
  FROM INFORMATION_SCHEMA.INNODB_FT_INDEX_TABLE;

-- 기존 전체 텍스트 인덱스 삭제
DROP INDEX idx_description ON FulltextTbl;

-- 중지단어를 위한 테이블 생성
CREATE TABLE user_stopword (value VARCHAR(30));

-- 중지단어 추가
INSERT INTO user_stopword VALUES ('그는'), ('그리고'), ('극에');

-- 중지단어 테이블을 추가함
SET GLOBAL innodb_ft_server_stopword_table = 'fulltextdb/user_stopword'; 

-- 중지단어 추가됬는지 조회
SHOW GLOBAL VARIABLES LIKE 'innodb_ft_server_stopword_table';

-- 전체 텍스트 인덱스 만들기
CREATE FULLTEXT INDEX idx_description ON FulltextTbl(description);

-- 검색하면 중지단어가 제거된 것을 볼 수 있음
SELECT word, doc_count, doc_id, position 
  FROM INFORMATION_SCHEMA.INNODB_FT_INDEX_TABLE;
```

## 11-02 파티션 개념과 실습
