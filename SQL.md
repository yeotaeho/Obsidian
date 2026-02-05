SQL
데이터베이스 설치 


개체 구조체 


도커 설치후
- **PostgreSQL**
    
    - 오픈소스 관계형 DB로, 파이썬(Psycopg2 등)과 자바(Spring Data JPA 등) 모두에서 잘 지원됩니다. [Ropstam Solutions Inc.+2Medium+2](https://www.ropstam.com/best-databases-for-python/?utm_source=chatgpt.com)
        
    - 구조화된 데이터를 사용하고, 트랜잭션이나 복잡한 조인 등이 필요할 경우 강점이 많습니다.
        
    - 프로젝트에서 백엔드가 파이썬이든 자바(Spring)이든 동일한 DB를 쓸 수 있어 협업에 유리합니다.
        
    - 추천 상황: 사용자/권한/게시판/관계형 데이터가 많고 스키마가 어느 정도 고정되어 있는 경우.
        
- **MySQL / MariaDB**
    
    - 역시 관계형 DB이며, 많은 개발자에게 익숙하고 도구/호스팅 지원이 많습니다. [Ropstam Solutions Inc.+1](https://www.ropstam.com/best-databases-for-python/?utm_source=chatgpt.com)
        
    - PostgreSQL보다 조금 덜 기능이 많을 수 있지만 팀 프로젝트 입문에는 충분히 안정적입니다.
        
    - 추천 상황: 관계형 DB가 맞는 케이스이고, 팀원이 MySQL 경험이 많다면 좋은 선택.
        
- **MongoDB**
    
    - NoSQL(문서 지향) DB로, 스키마 유연성이 높고 자바스크립트/JSON 형식 데이터를 많이 다루는 경우 유리합니다. [Ropstam Solutions Inc.+1](https://www.ropstam.com/best-databases-for-python/?utm_source=chatgpt.com)
        
    - 파이썬, 자바 모두에서 드라이버/라이브러리 지원이 잘 되어 있습니다.
        
    - 추천 상황: 데이터 구조가 자주 바뀌거나, 관계보다는 문서 중심의 저장이 많거나, 빠른 프로토타이핑이 중요할 때.


등등 데이터베이스을 설치할수있는데 여기중에서 PostgreSQL을 설치할거임
도커 설치되있다는 가정하에 **스프링 터미널**에 `docker pull postgres`입력 데이터베이스 설치
postgreSQL컨데이너 만들때 
`docker run -d --name 컨테이너이름 -e POSTGRES_USER=유저이름 -e POSTGRES_PASSWORD=비번(8자이상입력) -e POSTGRES_DB=DB이름 -p 5432:5432 -v ${HOME}\postgres_data:/var/lib/postgresql/data postgres:15`입력
이렇게 하면 도커에 컨테이너와 데이터베이스 설치완료

여기서 POSTGRES_PASSWORD=12341234 이부분이 컨데이터 비번 나중에 스프링이나 파이썬에서 쓸때 필요함

**데이터베이스에 테이블설치**

`docker exec -it 컨테이너이름 bash`
컨테이너에 들어가는 명령어
![[Pasted image 20251029091731.png]]
	
`psql -U 유저이름 -d DB이 이름`
DB에 들어가는 명령어
![[Pasted image 20251029091758.png]]


CREATE TABLE 테이블 이름 (
  id INT,
  name TEXT,
  age INT
);
이게 테이블 생성 명령어인데 테이블은 데이터 스키마 에 맞춰서 만들어야함

`docker cp ./src/main/resources/static/scv/train.csv cogirihub:/train.csv`
호스트(스프링?)에 있는 train.csv파일을 컨테이너안에 카피
경로설정 잘하셈


`\copy 테이블명 FROM '/train.csv' DELIMITER ',' CSV HEADER;`

train.csv데이터값을 테이블에 넣어라

`select * from 테이블명;`
결과확인

DO
`$$
DECLARE
    r RECORD;
BEGIN
    FOR r IN (SELECT tablename FROM pg_tables WHERE schemaname = 'public') LOOP
        EXECUTE 'DROP TABLE IF EXISTS ' || quote_ident(r.tablename) || ' CASCADE';
    END LOOP;
END
`$$

데이타 다 지우는거

쿼리문

쿼리문은 sql에서 쓰는 문법으로 데이터을 수정하거나 병합하거나 쉽게말해서 데이터 관리을
할수있다


SELECT _column1_, _column2, ..._  는 자바에서 retrun이라 생각하면 쉽고
FROM _table_name_     는 자바에서 class라 생각하면 쉽다
WHERE _condition_;    는 조건문 if라 생각하지말고 기능 {}여기 안에 있는 기능적인 부분이 더 가깝다 뭐뭐 면이 아니라 뭐뭐 한걸 이다
이게 쿼리문에 기본형

join과서브쿼리


![[Pasted image 20251029110249.png]]
튜플 = 자바에서의인스턴스
인스턴스 = 자바에서의 리스트
인스턴스에 한칸이 **스칼라**
select뒤에서브쿼리을 스칼라?
인라인뷰
taskkill /PID 9420 /F
taskkill /PID 20728 /F
taskkill /PID 000 /F

 ![[Pasted image 20251030091142.png]]
릴레이션끼리 연결된걸 ERD 
데이터베이스에서 객체와 객체끼리 묶은걸 시각화 한 다이어그램 여기서 
컨텍스트(맥락)에 따라 개체인지 구조체인지 결정 차원이동 

**RDBMS**는 **Relational DataBase Management System**(서브쿼리)
RDBMS는 데이터를 **표(테이블, Table)** 형태로 저장하고,  
이 표들 사이의 **관계(ship)** 를 이용해 데이터를 효율적으로 관리하는 시스템이에요.
예를 들어,

- `고객(Customer)` 테이블
    
- `주문(Order)` 테이블  
    이 따로 존재하지만,  
    두 테이블을 **고객ID(CustomerID)** 로 연결(관계)할 수 있습니다.


서브쿼리
**스칼라 서브쿼리 (Scalar Subquery)** 이 방법이 가장 빠름

- 단일 값(하나의 행, 하나의 열)만 반환하는 서브쿼리입니다.
    
- 주로 SELECT, WHERE, HAVING 절 등에서 사용됩니다.
    

SELECT name, (SELECT AVG(salary) FROM employees) AS avg_salary FROM employees;

→ 전체 평균 급여(AVG(salary))를 하나의 값으로 반환하여 각 행과 비교 가능.

**인라인 뷰 (Inline View)** 두 번째로 빠름

- FROM 절에 들어가 하위 쿼리를 임시 테이블처럼 사용하는 형태입니다.
    

SELECT dept_name, avg_sal FROM ( SELECT department AS dept_name, AVG(salary) AS avg_sal FROM employees GROUP BY department ) AS sub;

**중첩 서브쿼리 (Nested Subquery)** ㅈㄴ 느림

- WHERE 또는 HAVING 절에 포함되어 특정 조건을 위한 비교에 사용됩니다.
    

SELECT name, salary FROM employees WHERE salary > ( SELECT AVG(salary) FROM employees );

→ 평균 급여보다 높은 직원만 출력.


옵티마이저:SQL 쿼리를 가장 효율적으로 수행할 수 있는 최적의 처리 경로를 생성하는 DBMS의 엔진


쿼리에서 인덱스라는 개념으로 훨씬 빠르게 값을 찾아낼수 있다 
각 테이블에 키값(PK)을  가져와서 따로 저장한다 저장한걸 한줄로 나열해서 
사용자가 어느 한 값을 지정 하면 옵티마이저가 이진법(둘로 계속쪼개)으로 찾아낸다 

SELECT schemaname, tablename, indexname, indexdef
FROM pg_indexes
ORDER BY schemaname, tablename, indexname;
인덱스 확인코드

![[Pasted image 20251031113226.png]]
인덱스 연결한 키(PK)들


![[Pasted image 20251031102626.png]]


