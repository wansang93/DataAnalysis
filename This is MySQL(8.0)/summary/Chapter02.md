# Chapter 02 MySQL 설치

## 02-01 MySQL 8.0 설치

설치 링크 -> [https://dev.mysql.com/downloads/mysql/](https://dev.mysql.com/downloads/mysql/)

설치 잘하면 powershell 관리자모드로 열기

환경 변수 추가하기(아래는 명령어)

```powershell
cmd
SETX PATH "C:\Program Files\MySQL\MySQL Server 8.0\bin;%PATH%"
```

## 02-02 샘플 데이터베이스 생성, MySQL 제거

employees DB 다운로드 관련 링크 -> [https://dev.mysql.com/doc/employee/en/](https://dev.mysql.com/doc/employee/en/)

Windows에서는 관련 링크에서 Github 페이지로 가서 압축파일로 다운함

다운로드 후 powershell 관리자모드에서

```powershell
cmd  # cmd창 열기
# ![필수] cd 다운받은 경로에서
mysql -u root -p  # mysql 접속
# 비밀번호 입력
source employees.sql  # 설치
# 설치 확인
show databases;
```
