# Chapter 04 데이터베이스 모델링

## 04-01 데이터베이스 모델링 개념

정보처리기사 문제에 나오는 것들임

프로젝트는 계획, 설계, 구현, 테스트, 배포 이런식

모델링은 크게 3단계(개념적, 논리적, 물리적)

## 04-02 데이터베이스 모델링 툴 실습

### 다이어그램 그린 후 Export

1. `File` -> `New Model` -> `mydb` -> `Edit Schema` -> `ModelDB` 로 이름 변경
2. `Add Diagram` -> 다이어그램을 그린 후 -> `DataBase` -> `Forward Engineer` 후 별다른거 없이 확인
3. 아래와 같이 다이어그램 생성이 됨

    ```sql
    -- MySQL Workbench Forward Engineering

    SET @OLD_UNIQUE_CHECKS=@@UNIQUE_CHECKS, UNIQUE_CHECKS=0;
    SET @OLD_FOREIGN_KEY_CHECKS=@@FOREIGN_KEY_CHECKS, FOREIGN_KEY_CHECKS=0;
    SET @OLD_SQL_MODE=@@SQL_MODE, SQL_MODE='ONLY_FULL_GROUP_BY,STRICT_TRANS_TABLES,NO_ZERO_IN_DATE,NO_ZERO_DATE,ERROR_FOR_DIVISION_BY_ZERO,NO_ENGINE_SUBSTITUTION';

    -- -----------------------------------------------------
    -- Schema ModelDB
    -- -----------------------------------------------------

    -- -----------------------------------------------------
    -- Schema ModelDB
    -- -----------------------------------------------------
    CREATE SCHEMA IF NOT EXISTS `ModelDB` DEFAULT CHARACTER SET utf8 ;
    USE `ModelDB` ;

    -- -----------------------------------------------------
    -- Table `ModelDB`.`userTBL`
    -- -----------------------------------------------------
    CREATE TABLE IF NOT EXISTS `ModelDB`.`userTBL` (
    `userName` CHAR(3) NOT NULL,
    `birthYear` INT NOT NULL,
    `addr` CHAR(2) NOT NULL,
    `mobile` CHAR(12) NULL,
    PRIMARY KEY (`userName`))
    ENGINE = InnoDB;


    -- -----------------------------------------------------
    -- Table `ModelDB`.`buyTBL`
    -- -----------------------------------------------------
    CREATE TABLE IF NOT EXISTS `ModelDB`.`buyTBL` (
    `userName` CHAR(3) NOT NULL,
    `proName` CHAR(3) NULL,
    `price` INT NULL,
    `amount` INT NULL,
    PRIMARY KEY (`userName`),
    CONSTRAINT `fk_buyTBL_userTBL`
        FOREIGN KEY (`userName`)
        REFERENCES `ModelDB`.`userTBL` (`userName`)
        ON DELETE NO ACTION
        ON UPDATE NO ACTION)
    ENGINE = InnoDB;


    SET SQL_MODE=@OLD_SQL_MODE;
    SET FOREIGN_KEY_CHECKS=@OLD_FOREIGN_KEY_CHECKS;
    SET UNIQUE_CHECKS=@OLD_UNIQUE_CHECKS;
    ```

### DB 다이어그램 파일로 Import

1. `Reverse Engineer` -> 별다른거 없이 확인
2. 생성된거 확인 후 저장
