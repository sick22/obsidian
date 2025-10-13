# SQL 학습 노트 (문법 기준)

이 문서는 여러 SQL 파일에 흩어져 있는 코드와 주석을 SQL 문법 기준으로 재정리한 학습 자료입니다.

---

## 1. DDL (Data Definition Language) - 데이터 정의어

데이터베이스, 테이블, 인덱스 등 데이터 구조를 정의, 변경, 삭제하는 명령어입니다.

### 1.1. `CREATE DATABASE`

새로운 데이터베이스를 생성합니다.

**코드 예시:**
```sql
CREATE DATABASE shop_db;
CREATE DATABASE market_db;
```

**관련 주석:**
- `USE [데이터베이스명]`: 사용할 데이터베이스를 지정하거나 변경할 때 사용합니다. 모든 테이블명 앞에는 사실 `데이터베이스명.`이 생략되어 있으며, `USE`를 통해 현재 작업할 데이터베이스를 지정하면 자동으로 적용됩니다.

### 1.2. `CREATE TABLE`

새로운 테이블을 생성합니다.

**코드 예시:**
```sql
-- 기본 테이블 생성
CREATE TABLE hon(
	toy_id int,
	toy_name char(4),
    age int
);

-- 제약조건 포함 테이블 생성
CREATE TABLE product(
	product_name char(4) not null,
    cost int not null,
    make_date date ,
    company char(5),
    amount int not null,
    primary key(product_name)
);

-- PRIMARY KEY, FOREIGN KEY, AUTO_INCREMENT 등 다양한 제약조건 활용
CREATE TABLE member (
  mem_id  		CHAR(8) NOT NULL PRIMARY KEY, 
  mem_name    	VARCHAR(10) NOT NULL, -- 멤버명
  mem_number    INT NOT NULL,  -- 멤버 수
  addr	  		CHAR(2) NOT NULL, -- 주소
  phone1		CHAR(3), -- 국번
  phone2		CHAR(8), -- 나머지 폰번호
  height    	SMALLINT,  -- 평균 키
  debut_date	DATE  -- 데뷔일
);

CREATE TABLE buy (
   num 		INT AUTO_INCREMENT NOT NULL PRIMARY KEY, -- 구매번호
   mem_id  	CHAR(8) NOT NULL, -- 외래키
   prod_name 	CHAR(6) NOT NULL, -- 제품명
   group_name 	CHAR(4), -- 제품분류
   price     	INT  NOT NULL, -- 가격
   amount    	SMALLINT  NOT NULL, -- 수량
   FOREIGN KEY (mem_id) REFERENCES member(mem_id)
);
```

**관련 주석:**
- **데이터 타입:** `char(8)`에서 숫자는 최대 글자 수를 의미합니다.
- **PRIMARY KEY (기본키):**
    - 중복이 없고, `NULL` 값을 가질 수 없는 중요한 컬럼에 설정합니다. (예: 아이디, 주민번호, 학번)
    - PK 자체에 `NOT NULL` 속성이 포함되어 있어 `NOT NULL`을 생략할 수 있지만, 하위 버전 호환성을 위해 명시적으로 적어주는 것이 좋습니다.
    - PK 설정은 컬럼 옆에 바로 하거나, 테이블 정의 마지막에 `PRIMARY KEY(컬럼명)` 형태로 할 수 있습니다.
- **FOREIGN KEY (외래키):**
    - 한 테이블의 컬럼이 다른 테이블의 기본키를 참조하도록 설정합니다.
    - `FOREIGN KEY (자식테이블_컬럼) REFERENCES 부모테이블(부모테이블_PK)` 형식으로 사용합니다.
- **기타:**
    - 테이블 생성 시 마지막 컬럼 뒤에는 쉼표(`,`)를 붙이지 않습니다.

### 1.3. `DROP`

데이터베이스나 테이블, 인덱스를 삭제합니다.

**코드 예시:**
```sql
-- 데이터베이스가 존재할 경우에만 삭제
DROP DATABASE IF EXISTS market_db;

-- 테이블 삭제
drop table product;

-- 인덱스 삭제
drop index idx_member_name on member;
```

### 1.4. `CREATE INDEX`

검색 속도를 향상시키기 위해 인덱스를 생성합니다.

**코드 예시:**
```sql
create index idx_member_name on member(member_name);
```

---

## 2. DML (Data Manipulation Language) - 데이터 조작어

테이블의 데이터를 조회, 추가, 수정, 삭제하는 명령어입니다.

### 2.1. `INSERT INTO`

테이블에 새로운 데이터를 추가합니다.

**코드 예시:**
```sql
-- 모든 컬럼에 값 추가
insert into hon values(123, "toy",16);
insert into member values('jinu','진우', '저세상');

-- 특정 컬럼에만 값 추가 (나머지는 NULL)
insert into hon(toy_name) values('버즈');

-- AUTO_INCREMENT 컬럼에 NULL 전달 시 자동 증가
INSERT INTO buy VALUES(NULL, 'BLK', '지갑', NULL, 30, 2);
```

**관련 주석:**
- `INSERT INTO 테이블명(컬럼1, 컬럼2) VALUES(값1, 값2)` 형식으로 특정 컬럼에만 값을 넣을 수 있습니다. 이 경우, 값을 넣지 않은 컬럼은 `NULL`이 됩니다.
- 컬럼 순서를 바꿔서 입력할 수도 있습니다. `hon(toy_name, toy_id, age)`
- `AUTO_INCREMENT`가 설정된 PK 컬럼에 `NULL`을 입력하면 자동으로 값이 1씩 증가하여 들어갑니다.
- `DATE` 타입 컬럼에 유효하지 않은 날짜를 입력하면 에러가 발생합니다.

### 2.2. `UPDATE`

테이블의 기존 데이터를 수정합니다. `WHERE` 절을 사용하지 않으면 모든 행이 수정되므로 주의해야 합니다.

**코드 예시:**
```sql
-- 특정 조건의 데이터 수정
update member
set member_addr = '제주도 애월읍'
where member_id='카리나';

-- 기존 값을 활용하여 수정
update product
set amount = amount * 2
where amount >= 10;
```

**관련 주석:**
- `UPDATE 테이블명 SET 바꿀컬럼=바꿀값 WHERE 조건` 형식으로 사용합니다.
- `SET`은 어떻게 바꿀지, `WHERE`는 누구를 바꿀지 지정합니다.

### 2.3. `DELETE`

테이블에서 데이터를 삭제합니다. `WHERE` 절을 사용하지 않으면 모든 행이 삭제되므로 주의해야 합니다.

**코드 예시:**
```sql
delete from product where product_name='노트북';
```

---

## 3. DQL (Data Query Language) - 데이터 질의어

테이블에서 원하는 데이터를 조회합니다.

### 3.1. `SELECT`

**기본 조회:**
```sql
-- 모든 컬럼 조회
select * from member;

-- 특정 컬럼만 조회
select mem_name from member;

-- 다른 데이터베이스의 테이블 조회
select * from shop_db.member limit 2;
```

**조건 및 정렬:**
```sql
-- WHERE: 특정 조건에 맞는 데이터만 조회
select * from member where member_addr='보스턴';

-- ORDER BY: 특정 컬럼 기준으로 정렬 (DESC: 내림차순, ASC: 오름차순(기본값))
select mem_name, height from member order by height desc;

-- LIMIT: 조회할 행의 수를 제한
select mem_id, mem_name from member limit 0, 2; -- 0번째부터 2개

-- DISTINCT: 중복된 값을 제거하고 조회
select distinct addr from member order by addr;
```

**집계 함수 및 그룹화:**
```sql
-- COUNT: 행의 개수 세기
select count(*) 회원수 from member; -- NULL 포함 모든 행
select count(phone1) from member; -- 특정 컬럼에서 NULL 제외한 행

-- SUM: 합계
select mem_id, SUM(amount) from buy group by mem_id;

-- AVG: 평균
select mem_id, avg(amount) " 평균 구매 개수" from buy group by mem_id;

-- GROUP BY: 특정 컬럼을 기준으로 그룹화하여 집계
select mem_id "회원 아이디", SUM(amount) "구매 개수", sum(price * amount) 구매금액 
from buy 
GROUP BY mem_id;

-- HAVING: GROUP BY로 그룹화된 결과에 대한 조건절
select mem_id 회원아이디, sum(price*amount) 구매금액
from buy
group by mem_id
having 구매금액 > 100;
```

**서브쿼리 (Subquery):**
```sql
-- SELECT 문 안에 또 다른 SELECT 문을 사용하여 조건으로 활용
select name from city 
where countrycode = (select code from country where name='italy');
```

**관련 주석:**
- `*`는 모든 컬럼을 의미합니다.
- `AS`를 사용하거나 한 칸 띄고 별칭(Alias)을 주어 컬럼명을 바꿔서 조회할 수 있습니다. (예: `select count(*) AS 회원수`)
- 별칭에 공백이 포함되면 따옴표(`"`)로 묶어줍니다.
- `SELECT` 문의 실행 순서: `FROM` -> `WHERE` -> `GROUP BY` -> `HAVING` -> `SELECT` -> `ORDER BY` -> `LIMIT`
- 데이터를 입력한 순서와 상관없이 `SELECT`하면 보통 PK 순서로 보입니다. (물리적으로 PK 순서로 저장되기 때문)

---

## 4. TCL (Transaction Control Language) - 트랜잭션 제어어

데이터의 변경사항을 하나의 작업 단위(트랜잭션)로 묶어 제어하는 명령어입니다.

**코드 예시:**
```sql
-- autocommit 상태 확인 (1: 활성, 0: 비활성)
select @@autocommit;

-- autocommit 비활성화
set autocommit = 0;

-- 트랜잭션 시작
start transaction;

-- 데이터 변경 작업
select * from member;
update member set member_addr="부산 동아대학교 기숙사" where member_id='카리나';

-- 변경사항 취소 (트랜잭션 시작 이전으로 복구)
rollback;

-- 변경사항 최종 저장
commit;
```

---

## 5. 기타 유용한 명령어

### `DESC`

테이블의 구조(컬럼명, 데이터타입, 제약조건 등)를 확인합니다.

**코드 예시:**
```sql
desc member;
desc product;
```

### `SHOW TABLES`

현재 데이터베이스에 있는 테이블 목록을 보여줍니다.

**코드 예시:**
```sql
show tables;
```

### 주석(Comments) 작성법

- 한 줄 주석: `--` (뒤에 한 칸 띄어쓰기, 표준), `#` (MySQL)
- 여러 줄 주석: `/* 주석 내용 */`
