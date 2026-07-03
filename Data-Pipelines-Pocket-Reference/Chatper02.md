# Chapter 02: 최신 데이터 인프라(A Modern Data Infrastructure)

파이프라인 구축 전 최신 데이터 스택을 이해해야 함

## 데이터 소스의 다양성(Diversity of Data Sources)

대부분 조직은 수십개 이상의 데이터 소스가 있으며 이를 통해 분석 작업을 함

![modern data infrastructure](./assets/figure2-1.png)

최신 데이터 인프라의 핵심 구성 요소

- 데이터 수집 도구(Data ingestion tools)
- 클라우드 데이터 웨어하우스와 데이터 레이크(Cloud data warehouses and datalakes)
- 데이터 소스의 다양성(Diversity of data sources)
- 모델링 도구 및 프레임워크(Modeling tools and frameworks)
- 워크플로우 오케스트레이션 플랫폼(Workflow orchestration platforms)

### 소스 시스템 소유권(Source System Ownership)

분석 팀은 내부와 외부로부터 데이터를 수집함

![simple pipeline with data from multiple sources](./assets/figure2-2.png)

여러 소스(Postgres DB, REST API)의 데이터 -> S3 버킷 -> Redshift 로드

소스의 원천을 이해하는 것이 중요

- 타사 데이터 엑세스 접근 제한(API 접근 가능, SQL 접근 불가)
- 내부에 구축한 시스템은 더 많은 기회를 제공
  - 하지만 데이터 수집을 고려하여 설계했는지는 또 다른 문제
  - 수집은 시스템에 의도하지 않은 부하를 가할 수 있음
  - 데이터를 점진적으로 로드할 수 있는지 여부 등 다양한 과제 발생
- 리소스 제한은 외부 업체와 일하는 것과 크게 다르지 않음

### 수집 인터페이스 및 데이터 구조(Ingestion Interface and Data Structure)

데이터 엔지니어는 소스 데이터를 얻는 방법과 형식, 데이터에 대한 인터페이스가 무엇인지 알아야 함

인터페이스

- Postgres 또는 MysSQL 같은 앱 뒤에 있는 DB
- REST API와 같은 시스템 상단의 추상화 계층
- Apache Kafka와 같은 스트림 처리 플랫폼
- CSV 파일이나 플랫한 파일을 포함한 공유 네트워크 파일 시스템(NFS) or 클라우드 스토리지 버킷(S3)
- 데이터 웨어하우스 또는 데이터 레이크
- HDFS 또는 HBase 데이터베이스의 데이터

데이터 구조

- **JSON** from REST API
- **JSON** within columns from MySQL Table
- **Well-structured data** from from MySQL Table
- **Semistructured log data**
- **CSV**, **FWF(fixed-width format)**, and **other flat file** formats
- **JSON** in flat files
- **Stream output** from Kafka

데이터 구조화

1. 정형 데이터

   - 일반적인 앱, 웹 데이터
   - 분석 프로젝트를 위해 수집, 클렌징, 변환 작업 파이프라인 필요

2. 반정형 데이터

   - 키-벨류 형태(JSON), 점점 보편화
   - 데이터 유연성이 높음, 정제 필요

3. 비정형 데이터

   - 이미지, 비디오, 텍스트, HTML안 텍스트
   - CV(Computer Vision), NLP(Natural Language Processing)등 분석 작업에 흔히 사용

### 데이터 사이즈(Data Volume)

- 데이터를 설계할 때 데이터 사이즈를 고려
- 수집할 때 작은 데이터, 큰 데이터 모두 수집
- 대용량, 소용량에 생각하기 보단 스펙트럼(범위) 측면에서 생각

### 데이터 클렌징 작업 및 유효성 검사(Data Cleanliness and Validity)

데이터의 품질도 매우 다양, 데이터의 한계와 결함을 이해하고 파이프라인에서 해결

지저분한 데이터의 특징

- 중복(Duplicate), 모호한(Ambiguous), 고립된(Orphaned), 불안전(Incomplete), 누락(Missing) 레코드
- 텍스트 인코딩 에러(Text encoding errors)
- 일정하지 않은 포맷(Inconsistent formats)
  - 예 010-xxxx-xxxx, 010xxxxxxxx
- 잘못된 분류(Mislabeled) 데이터
- 라벨링안된(Unlabeled) 데이터

이 책에서 다룰 접근 방식

- 최악을 예상하고 최고를 기대(Assume the worst, expect the best)
- 가장 적절한 시스템에서 데이터를 정제하고 검증(Clean and validate data in the system best suited to do so)
  - ELT가 더 morden한 파이프라인 rather than ETL
  - 떄로는 원본 그대로 Data lake에 적재 후 파이프라인에서 정제
- 자주 검증(Validate often)
  - 초기의 유효성 검증은 나중에도 더 해야함

### 소스 시스템의 지연 시간 및 대역폭(Latency and Bandwidth of the Source System)

- 대량의 데이터를 빈번하게 추출하는 일이 많음
- 파이프라인에서 데이터 추출 단계에서 API 속도 제한, 연결 시간 초과, 느린 다운로드 등은 unhappy

## 클라우드 데이터 웨어하우스 및 데이터 레이크(Cloud Data Warehouses and Data Lakes)

3가지 요소(데이터 웨어하우징과 분석의 배경 뒤에 퍼블릭 클라우드가 있음)

1. 데이터 파이프라인, 데이터 레이크, 웨어하우스, 분석 작업의 빌드와 배포가 쉬움
2. 지속적인 클라우드 비용 감소
3. Amazon RedShift, Snowflake, Google Big Query 같은 확장성 뛰어난 columns

## 데이터 수집 도구(Data Ingestion Tools)

최신 데이터 인프라에서 많은 상용 및 오픈 소스 도구 사용

- Singer: ETL 프레임워크, 데이터 추출 및 로드를 위한 표준 제공, 탭, 타겟 구성
- Stitch: ETL 서비스, 다양한 커넥터, 쉬운 인터페이스, 데이터 통합 작업 간소화
- Fivetran: 완전 관리형 데이터 통합 서비스, 데이터 추출 후 데이터 웨어하우스에 로드

이런 툴이 많아도 자체 프레임워크 개발을 하는 이유

- 비용, 직접 구축 선호, 법적 보안적 위험

## 데이터 변환 및 모델링 도구(Data Tramsformation and Modeling Tools)

- 데이터 모델링
- 데이터 변환

데이터 변환 기능(개인 식별 가능 정보(PII)), 수집 프로세스에 적용

분석 및 보고(GUI 방식 vs SQL 방식)

## 워크플로 오케스트레이션 플랫폼(Workflow Orchestration Platforms)

워크플로 오케스트레이션 플랫폼, 워크플로 관리 시스템(WMS), 오케스트레이션 프레임워크, 오케스트레이션 플랫폼

워크플로 오케스트레이션 플랫폼 도입 이유

- 파이프라인의 복잡성과 수 증가
- 스케줄링 및 종속성 해결
- 일반적 플랫폼: Apache Airflow, Luigi, AWS Glue
- 특별한 플랫폼: Kubeflow Pieline, 도커 컨테이너에 구성된 머신러닝 워크플로

### 방향성 비순환 그래프(Directed Acyclic Graphs)

파이프라인 단계 특징

- 방향성(directed), 순서 보장 및 종속적
- 비순환(acyclic), 작업을 돌릴 수 없음

2가지 제약 조건 때문에 **방향성 비순환 그래프(DaGs, Directed Acyclic Graphs)** 로 파이프라인 생성

![simple_pipeline](./assets/figure2-3.png)

DAG(Directed Acyclic Graph) 그래프 예시

![simple_pipeline](./assets/figure2-4.png)

1. 관계형 DB에서 데이터 쿼리 후 CSV 파일 저장하는 SQL 스크립트 실행
2. CSV 로드 및 정리, 데이터 형 변경하고 싶은 CSV로 데이터 저장
3. CSV를 Snowflake 데이터 웨어하우스로 로드 SQL COPY 명령어 실행

## 데이터 인프라 커스터마이징(Customizing Your Data Infrastructure)

데이터 인프라가 정확히 일치하는 두 조직을 찾기 힘듬, 커스터마이징 필요

제약 조건(비용, 엔지니어링 리소스, 보안 및 법적 리스크 허용 범위), 그에 따른 트레이드오프 이해
