# Chapter 12 PHP 기본

## 12-01 웹 개발환경 구축

### 웹 사이트 구축

웹 서버, DB, 프로그래밍 언어 필수

예전 **웹 서버**는 `Apache`, **DB**는 `MySQL`, **웹 프로그래밍 언어**는 `PHP`로 많이 구현함

**XAMPP**는 `Apache`, `MySQL`, `PHP`를 한번에 설치해 충돌 문제를 해결하고 편리하게 설치 가능

PHP는 버전이 바뀌면 안되는 경향이 있음, 버전별 호환성 차이

XAMPP 서버 다운로드(Ver.7.3.9로 받기) 링크 -> <https://www.apachefriends.org/download.html>

다운받은 후 mysql에서 다음을 설정

```sql
-- sql 원활한 접속을 위해 mysql_native_password를 다음과 같이 설정
ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password by '1234';
```

`htdocs` 폴더에서 `info.php` 파일 생성 후 아래 내용 작성

```php
<?php
    $con=mysqli_connect("localhost", "root", "1234") or die("MySQL 접속 실패");
    phpinfo();
    mysqli_close($con);
?>
```

`http://localhost/info.php` 에서 접속해서 확인하기

## 12-02 HTML 태그, PHP 기초문법

### 서버 스크립트와 클라이언트 스크립트

- 옛날 코딩: HTML과 스크립트 전송
- 요즘 코딩: 서버 스크립트 -> HTML 코드를 전송

### HTML

패스

### PHP

```php
<?php
// 한 줄 주석용
/* 여러 줄
   주석용
*/

$a = 100;  // 변수 선언
print $a  // 출력1
echo $b  // 출력2

echo gettype($a)  // 타입 출력

$str3 = "SELECT * FROM userTBL WERHER userID='JYP'";
echo $str3;
?>
```
## 12-03 PHP 문법 및 내장 함수

```php
// if 문
<?php
$a = 100;
$b = 200;

if ($a > $b) {
    echo "a가 b보다 큽니다.";
} elseif ($a < $b) {
    echo "a가 b보다 작습니다";
} else {
    echo "a와 b가 같습니다";
}

// switch~case 문
$jumsu = 83;

switch(intval($jumsu / 10)){
    case 10:
    case 9:
        echo "A학점"; break;
    case 8:
        echo "B학점"; break;
    case 7:
        echo "C학점"; break;
    case 6:
        echo "D학점"; break;
    default:
        echo "F학점";
}

// for문
for ($i=1; $i<=10; $i=$i+1) {
    echo $i, " ";
}

// while문
$hap = 0;
$i = 123;
while ($i <= 456){
    $hap = $hap + $i;
    $i = $i + 2
}

// 배열 선언
$myArr = array(100, 'MySQL', 123.123)
$myArr2 = range(1, 10)

// 배열 값 가져오기
for ($i=0; i<10; $i++) {
    $hap = $hap + $myArr2[$i]
}

// 배열 값 하나씩 가져오기
foreach($myArr as $data)
  echo $data, " ";

// 나머지는 강의 참고...
?>
 ```

## 12-04 HTML과 PHP의 관계

### HTML과 PHP 데이터 전송 개념

1. 회원가입 버튼을 누르면 서버로 값 전달
2. 웹 서버에서 PHP파일이 실행 MySQL로 데이터 입력
3. MySQL 서버에 저장

`send.html` 파일

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>send</title>
</head>
<body>
    <form method="post" action="receive.php">
        <input type="text" name="userID">
        <input type="text" name="userName">
        <br><br>
        <input type="submit" value="전송">
    </form>
</body>
</html>
```

`receive.php` 파일

```php
<?php
    $userID = $_POST["userID"];
    $userName = $_POST["userName"];

    echo "전달 받은 아이디: ", $userID, "<br>";
    echo "전달 받은 이름: ", $userName, "<br>";
?>
```

HTML과 PHP 파일을 섞어서 사용 가능
