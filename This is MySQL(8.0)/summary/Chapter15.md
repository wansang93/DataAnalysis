# Chapter 15 파이썬과 MySQL 응용

## 15-01 파이썬 개발 환경 구축

설치 다 알아서 패스

## 15-02 파이썬 기초 문법

기초 문법 패스

## 15-03 리스트(배열), 문자열, map함수

기초 문법 패스

## 15-04 함수, 윈도 프로그래밍

기초 문법 패스

## 15-05 파이썬과 MySQL 연동

cmd창에서 pymysql 다운받기

```cmd
pip install pymysql
```

### 데이터 입력하기

MySQL DB 생성

```sql
DROP DATABASE IF EXISTS hanbitDB;
CREATE DATABASE hanbitDB;

use hanbitdb;

DROP TABLE IF EXISTS usertable;

CREATE TABLE userTable (
	ID CHAR(12) PRIMARY KEY,
    userName VARCHAR(20) NOT NULL,
    useremail VARCHAR(30) NOT NULL,
    userbirth INT
);

SELECT * FROM usertable;
```
MySQL 연결하는 방법

```python
import pymysql

# 전역변수 선언부
conn, cur = None, None
data1, data2, data3, data4 = "", "", "", ""
sql=""

# 메인 코드
conn = pymysql.connect(host='127.0.0.1', user='root', password='1234', db='hanbitDB', charset='utf8')
cur = conn.cursor()

while True:
    data1 = input("사용자 ID ==> ")
    if data1 == "":
        break
    data2 = input("사용자 이름 ==> ")
    data3 = input("사용자 이메일 ==> ")
    data4 = input("사용자 출생연도 ==> ")
    sql = f"INSERT INTO userTable VALUES('{data1}', '{data2}', '{data3}', '{data4}')"
    cur.execute(sql)

conn.commit()
conn.close()
```

### 데이터 조회하기

```python
import pymysql

# 전역변수 선언부
con, cur = None, None
data1, data2, data3, data4 = "", "", "", ""
row=None

# 메인 코드
conn = pymysql.connect(host='127.0.0.1', user='root', password='1234', db='hanbitDB', charset='utf8')
cur = conn.cursor()

cur.execute("SELECT * FROM userTable")

print("사용자ID  사용자이름       이메일                         출생연도")
print("------------------------------------------------------------------")

while (True) :
    row = cur.fetchone()
    if row== None :
        break
    data1 = row[0]
    data2 = row[1]
    data3 = row[2]
    data4 = row[3]
    # print("%5s   %15s   %15s   %5d" % (data1, data2, data3, data4))
    print(f"{data1:10s} {data2:15s} {data3:30s} {data4:5d}")

conn.close()
```

## 15-06 공간데이터 조회 응용프로그램

생략
