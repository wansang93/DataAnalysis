# Chapter 13 PHP와 MySQL 연동

## 13-01 기본 연동 방법

### DB로 접속 확인

```php
<?php
   $db_host="localhost";
   $db_user="root";
   $db_password="1234";
   $db_name="";
   $con=mysqli_connect($db_host, $db_user, $db_password, $db_name);
   if ( mysqli_connect_error($con) ) {
       echo "MySQL 접속 실패 !!", "<br>";
       echo "오류 원인 : ", mysqli_connect_error();
       exit();
   }
   echo "MySQL 접속 완전히 성공!!";
   mysqli_close($con);
?>
```

```php
// 간단히
<?php
   $con=mysqli_connect("localhost", "root", "1234", "") or die("접속실패");
   echo "MySQL 접속 완전히 성공!!";
   mysqli_close($con);
?>
```

```php
// 13-02 실습
<?php
    $con=mysqli_connect("localhost", "root", "1234", "") or die("접속실패");

    $sql = "CREATE DATABASE sqldb";
    $ret = mysqli_query($con, $sql);

    if ($ret) {
        echo "sqlDB가 성공적으로 생성됨.";
    }
    else {
        echo "sqlDB 생성 실패!!"."<br>";
        echo "실패 원인: ".mysqli_error($con);
    }

    mysqli_close($con);
?>
```

```php
// db에 바로 연결 후 테이블에 행 추가
<?php
    $con=mysqli_connect("localhost", "root", "1234", "sqlDB") or die("MySQL 접속 실패 !!");

    $sql ="
    CREATE TABLE userTbl (
    userID      CHAR(8) NOT NULL PRIMARY KEY,
    name         VARCHAR(10) NOT NULL,
    birthYear    INT NOT NULL,
    addr          CHAR(2) NOT NULL,
    mobile1    CHAR(3),
    mobile2    CHAR(8),
    height         SMALLINT,
    mDate         DATE
    )
    ";

    $ret = mysqli_query($con, $sql);

    if($ret) {
        echo "userTBL이 성공적으로 생성됨..";
    }
    else {
        echo "userTBL 생성 실패!!!"."<br>";
        echo "실패 원인 :".mysqli_error($con);
    }

    mysqli_close($con);
?>
```

```php
// select문으로 가져오기
<?php
    $con=mysqli_connect("localhost", "root", "1234", "sqlDB") or die("MySQL 접속 실패 !!");

    $sql ="
    SELECT * FROM userTBL
    ";

    $ret = mysqli_query($con, $sql);

    if($ret) {
    echo mysqli_num_rows($ret), "건이 조회됨.<br><br>";
    }
    else {
    echo "userTBL 데이터 조회 실패!!!"."<br>";
    echo "실패 원인 :".mysqli_error($con);
    exit();
    }

    // 해당 구문으로 모든 열을 가져옴
    while($row = mysqli_fetch_array($ret)) {
        echo $row['userID'], " ", $row['name'], " ", $row['height'], " ", "<br>";
    }

    mysqli_close($con);
?>
```

## 13-02 회원 관리 시스템 구현

회원 관리 시스템(CRUD)

### 회원 조회 화면

```html
<HTML>
<HEAD>
<META http-equiv="content-type" content="text/html; charset=utf-8">
</HEAD>
<BODY>

<h1> 회원 관리 시스템 </h1>

<a href='select.php'> (1) 회원 조회 (조회 후 수정/삭제 가능) </a> <br><br>
<a href='insert.php'> (2) 신규 회원 등록 </a> <br><br>
<FORM METHOD="get"  ACTION="update.php">
    (3) 회원 수정 - 회원 아이디 : <INPUT TYPE ="text" NAME="userID"> 
    <INPUT TYPE="submit"  VALUE="수정">
</FORM>
<FORM METHOD="get"  ACTION="delete.php">
    (4) 회원 삭제 - 회원 아이디 : <INPUT TYPE ="text" NAME="userID"> 
    <INPUT TYPE="submit"  VALUE="삭제">
</FORM>

</BODY>
</HTML>
```

### 회원 신규 화면

회원 입력 화면

```php
<HTML>
<HEAD>
<META http-equiv="content-type" content="text/html; charset=utf-8">
</HEAD>
<BODY>

<h1> 신규 회원 입력 </h1>
<FORM METHOD="post"  ACTION="insert_result.php">
    아이디 : <INPUT TYPE ="text" NAME="userID"> <br>
    이름 : <INPUT TYPE ="text" NAME="name"> <br> 
    출생년도 : <INPUT TYPE ="text" NAME="birthYear"> <br>
    지역 : <INPUT TYPE ="text" NAME="addr"> <br>
    휴대폰 국번 : <INPUT TYPE ="text" NAME="mobile1"> <br>
    휴대폰 전화번호 : <INPUT TYPE ="text" NAME="mobile2"> <br>
    신장 : <INPUT TYPE ="text" NAME="height"><br>
    <BR><BR>
    <INPUT TYPE="submit"  VALUE="회원 입력">
</FORM>

</BODY>
</HTML>
```

```php
<?php
    $con=mysqli_connect("localhost", "root", "1234", "sqlDB") or die("MySQL 접속 실패 !!");

    $userID = $_POST["userID"];
    $name = $_POST["name"];
    $birthYear = $_POST["birthYear"];
    $addr = $_POST["addr"];
    $mobile1 = $_POST["mobile1"];
    $mobile2 = $_POST["mobile2"];
    $height = $_POST["height"];
    $mDate = date("Y-m-j");
    
    $sql =" INSERT INTO userTbl VALUES('".$userID."','".$name."',".$birthYear;
    $sql = $sql.",'".$addr."','".$mobile1."','".$mobile2."',".$height.",'".$mDate."')";
    
    $ret = mysqli_query($con, $sql);

    echo "<h1> 신규 회원 입력 결과 </h1>";
    if($ret) {
        echo "데이터가 성공적으로 입력됨.";
    }
    else {
        echo "데이터 입력 실패!!!"."<br>";
        echo "실패 원인 :".mysqli_error($con);
    }
    mysqli_close($con);
    
    echo "<br> <a href='main.html'> <--초기 화면</a> ";
?>
```

### 회원 수정 화면

회원 수정 화면

```php
<?php
    $con=mysqli_connect("localhost", "root", "1234", "sqlDB") or die("MySQL 접속 실패 !!");
    $sql ="SELECT * FROM userTBL WHERE userID='".$_GET['userID']."'";

    $ret = mysqli_query($con, $sql);
        if($ret) {
        $count = mysqli_num_rows($ret);
        if ($count==0) {
        echo $_GET['userID']." 아이디의 회원이 없음!!!"."<br>";
        echo "<br> <a href='main.html'> <--초기 화면</a> ";
        exit();
        }
        }
    else {
        echo "데이터 조회 실패!!!"."<br>";
        echo "실패 원인 :".mysqli_error($con);
        echo "<br> <a href='main.html'> <--초기 화면</a> ";
        exit();
    }
        $row = mysqli_fetch_array($ret);
    $userID = $row['userID'];
    $name = $row["name"];
    $birthYear = $row["birthYear"];
    $addr = $row["addr"];
    $mobile1 = $row["mobile1"];
    $mobile2 = $row["mobile2"];
    $height = $row["height"];
    $mDate = $row["mDate"];
?>

<HTML>
<HEAD>
<META http-equiv="content-type" content="text/html; charset=utf-8">
</HEAD>
<BODY>

<h1> 회원 정보 수정 </h1>
<FORM METHOD="post"  ACTION="update_result.php">
    아이디 : <INPUT TYPE ="text" NAME="userID" VALUE=<?php echo $userID ?> READONLY> <br>
    이름 : <INPUT TYPE ="text" NAME="name" VALUE=<?php echo $name ?>> <br> 
    출생년도 : <INPUT TYPE ="text" NAME="birthYear" VALUE=<?php echo $birthYear ?>> <br>
    지역 : <INPUT TYPE ="text" NAME="addr" VALUE=<?php echo $addr ?>> <br>
    휴대폰 국번 : <INPUT TYPE ="text" NAME="mobile1" VALUE=<?php echo $mobile1 ?>> <br>
    휴대폰 전화번호 : <INPUT TYPE ="text" NAME="mobile2" VALUE=<?php echo $mobile2 ?>> <br>
    신장 : <INPUT TYPE ="text" NAME="height" VALUE=<?php echo $height ?>> <br>
    회원가입일 : <INPUT TYPE ="text" NAME="mDate" VALUE=<?php echo $mDate ?> READONLY><br>
    <BR><BR>
    <INPUT TYPE="submit"  VALUE="정보 수정">
</FORM>

</BODY>
</HTML>
```

수정 후 결과화면

```php
<?php
    $con=mysqli_connect("localhost", "root", "1234", "sqlDB") or die("MySQL 접속 실패 !!");

    $userID = $_POST["userID"];
    $name = $_POST["name"];
    $birthYear = $_POST["birthYear"];
    $addr = $_POST["addr"];
    $mobile1 = $_POST["mobile1"];
    $mobile2 = $_POST["mobile2"];
    $height = $_POST["height"];
    $mDate = $_POST["mDate"];

    $sql ="UPDATE userTbl SET name='".$name."', birthYear=".$birthYear;
    $sql = $sql.", addr='".$addr."', mobile1='".$mobile1."',mobile2='".$mobile2;
    $sql = $sql."', height=".$height.", mDate='".$mDate."' WHERE userID='".$userID."'";
    
    $ret = mysqli_query($con, $sql);

    echo "<h1> 회원 정보 수정 결과 </h1>";
    if($ret) {
        echo "데이터가 성공적으로 수정됨.";
    }
    else {
        echo "데이터 수정 실패!!!"."<br>";
        echo "실패 원인 :".mysqli_error($con);
    } 
    mysqli_close($con);

    echo "<br> <a href='main.html'> <--초기 화면</a> ";
?>
```

### 회원 삭제 화면

삭제 확인 화면

```php
<?php
    $con=mysqli_connect("localhost", "root", "1234", "sqlDB") or die("MySQL 접속 실패 !!");
    $sql ="SELECT * FROM userTBL WHERE userID='".$_GET['userID']."'";

    $ret = mysqli_query($con, $sql);
    if($ret) {
        $count = mysqli_num_rows($ret);
        if ($count==0) {
            echo $_GET['userID']." 아이디의 회원이 없음!!!"."<br>";
            echo "<br> <a href='main.html'> <--초기 화면</a> ";
            exit();    
        }
    }
    else {
        echo "데이터 조회 실패!!!"."<br>";
        echo "실패 원인 :".mysqli_error($con);
        echo "<br> <a href='main.html'> <--초기 화면</a> ";
        exit();
    }    
    $row = mysqli_fetch_array($ret);
    $userID = $row['userID'];
    $name = $row["name"];
?>

<HTML>
<HEAD>
<META http-equiv="content-type" content="text/html; charset=utf-8">
</HEAD>
<BODY>

<h1> 회원 삭제 </h1>
<FORM METHOD="post"  ACTION="delete_result.php">
    아이디 : <INPUT TYPE ="text" NAME="userID" VALUE=<?php echo $userID ?> READONLY> <br>
    이름 : <INPUT TYPE ="text" NAME="name" VALUE=<?php echo $name ?> READONLY> <br>
    <br><br>
    위 회원을 삭제하겠습니까?
    <INPUT TYPE="submit"  VALUE="회원 삭제">
</FORM>

</BODY>
</HTML>
```

삭제 후 후 결과화면

```php
<?php
    $con=mysqli_connect("localhost", "root", "1234", "sqlDB") or die("MySQL 접속 실패 !!");

    $userID = $_POST["userID"];

    $sql ="DELETE FROM userTbl WHERE userID='".$userID."'";
    
    $ret = mysqli_query($con, $sql);

    echo "<h1> 회원 삭제 결과 </h1>";
    if($ret) {
        echo $userID." 회원이 성공적으로 삭제됨..";
    }
    else {
        echo "데이터 삭제 실패!!!"."<br>";
        echo "실패 원인 :".mysqli_error($con);
    } 
    mysqli_close($con);
    
    echo "<br><br> <a href='main.html'> <--초기 화면</a> ";
?>
```
