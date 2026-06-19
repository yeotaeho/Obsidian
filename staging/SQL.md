# SQL — PostgreSQL 설치부터 쿼리·인덱스까지

데이터베이스를 직접 설치하고 데이터를 적재한 뒤, 쿼리와 인덱스까지 다뤄 본 과정을 정리했습니다.

## 1. 어떤 DB를 쓸 것인가

도커를 먼저 설치한 뒤, 후보로 다음 세 가지를 비교했습니다.

- **PostgreSQL**
  - 오픈소스 관계형 DB로, 파이썬(Psycopg2 등)과 자바(Spring Data JPA 등) 양쪽에서 잘 지원됩니다.
  - 구조화된 데이터, 트랜잭션, 복잡한 조인 등이 필요할 때 강점이 많습니다.
  - 백엔드가 파이썬이든 자바든 동일한 DB를 쓸 수 있어 협업에 유리합니다.
  - 추천 상황: 사용자/권한/게시판 등 관계형 데이터가 많고 스키마가 어느 정도 고정된 경우.
- **MySQL / MariaDB**
  - 익숙한 개발자가 많고 도구/호스팅 지원이 풍부합니다.
  - PostgreSQL보다 기능은 다소 적을 수 있지만, 팀 프로젝트 입문에는 충분히 안정적입니다.
- **MongoDB**
  - NoSQL(문서 지향) DB로, 스키마 유연성이 높고 JSON 형식 데이터를 다룰 때 유리합니다.
  - 데이터 구조가 자주 바뀌거나 빠른 프로토타이핑이 중요할 때 적합합니다.

이 중에서 저는 **PostgreSQL**을 선택했습니다.

## 2. PostgreSQL 설치 (Docker)

도커가 설치되어 있다는 전제 하에, 스프링 터미널에서 이미지를 받습니다.

```bash
docker pull postgres
```

컨테이너를 생성합니다.

```bash
docker run -d --name 컨테이너이름 \
  -e POSTGRES_USER=유저이름 \
  -e POSTGRES_PASSWORD=비번(8자이상) \
  -e POSTGRES_DB=DB이름 \
  -p 5432:5432 \
  -v ${HOME}\postgres_data:/var/lib/postgresql/data \
  postgres:15
```

이렇게 하면 컨테이너와 데이터베이스 설치가 완료됩니다. 여기서 지정한 `POSTGRES_PASSWORD`는 나중에 스프링이나 파이썬에서 접속할 때 필요하므로 꼭 기억해 둡니다.

## 3. 테이블 생성과 데이터 적재

컨테이너에 진입합니다.

```bash
docker exec -it 컨테이너이름 bash
```

<!-- TODO: 이미지 업로드 필요 -->
![[Pasted image 20251029091731.png]]

DB에 접속합니다.

```bash
psql -U 유저이름 -d DB이름
```

<!-- TODO: 이미지 업로드 필요 -->
![[Pasted image 20251029091758.png]]

테이블을 생성합니다. 테이블은 데이터 스키마에 맞춰 만들어야 합니다.

```sql
CREATE TABLE 테이블이름 (
  id INT,
  name TEXT,
  age INT
);
```

호스트에 있는 CSV 파일을 컨테이너 안으로 복사합니다. 경로를 정확히 지정해야 합니다.

```bash
docker cp ./src/main/resources/static/scv/train.csv cogirihub:/train.csv
```

CSV 데이터를 테이블에 적재합니다.

```sql
\copy 테이블명 FROM '/train.csv' DELIMITER ',' CSV HEADER;
```

결과를 확인합니다.

```sql
SELECT * FROM 테이블명;
```

`public` 스키마의 테이블을 모두 비우려면 다음을 실행합니다.

```sql
DO
$$
DECLARE
    r RECORD;
BEGIN
    FOR r IN (SELECT tablename FROM pg_tables WHERE schemaname = 'public') LOOP
        EXECUTE 'DROP TABLE IF EXISTS ' || quote_ident(r.tablename) || ' CASCADE';
    END LOOP;
END
$$
```

## 4. 쿼리문 — 자바에 빗대어 이해하기

쿼리문은 SQL에서 데이터를 수정·병합하는 등 데이터를 관리하는 문법입니다. 저는 자바 개념에 빗대어 익혔습니다.

- `SELECT column1, column2, ...` → 자바의 **`return`** 이라고 생각하면 쉽습니다.
- `FROM table_name` → 자바의 **`class`** 라고 생각하면 됩니다.
- `WHERE condition;` → 단순한 `if`라기보다, **"무엇을 한 것"이라는 기능적 조건**에 가깝습니다.

## 5. ERD와 RDBMS

<!-- TODO: 이미지 업로드 필요 -->
![[Pasted image 20251029110249.png]]

용어를 자바와 매핑하면 다음과 같이 이해했습니다.

- **튜플** = 자바에서의 인스턴스
- **인스턴스** = 자바에서의 리스트
- 인스턴스의 한 칸 = **스칼라**

```bash
taskkill /PID 9420 /F
taskkill /PID 20728 /F
```

<!-- TODO: 이미지 업로드 필요 -->
![[Pasted image 20251030091142.png]]

릴레이션끼리 연결된 것을 **ERD**라고 합니다. 데이터베이스에서 객체와 객체끼리 묶은 관계를 시각화한 다이어그램으로, 컨텍스트(맥락)에 따라 개체인지 구조체인지가 결정됩니다.

> **RDBMS = Relational DataBase Management System.**

RDBMS는 데이터를 **표(테이블)** 형태로 저장하고, 표들 사이의 **관계**를 이용해 효율적으로 관리하는 시스템입니다. 예를 들어 `고객(Customer)` 테이블과 `주문(Order)` 테이블이 따로 존재하더라도, 두 테이블을 **고객ID(CustomerID)** 로 연결할 수 있습니다.

## 6. 서브쿼리 — 속도 순서

### 스칼라 서브쿼리 (Scalar Subquery) — 가장 빠름

단일 값(하나의 행, 하나의 열)만 반환하는 서브쿼리로, 주로 `SELECT`, `WHERE`, `HAVING` 절에서 사용합니다.

```sql
SELECT name, (SELECT AVG(salary) FROM employees) AS avg_salary
FROM employees;
```

전체 평균 급여를 하나의 값으로 반환해 각 행과 비교할 수 있습니다.

### 인라인 뷰 (Inline View) — 두 번째로 빠름

`FROM` 절에 들어가 하위 쿼리를 임시 테이블처럼 사용하는 형태입니다.

```sql
SELECT dept_name, avg_sal
FROM (
    SELECT department AS dept_name, AVG(salary) AS avg_sal
    FROM employees
    GROUP BY department
) AS sub;
```

### 중첩 서브쿼리 (Nested Subquery) — 가장 느림

`WHERE` 또는 `HAVING` 절에 포함되어 특정 조건 비교에 사용됩니다.

```sql
SELECT name, salary
FROM employees
WHERE salary > (SELECT AVG(salary) FROM employees);
```

평균 급여보다 높은 직원만 출력합니다.

## 7. 옵티마이저와 인덱스

> **옵티마이저: SQL 쿼리를 가장 효율적으로 수행할 최적의 처리 경로를 생성하는 DBMS의 엔진.**

쿼리에서 **인덱스** 개념을 사용하면 값을 훨씬 빠르게 찾을 수 있습니다. 각 테이블의 키 값(PK)을 따로 모아 한 줄로 나열해 두고, 사용자가 특정 값을 지정하면 옵티마이저가 **이진 탐색(둘로 계속 쪼개기)** 으로 찾아냅니다.

인덱스를 확인하는 쿼리는 다음과 같습니다.

```sql
SELECT schemaname, tablename, indexname, indexdef
FROM pg_indexes
ORDER BY schemaname, tablename, indexname;
```

<!-- TODO: 이미지 업로드 필요 -->
![[Pasted image 20251031113226.png]]
인덱스로 연결한 키(PK)들입니다.

<!-- TODO: 이미지 업로드 필요 -->
![[Pasted image 20251031102626.png]]
