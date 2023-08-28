# sqld 기본 및 활용

## 기타 
### 0 나누기

- A / 0 → 0
- 0 / A → ERROR
- 0 / NULL, NULL / 0, A / NULL, NULL / A → NULL

<br/>

### null

- 모르는 값을 의미한다.
- 값의 부재를 의미한다.
- `NULL`과의 모든 비교는 `Unknown`을 반환한다.
- `NULL`과의 모든 연산은 `NULL`을 반환한다.

<br/>

### ORACLE과 SQL

- orcle은 ‘’로 입력하면 null로 인식 / sql은 ‘’로 인식
- Oracle에서는 `NULL`을 가장 큰 값으로 간주한다.
- SQL에서는 `NULL`을 가장 작은 값으로 간주한다.

<br/>

### SQL 문장의 종류

**`FROM` - `WHERE` - `GROUP BY` - `HAVING` - `SELECT` - `ORDER BY`**

- **(비절차적) 데이터 조작어 (DML) →** SELECT, INSERT, UPDATE, DELETE
- **데이터 정의어 (DDL) →** CREATE, ALTER, DROP, RENAME
- **데이터 제어어 (DCL) →** GRANT, REVOKE
- **트랜잭션 제어어 (TCL) →** COMMIT, ROLLBACK

<br/>

### 테이블명 설정

- 영문자로 시작해야함
- 중복되면 안됨
- 예약서 사용불가
- A-Z, a-z, 0-9, _ ,#, $ 의 문자만 허용한다.
- 끝은 항상 ;로 끝남

abc_123 (0) /  _abc (x)   /  123abc (x)

<br/>

## CREATE

```tsx
CREATE TABLE member(
		id VARCHAR2(20),
    password VARCHAR2(20),
    name VARCHAR2(20)
)
```

<br/>

### NOT NULL

```tsx
CREATE TABLE member(
		id VARCHAR2(20) NOT NULL,
    password VARCHAR2(20) NOT NULL,
    name VARCHAR2(20)
)
```

<br/>

### DEFAULT

```tsx
CREATE TABLE member(
		id VARCHAR2(20) NOT NULL,
    password VARCHAR2(20) DEFAULT '1234',
    name VARCHAR2(20) DEFAULT 'NICKNAME' NOT NULL
)
```

**`DEFAULT`** 옵션은 새로운 레코드가 테이블에 삽입될 때 해당 컬럼에 값을 제공하지 않을 경우 사용되는 기본 값을 설정합니다.

<br/>

### UNIQUE

```tsx
CREATE TABLE member(
		id VARCHAR2(20) UNIQUE,
    password VARCHAR2(20) NOT NULL,
    name VARCHAR2(20)
)

CREATE TABLE member(
		id VARCHAR2(20),
    password VARCHAR2(20) NOT NULL,
    name VARCHAR2(20),
		UNIQUE(id)
)
```

**`UNIQUE`** 옵션은 테이블의 컬럼에 중복된 값을 허용하지 않도록 설정합니다. 즉, 해당 컬럼의 모든 값은 유일해야 합니다.

<br/>

### CHECK

```tsx
CREATE TABLE member(
		id VARCHAR2(20) UNIQUE,
    password VARCHAR2(20) NOT NULL,
    name VARCHAR2(20)
		age NUMBER CHECK(AGE ≥ 18)  
		gender VARCHAR2(10) CHECK(gender IN ('남', '여'))
)
```

**`CHECK`** 옵션은 특정 조건이 만족되어야만 테이블의 데이터가 삽입되거나 업데이트될 수 있도록 설정합니다. 즉, 지정한 조건을 만족하지 않는 데이터는 테이블에 추가되지 않습니다.

<br/>

### PRIMARY

```tsx
// 데이터 타입과 같이 설정하기
CREATE TABLE member(
		id VARCHAR2(20) PRIMARY KEY,
    password VARCHAR2(20),
    name VARCHAR2(20)
)

// 데이터 타입과 따로 설정하기
CREATE TABLE member(
		id VARCHAR2(20),
    password VARCHAR2(20),
    name VARCHAR2(20)
		PRIMARY KEY(id)
)
```

<br/>

**PRIMARY KEY 설정**

```tsx
CREATE TABLE member(
		id VARCHAR2(20) CONSTRAINT pk_id PRIMARY KEY,
    password VARCHAR2(20),
    name VARCHAR2(20)
)
```

PRIMARY KEY를 지정하며 제약 조건에 이름 달기

제약 조건을 추가할때는 `CONSTRAINT` 로 이름을 지정해주는 것이 일반적으로 좋은 관행이다.

<br/>

```tsx
CREATE TABLE orders (
	order_id INT,
	product_id INT,
	quantity INT,
	PRIMARY KEY (order_id, product_id)
);
```

여러 컬럼을 조합하여 복합 기본 키(Composite Primary Key) 설정하기

<br/>

### FOREIGN KEY

```tsx
CREATE TABLE member(
		id VARCHAR2(20) PRIMARY KEY,
    password VARCHAR2(20),
    name VARCHAR2(20)
)

// 데이터 타입과 같이 설정하기
CREATE TABLE member_score(
	  id VARCHAR2(20) REFERENCES member(id),
		score NUMBER NOT NULL,
)

// 데이터 타입과 따로 설정하기
CREATE TABLE member_score(
	  id VARCHAR2(20) REFERENCES member(id),
		score NUMBER NOT NULL,
		FOREGIN KEY (id) REFERENCES member(id)
)
```

member_score에서 member에 없는 id를 삽입하려 한다면 `DEPENDENT` 오류 발생한다.

외래 키 값은 참조 무결성 제약을 받을 수 있다.

<br/>

### 외래키 삭제 옵션

```tsx
CREATE TABLE member(
		id VARCHAR2(20) PRIMARY KEY,
    password VARCHAR2(20),
    name VARCHAR2(20)
)

CREATE TABLE member_score(
	  id VARCHAR2(20) PRIMARY KEY,
		score NUMBER NOT NULL,
		// 부모 값 삭제시 참조하는 자식이 있다면 삭제 불가 (기본 옵션)
		FOREIGN KEY(id) REFERENCES member(id) ON DELETE RESTRICT
		// 부모 키를 삭제 시 자식 키를 가진 행도 함께 삭제
		FOREIGN KEY(id) REFERENCES member(id) ON DELETE CASCADE
		// 부모 키를 삭제 시 자식 키를 NULL로 변경한다 (다른 행은 그대로).
		FOREIGN KEY(id) REFERENCES member(id) ON DELETE SET NULL
)
```

<br/>

### 칼럼에 주석 달기

```tsx
CREATE TABLE member(
		id VARCHAR2(20),
    password VARCHAR2(20),
    name VARCHAR2(20)
)

COMMENT ON COLUMN member.id IS '멤버 아이디'
COMMENT ON COLUMN member.password IS '멤버 비밀번호'
COMMENT ON COLUMN member.name IS '멤버 닉네임'
```

<br/>

### Indexing

```tsx
CREATE TABLE tableName (
	columnName dataType(SIZE) NOT NULL,
	columnName dataType(SIZE) DEFAULT,
)

CREATE INDEX indexName ON tableName (columnName)
```

<br/>

## ALTER

```tsx
// 칼럼 이름 변경
ALTER TABLE tableName RENAME COLUMN oldColumnName TO newColumnName;

// 칼럼 추가
ALTER TABLE tableName ADD columnName dataType(SIZE) 

// 사이즈 변경, 데이터 타입 변경
ALTER TABLE tableName MODIFY columnName dataType(SIZE)

// NOT NULL 옵션 추가
ALTER TABLE tableName MODIFY columnName VARCHAR(100) NOT NULL;

// 컬럼 삭제
ALTER TABLE tableName DROP COLUMN columnName
```

`ALTER` 명령어는 여러개의 column을 동시에 수정할 수 없다. 단, 하나에 한개의 작업만 수행할 수 있다.

<br/>

### 제약조건 추가하기

```tsx
// unique 옵션 추가
ALTER TABLE tableName ADD CONSTRAINT 제약조건이름 UNIQUE (columnName);

// PK키 설정 추가
ALTER TABLE tableName ADD PRIMARY KEY (columnName);
ALTER TABLE tableName ADD CONSTRAINT 제약조건이름 PRIMARY KEY (columnName);

// 외래키 설정 추가
ALTER TABLE child_tableName ADD CONSTRAINT 제약조건이름
FOREIGN KEY (child_columnName) REFERENCES parent(parent_columnName);

// 외래키 삭제
ALTER TABLE 테이블이름 DROP PRIMARY KEY

// check 옵션 추가
ALTER TABLE products ADD CONSTRAINT 제약조건이름 CHECK (price > 0);
```

위에서도 말했듯이 제약 조건을 추가할때는 `CONSTRAINT` 로 이름을 지정해주는 것이 일반적으로 좋은 관행이다.

<br/>

## DROP / TRUNCATE / DELETE

```tsx
// 테이블 삭제
DROP TABLE tableName 

// 칼럼값만 남기고 테이블의 모든 데이터 삭제
TRUNCATE TABLE tableName 

// 데이터 모두 삭제
DELETE FROM tableName
```

| DROP | TRUNCATE | DELETE |
| --- | --- | --- |
| DDL | DDL | DML |
| RollBack 불가능 | RollBack 불가능 | Commit 이전 Rollback 가능 |
| Auto Commit | Auto Commit  | 사용자 Commit |
| 테이블이 사용했던 모든 Storage 삭제 | 테이블이 사용했던 Storage 중 최초 테이블 생성시 할당된 Storage만 남기고 삭제 | 사용했던 Storage는 삭제되지 않음 |
| 테이블의 정의 자체를 완전히 삭제 | 테이블을 최초 생성된 초기상태로 만듬 | 데이터만 삭제 |

<br/>

## RENAME

```tsx
RENAME origin_table_name TO change_table_name
```

테이블 이름 변경하기

<br/>

## INSERT

```tsx
// 삽입 컬럼을 명시하지 않았을 경우 모든 컬럼을 삽입해야한다.
INSERT INTO tableName VALUES (data, data, data...); 

// 특정 데이터만 삽입하는 경우 삽입 컬럼을 명시해야한다.
INSERT INTO tableName (columnName, columnName) VALUES (data, data); 
```

<br/>

## UPDATE

```tsx
// 특정 column 값 변경
UPDATE tableName SET columnName = data WHERE 조건 

// 두개 한번에 변경
UPDATE tableName SET columnName1 = data1, columnName2 = data2 WHERE 조건 
```

WHERE 조건을 지정하지 않으면 데이터베이스의 모든 행이 업데이트 대상이 된다.

<br/>

## DELETE

```tsx
 // table의 모든 데이터 삭제
DELETE FROM tableName

// 조건을 달아서 삭제하기
DELETE FROM tableName WHERE 조건

// 조건 여러개 달아서 삭제하기
DELETE FROM tableName WHERE 조건 AND 조건 
DELETE FROM neflix WHERE video_name IN ('시그널', '나의 아저씨')

// X 잘못된 문법
DELETE * FROM tableName
```

**TRUNCATE** 
<br/>
DDL, ROLLBACK 불가능, AUTO COMMIT, 테이블 최초 생성 시 용량만 남기고 모두반환. 테이블을 최초 생성된 초기상태로 만듬

**DELETE**
<br/>
DML, ROLLBACK 가능(로그 남음), 사용자 COMMIT, 용량반환하지 않음,데이터만 삭제

<br/>

## SELECT

```tsx
// 테이블의 전체 데이터 조회
SELECT * FROM tableName 

// 특정 컬럼만 가져오기
SELECT columnName1, columnName2 FROM tableName 

// 특정 컬럼만 가져오기
SELECT * FROM tableName WHERE 조건절 

// 중복 데이터 하나로 불러오기
SELECT DISTINCT columnName FROM tableName WHERE 조건절 

// AS키워드로 별칭 설칭하기
SELECT columnName1 AS c1, columnName2 AS c2 FROM tableName 
```

**FROM - WHERE - GROUP BY - HAVING - SELECT - ORDER BY**

<br/>

### alias 사용

```tsx
SELECT tableA.name, tableA.age, tableB.job
FROM tableA, tableB

SELECT A.name, A.age, B.job
FROM tableA A, tableB B
```

이렇게 다중 테이블에서 SELECT할 경우 테이블 이름을 계속 쓰는건 불편하므로 `alias`로 줄여서 나타낼 수 있다.

<br/>

## WHERE

```tsx
// column
SELECT * FROM tableName WHERE columnName = "columnName"

// category가 movie나 animtion인 것들 조회
SELECT * FROM neflix WHERE category IN ("movie", "animation") 

// category가 movie나 animtion이 아닌것들 조회
SELECT * FROM neflix WHERE category NOT IN ("movie", "animation") 

// AND나 OR으로 여러개 조건 비교
SELECT * FROM neflix WHERE view < 70;
SELECT * FROM neflix WHERE view < 70 AND view > 50;
SELECT * FROM neflix WHERE category = "animation" AND view < 70 
SELECT * FROM neflix WHERE category = "animation" OR view < 70 

// BEWEEN으로 사이값 비교
SELECT * FROM neflix WHERE view BETWEEN 50 AND 70

// LIKE문으로 문자열 비교
SELECT * FROM neflix WHERE name LIKE "미%" // "미"로 시작하는 영화 가져오기
SELECT * FROM neflix WHERE name LIKE "%미" // "미"로 끝나는 영화 가져오기
SELECT * FROM neflix WHERE name LIKE "%미%" // "미"를 포함하는 영화 가져오기

// NULL을 WHERE에 사용할때는 IS를 사용해야 한다.
SELECT * FROM tableName WHERE columnName IS NULL
SELECT * FROM tableName WHERE columnName IS NOT NULL
```

<br/>

## JOIN

**Customers table**
| customer_id | customer_name |
| --- | --- |
| 1 | a |
| 2 | b |
| 3 | c |
| 4 | d |

**Orders table**

| custome_id | order_id |
| --- | --- |
| 1 | 101 |
| 2 | 102 |
| 1 | 103 |
| 5 | 104 |

<br/>

### LEFT-OUTER-JOIN

```sql
SELECT A.customer_id, A.customer_name, B.order_id 

FROM Customers_table A 

LEFT OUTER JOIN Orders_table B 

ON A.customer_id = B.customer_id
```

| customer_id | customer_name | order_id |
| --- | --- | --- |
| 1 | a | 101 |
| 1 | b | 103 |
| 2 | b | 102 |
| 3 | c | NULL |
| 4 | d | NULL |

**`LEFT OUTER JOIN`**은 왼쪽 테이블의 모든 레코드와 오른쪽 테이블의 일치하는 레코드를 가져오며, 오른쪽 테이블의 레코드가 일치하지 않는 경우에는 NULL 값을 가진 레코드가 생성된다.

왼쪽에 있는 table에 정보는 모두 가져와지며, 오른쪽에 있는 table은 ON조건에 일치하는 것만 가져와진다.

<br/>

### RIGHT-OUTER-JOIN

```sql
SELECT A.customer_id, A.customer_name, B.order_id 

FROM Customers_table A 

RIGHT OUTER JOIN Orders_table B 

ON A.customer_id = B.customer_id
```

| order_id | custome_id | customer_name |
| --- | --- | --- |
| 101 | 1 | a |
| 102 | 2 | b |
| 103 | 1 | a |
| 104 | 5 | NULL |

**`RIGHT OUTER JOIN`**은 왼쪽 테이블의 일치하는 레코드와 오른쪽 테이블의 모든 레코드를 가져오며, 왼쪽 테이블의 레코드가 일치하지 않는 경우에는 NULL 값을 가진 레코드가  생성된다.

<br/>

### FULL-OUTER-JOIN

```sql
SELECT A.customer_id, A.customer_name, B.order_id 

FROM Customers_table A 

FULL OUTER JOIN Orders_table B 

ON A.customer_id = B.customer_id
```

| customer_id | customer_name | order_id |
| --- | --- | --- |
| 1 | a | 101 |
| 1 | b | 103 |
| 2 | b | 102 |
| 3 | c | NULL |
| 5 | NULL | 104 |

**`FULL OUTER JOIN`**은 양쪽 테이블의 모든 레코드를 가져오며, 양쪽 테이블 중 어느 한쪽이라도 일치하지 않는 경우에는 NULL 값을 가진 레코드가 생성된다.

<br/>

### INNER JOIN ( Equal Join )

```tsx
SELECT A.name, A.age, B.name

FROM tableA A, tableB B

WHERE A.name = B.name
```

| customer_id | customer_name | order_id |
| --- | --- | --- |
| 1 | a | 101 |
| 1 | b | 103 |
| 2 | b | 102 |

<br/>

### INNER JOIN ( Non Equal Join )

```tsx
SELECT A.name, A.age, B.name

FROM tableA A, tableB B

WHERE A.name Like B.name
```

일반적으로 Join은 PK와 FK값의 연관성에 의해 성립된다.

Equal Join은 Join에 관여하는 테이블 간의 컬럼 값들이 정확하게 일치하는 경우에 사용된다.

Equal Join은 ‘=’ 연산자에 의해 수행되며, 그 이외의 비교 연산자를 사용하는 경우에는 모두 Non Equal Join이다. 대부분 Non Equal Join을 수행할 수 있지만 설계상의 이유로 수행이 불가능 한 경우도 있다.

<br/>

### SELF JOIN

```sql
// 직속상관을 출력하는 쿼리
SELECT A.id, A.name AS '직원명', B.name AS '직속상관'
FROM employee A, employee B
WHERE A.supervisor_id = B.id
```

<br/>

### CROSS JOIN

```sql
SELECT *
FROM Customers
CROSS JOIN Products;
```

위의 SQL 문장은 "Customers" 테이블과 "Products" 테이블을 `CROSS JOIN`하여 가능한 모든 고객과 제품 조합을 반환합니다

한쪽 테이블의 모든 행과 다른 쪽 테이블의 모든 행을 조인시키는 기능입니다. 상호 조인 결과의 전체 행 개수는 두 테이블의 각 행의 개수를 곱한 수만큼 됩니다. `카티션 곱(CARTESIAN PRODUCT)`라고도 합니다.

<br/>

## ORDER BY

```tsx
// 조회수 기준 오름차순 (ASC가 default)
SELETE * FROM neflix ORDER BY view ASC 

// 조회수 기준 내림차순
SELETE * FROM neflix ORDER BY view DESC 

// category별로 정렬한 다음, 조회수 기준 정렬
SELETE * FROM neflix ORDER BY category, view DESC 

// category는 내림차순 정렬한 다음, 조회수는 오름차순 기준 정렬
SELETE * FROM neflix ORDER BY category DESC, view ASC 

// GROUP BY를 사용할 경우 GROUP BY 표현식이 아닌 값은 ORDER BY에 기술될 수 없다.
SELECT 지역, SUM(매출금액) AS 매출금액
FROM 지역별 매출
GROUP BY 지역
ORDER BY 년 (x)

// GROUP BY를 사용하지 않은 경우는 상관없다.
SELECT 지역, 매출금액
FROM 지역별 매출
ORDER BY 년 (o)

// 첫번째 칼럼 기준 정렬 후, 두번째 칼럼 기준 정렬
SELECT 지역, 매출금액
FROM 지역별 매출
ORDER BY 1, 2
```

<br/>

## GROUP BY

```tsx
// category 별로 숫자세기
// null은 count 안된다
SELECT category, COUNT(*) FROM neflix GROUP BY category

// category 별로 조회수 합계
SELECT category, SUM(view) FROM neflix GROUP BY category

// category 별로 조회수가 가장 많은 값
SELECT category, MAX(view) FROM neflix GROUP BY category

// MAX값이 가장 큰 category부터 정렬
SELECT category, MAX(view) FROM neflix GROUP BY category ORDER BY MAX(view) DESC

// category 별로 조회수가 가장 작은 값
SELECT category, MIN(view) FROM neflix GROUP BY category

// category 별로 조회수 최대 최소값 함께 출력
SELECT category, MAX(view), MIN(view) FROM neflix GROUP BY category

// category 별로 조회 수 평균
SELECT category, AVG(view) FROM neflix GROUP BY category
```

<br/>

## HAVING

```tsx
SELECT customer_id, COUNT(order_id) AS order_count
FROM orders
GROUP BY customer_id
HAVING order_count >= 10;
```

`**Having**`절은 `**WHERE**`절과 비슷하게 특정 조건을 만족하는 그룹만을 선택하지만, 

`**GROUP BY**`로  그룹화된 결과 중에서 특정 조건을 만족하는 그룹만을 선택할 수 있다.

<br/>

## CASE WHEN

```tsx
SELECT column1, column2, column3
CASE WHEN column1 = 1 THEN 'ONE'  // 1인 값들에 대해선 'ONE'
		 WHEN column2 = 2 THEN 'TWO' ELSE 'THREE' // 2인 값들에 대해선 'TWO' 나머지는 'THREE'
```

`**CASE WHEN**`을 사용하면 기존에 있는 열의 조건에 따른 새로운 값을 갖는 새로운 열이 만들어지게 된다.

```tsx
SELECT column1, column2, column3
CASE WHEN column1 = 1 THEN 'ONE'  // 1인 값들에 대해선 'ONE' 나머지는 NULL
```

`**ELSE**`를 빼고 쓰면 일치하지 않는 값들에 대해선 NULL이 된다.

```tsx
SELECT column1, column2, column3
CASE column1 WHEN 1 THEN 'ONE' 
			WHEN 2 THEN 'TWO' ELSE 'THREE' AS new_col
```

`AS`를 사용하면 행의 이름을 지정해 줄 수 있다.

<br/>

## 내장 함수

### 날짜 함수

```tsx
// 현재 날짜 및 시각 가져오기
SELECT SYSDATE FROM DUAL;

// 년, 월만 출력하고 시간을 버리기
SELECT TRUNC(SYSDATE) FROM DUAL;

// 오늘 날짜로 부터 3달 후 날짜 출력
SELECT ADD_MONTHS(SYSDATE, 3) FROM DUAL;

// 오늘 날짜로 부터 3달 전 날짜 출력
SELECT ADD_MONTHS(SYSDATE, -3) FROM DUAL;

// 시간 문자열 포매팅
SELECT TO_CHAR(SYSDATE, 'YYYYMMDD HH24MISS') FROM DUAL;
SELECT TO_CHAR(SYSDATE, 'YYYY-MM-DD HH24:MI:SS') FROM DUAL;
SELECT TO_CHAR(SYSDATE, 'YYYY/MM/DD HH24:MI:SS') FROM DUAL;
```

<br/>

### REPLACE

```tsx
// 코드라이언에서 코드를 CODE로 변경
SELECT REPLACE('코드라이언', '코드', 'CODE') FROM DUAL

// 세번째 인자에 아무것도 적어주지 않으면 코드라이언에서 코드를 없애기
SELECT REPLACE('코드라이언', '코드') FROM DUAL

// 핸드폰 번호에서 - 삭제
SELECT REPLACE('010-1234-1234', '-') FROM DUAL

// 실제 테이블 사용 예 
SELECT REPLACE(columnName, "A", "B") FROM tableName
```

<br/>

### SUBSTR

```tsx
// 3번째 부터 2글자 잘라서 가져오기
SELECT SUBSTR("코드라이언", 3, 2) FROM DUAL // 라이

// 3번 인자에 아무것도 안쓰면 끝까지 가져옴
SELECT SUBSTR("코드라이언", 3, 2) FROM DUAL // 라이언

// 음수면 뒤에서부터 자리수 (4니까 뒤에서부터 4번째 글자)
SELECT SUBSTR("코드라이언", -4, 2) FROM DUAL // 드라이언

// 이름 변경 김민우 -> 김*우  || 는 이어 붙히기
SELECT SUBSTR(name, 1, 1) || "*" || SUBSTR(name, 3) FROM tableName

// 이 편지는...
SELECT SUBSTR("이 편지는 영국에서부터 시작되어 한 아이으 가정~~~", 1, 5) || "...." FROM DUAL
```

<br/>

### UPPER / LOWER

```tsx
// 대문자 변경
SELECT UPPER("abcd") FROM DUAL // ABCD

// 소문자 변경
SELECT LOWER("ABCD") FROM DUAL // ABCD

SELECT * FROM member WHERE ID = UPPER("collection");
```

<br/>

### 숫자 함수

```tsx
// 반올림
SELECT ROUND(3.16) FROM DUAL

// 반올림해서 소수점 아래 첫재자리 까지 반환
SELECT ROUND(3.16, 1) FROM DUAL

// 뒤에 소수점 버리기
SELECT TRUNC(3.16) FROM DUAL

// 올림
SELECT CEIL(3.16) FROM DUAL
```

<br/>

### TOP

```tsx
SELECT TOP 3 columnName
FROM tableName
ORDER BY columnName DESC
```

columnName으로 정렬하고 상위 3개 출력

```tsx
SELECT TOP 3 WITH TIES columnName
FROM tableName
ORDER BY columnName DESC
```

만약 같은 값이 있다면 같이 출력

```tsx
SELECT TOP 50 PERCENT columnName
FROM tableName
ORDER BY columnName DESC
```

상위 50퍼센트 출력

<br/>

### 집계 함수

```tsx
// 모든 행의 수를 출력하며 NULL 값을 가진 행도 포함
SELECT COUNT(*) AS count FROM tableName

// 표현식의 값이 NULL인 것을 제외한 행의 수를 출력
SELECT COUNT(columnName) AS c1 FROM tableName;

// 표현식의 NULL을 제외한 합계 출력
SELECT SUM(columnName) AS c1 FROM tableName;

// 표현식의 NULL을 제외한 평균 출력
SELECT AVG(columnName) AS c1 FROM tableName;

// 최대값 출력
SELECT MAX(columnName) AS c1 FROM tableName;

// 최소값 출력
SELECT MIN(columnName) AS c1 FROM tableName;

// 표준 편차 출력
SELECT STDDEV(columnName) AS c1 FROM tableName;

// 분산 출력
SELECT VARIAN(columnName) AS c1 FROM tableName;
```

<br/>

### 단일행 NULL 관련 함수

```tsx
// ISNULL(A, B) → A column의 값이 NULL이라면 B로 대체 (SQL)
SELECT column1, column2, ISNULL(column3, '값 없음') FROM tableName

// NVL(A, B) → A column의 값이 NULL이라면 B로 대체 (ORACLE)
SELECT column1, column2, NVL(column3, '값 없음') FROM tableName

// NULLIF(A, B) → A column의 값이 B와 같으면 NULL을, 같지 않으면 A column의 값을 return
SELECT column1, column2, NULLIF(column3, 1) FROM tableName

// NULL이 아닌 최초의 표현식 나타냄, 모든게 NULL이면 NULL return
SELECT column1, column2, COALESCE(column3, column4, column5) IS FROM tableName
```

```sql
SELECT A, B, C COALESCE(A, B, C) FROM tableName;
```

| A | B | C | COALESCE(A, B) |
| --- | --- | --- | --- |
| 1 | 2 | NULL | 1 |
| NULL | NULL | 3 | 3 |
| NULL | 1 | NULL | 1 |
| NULL | NULL | NULL | NULL |

<br/>

## Transaction

`Transaction`은 데이터베이스의 논리적 연산단위로서 밀접히 관련되어 분리될 수 없는 한 개 이상의 데이터베이스 조작을 가리킨다.

- `**원자성**` - 트랙잭션에서 정의된 연산들은 모두 성공적으로 실행되던지 전혀 실행되지 않은 상태로 남아있어야 한다.
- `**일관성**` - 트랜잭션이 실행되기 전의 데이터베이스 내용이 잘못되어 있지 않다면 트랙잭션이 실행된 이ㅜㅎ에도 데이터베이스의 내용에 잘못이 있으면 안된다.
- `**고립성**` - 트랜잭션이 실행되는 도중에 다른 트랜잭션의 영향을 받아 잘못된 결과를 만들어서는 안된다
- `**지속성**` - 트랜잭션이 성공적으로 수행되면 그 트랜잭션이 갱신한 데이터베이스의 내용은 영구적으로 저장된다.
- `**Dirty Read**` - 수정되었지만 commit이 되지 않은 데이터를 읽는 것
- `**Non-Repeatbale Read**` - 조회 / 수정 / 조회 순으로 일어나면 두 조회의 결과물이 다를 수 있다. ( 조회된 레코드의 값이 변경 됨 )
- `**Phantom Read**` -  조회 / 삽입 or 삭제 / 조회 순으로 일어나서 두 조회의 결과물이 다를 수 있다. ( 새로운 값이 생겨나거나 없어짐 )

| ORACLE | SQL Server |
| --- | --- |
| DDL 문장 수행 후 자동 COMMIT | DDL 문장 수행 후 자동 COMMIT 하지 않음 |
| DDL 문장의 수행은 내부적으로 Transaction 종료 | CREATE도 Transaction에 포함됨으로 Rollback시 취소 |
| 데이터 값을 공백으로 입력하면 NULL 입력됨 | 데이터 값을 공백으로 입력하면 “” 입력됨 |

## 서브쿼리

### WHERE절에 들어가는 쿼리

**단일행 연관 서브쿼리**

```tsx
// Jones보다 높은 급여를 받는 사원들 출력
SELECT *
FROM employees
WHERE salary > (SELECT salary FROM employees WHERE name = 'JONES');
```

이때 JONES라는 이름이 여러개 존재하면 에러가 발생한다. 괄호안의 결과는 하나여야 한다.

<br/>

**다중행 연관서브쿼리**

```tsx
SELECT * FROM employees A
WHERE A.department_id IN
     (SELECT B.department_id FROM department B WHERE b.location_id = 1700)
```

 여러개의 결과를 확인하고 싶다면 `IN`을 사용해주면 에러가 발생하지 않는다.

<br/>

**WHERE EXIST**

```sql
// 런닝맨과 무한도전 모두 참여하는 참여자 
SELECT * FROM 런닝맨 A 
WHERE EXISTS (SELECT 1 FROM 무한도전 B WHERE A.NAME = B.NAME)
```

`WHERE EXIST` 다음의 `SELECT` 절 안의 컬럼은 SELECT 1 처럼 아무 쿼리나 적어도 된다. 중요한 건 조건에 맞춰 반환값이 있냐 없냐이기 때문이다. `WHERE` 절의 조건만 유심히 확인하면 된다.

<br/>

**WHERE NOT EXISTS**

```sql
SELECT * FROM 런닝맨 A 
WHERE NOT EXISTS (SELECT 1 FROM 무한도전 B WHERE A.NAME = B.NAME)
```

런닝맨에는 참여하지만 무한도전에는 참여하지 않는 참여자 ( 차집합 ) 

<br/>

### FROM절 서브쿼리 (Inline view)

```sql
SELECT E.EMPNO, E.ENAME, E.DEPTNO, D.DNAME, D.LOC
FROM (SELECT * FROM EMP WHERE DEPTNO = 10) E, DEPT D
WHERE E.DEPTNO = D.DEPTNO;
```

FROM절에 직접 테이블을 명시하여 사용하기에 데이터 규모가 크거나 불필요한 열이 많을 때, 일부 행과 열만 사용하고자 할 때 사용한다.

<br/>

### SELECT절 서브쿼리 (스칼라 서브쿼리)

```sql
// customers 테이블의 각 고객에 대해 해당 고객의 주문 수
SELECT customers.customer_name,
    (
      SELECT COUNT(*)
      FROM orders
      WHERE orders.customer_id = customers.customer_id
    ) AS order_count
FROM customers;
```

 select절 서브쿼리는 `join`으로도 동일한 결과 도출 가능하다.

<br/>

## 집합연산자

### UNION

두개의 테이블을 하나로 만드는 연산, 중복되는 데이터는 하나로 처리

### UNION ALL

두개의 테이블을 하나로 만드는 연산, 중복되는 데이터에 대해 중복 제거를 하지 않음

### INTERSECT

두개의 테이블에 대해 겹치는 부분을 추출하는 연산. 중복되는 데이터는 하나로 처리

### EXCEPT

앞의 수행 결과에서 뒤의 결과를 차집합. 중복되는 데이터는 하나로 처리

<br/>

```sql
SELECT column1 A, column2 B
FROM table1
UNION ALL
SELECT column1 C, column2 D
FROM table2
```

결과에서 `column1`을 표현할때는 첫번째 SELECT 문장의 `alias` 인 `A`가 적용이 된다.

<br/>

## START WITH

- START WITH는 Oracle에서만 사용할 수 있다.
- SQL Server에서의 계층형 질의문은 CTE를 재귀호출함으로써 계층 구조를 전개한다.
- SQL Server에서의 계층형 질의문은 앵커 멤버를 실행하여 기본 결과 집합을 만들고 이후 재귀 멤버를 지속적으로 실행한다.
- Oracle의 계층형 질의문에서 WHERE절은 모든 전개를 진행한 이후 필터 조건으로서 조건을 만족하는 데이터만을 추출하는데 활용한다.
- PRIOR키워드는 SELECT에서도 사용할 수 있다.
  
<br/>


| C1 | C2 | C3 |
| --- | --- | --- |
| 1 | NULL | A |
| 2 | 1 | B |
| 3 | 1 | C |
| 4 | 2 | D |

```sql
SELECT * FROM TABLE
START WITH C2 IS NULL // 1행이 level 1이 된다.
CONNECT BY PRIOR C1 = C2 // c2가 1인 것들이 level2가 된다.
ORDER SIBLINGS BY C3 DESC // 자식들끼리는 c3기준 내림차순 정렬을 한다.
```

| C1 | C2 | C3 | level |
| --- | --- | --- | --- |
| 1 | NULL | A | 1 |
| 3 | 1 | C | 2 |
| 2 | 1 | B | 2 |
| 4 | 2 | D | 3 |

<br/>

### 순방향 전개 & 역방향 전개

| 부서코드 | 부서명 | 상위 부서코드 |
| --- | --- | --- |
| 100 | 아시아부  | NULL |
| 110 | 한국지사 | 100 |
| 111 | 서울지점 | 110 |
| 120 | 일본지사 | 100 |
| 121 | 도쿄지점 | 120 |
| 122 | 오사카지점 | 120 |

<br/>

**순방향전개**

```sql
SELECT * FROM TABLE
START WITH 부서코드 = 120
CONNECT BY PRIOR 부서코드 = 상위 부서코드 
```

부모 노드로부터 자식 노드 방향으로 전개하는 것

| 부서코드 | 부서명 | 상위 부서코드 | level |
| --- | --- | --- | --- |
| 120 | 일본지사 | 100 | 1 |
| 121 | 도쿄지점 | 120 | 2 |
| 122 | 오사카지점 | 120 | 2 |

<br/>

**역방향전개**

```sql
SELECT * FROM TABLE
START WITH 부서코드 = 120
CONNECT BY PRIOR 상위 부서코드 = 부서코드 
```

자식 노드로부터 부모 노드 방향으로 전개하는 것

| 부서코드 | 부서명 | 상위 부서코드 | level |
| --- | --- | --- | --- |
| 120 | 일본지사 | 100 | 1 |
| 100 | 아시아부 | NULL | 2 |

<br/>

### 계층형 쿼리에서의 조건절

```sql
SELECT * FROM TABLE
START WITH C2 IS NULL // 1행이 level 1이 된다.
CONNECT BY PRIOR C1 = C2 // c2가 1인 것들이 level2가 된다.
AND 조건~~~
ORDER SIBLINGS BY C3 DESC // 자식들끼리는 c3기준 내림차순 정렬을 한다.
```

`START WITH`에 해당하는 행들은(여기선 `C2`) 조건과 상관없이 select가 된다!! 주의

<br/>

## VIEW

: 하나 이상의 기본 테이블이나 다른 뷰를 이용하여 생성되는 가상 테이블

- - 기본 테이블은 디스크에 공간이 할당되어 데이터를 저장함
- - 뷰는 데이터 딕셔너리 (Data Dictionary) 테이블에 뷰에 대한 정의 (SQL 문) 만 저장되어 디스크 저장 공간 할당이 이루어지지 않음
- - 전체 데이터 중에서 일부만 접근할 수 있도록 함
- - 뷰에 대한 수정 결과는 뷰를 정의한 기본 테이블에 적용됨
- - 뷰를 정의한 기본 테이블에서 정의된 무결성 제약조건은 그대로 유지됨

뷰의 필요성

- - 사용자 마다 특정 객체만 조회할 수 있도록 할 필요가 있음

(모든 직원에 대한 정보를 모든 사원이 볼 수 있도록 하면 안 됨)

- - 복잡한 질의문을 단순화 할 수 있음
- - 데이터의 중복성을 최소화할 수 있음

예) 판매부에 속한 사원들만을 사원테이블에서 찾아서 다른 테이블로 만들면 중복성이 발생함

- > 이럴 때 뷰가 필요함

**create view 뷰이름 as sql문 (select문)** ;

예) 사원 테이블에 부서번호 30인 사원들의 뷰 생성하기

```sql
create view emp30 as select * from employee where dno=30;
```

<br/>

## ROLLUP

`ROLL UP`함수는 소그룹간의 합계를 계산하는 함수이다. `ROLLUP`을 사용하면 `GROUP BY`로 묶은 각각의 소그룹 합계와 전체 합계를 모두 구할 수 있다.

<br/>

```sql
SELECT 상품ID, 월, SUM(매출액) AS 매출액
FROM 월별매출
GROUP BY 상품ID, 월;
```

| 상품ID | 월 | 매출액 |
| --- | --- | --- |
| P1 | 1 | 1000 |
| P1 | 2 | 2000 |
| P2 | 1 | 1000 |
| P2 | 1 | 3000 |
| P3 | 2 | 4000 |

이렇게 `GROUP BY` 만 작성한다면 상품ID의 월별 합계만 보여주지만

<br/>

```sql
SELECT 상품ID, 월, SUM(매출액) AS 매출액
FROM 월별매출
GROUP BY ROLLUP(상품ID, 월);
```

| 상품ID | 월 | 매출액 |
| --- | --- | --- |
| P1 | 1 | 1000 |
| P1 | 2 | 2000 |
| p1 | null | 3000 |
| P2 | 1 | 1000 |
| P2 | 1 | 3000 |
| P2 | null | 4000 |
| P3 | 2 | 4000 |
| P3 | null | 4000 |
| null | null | 11000 |

`ROLL UP` 으로 묶어준다면 상품ID의 월별 합계, 상품ID별 합계, 총 합계 이렇게 3개를 보여주게 된다.

<br/>

## CUBE

`CUBE`함수는 항목들 간의 다차원적인 소계를 계산한다. `ROLL UP`과 달리 **GROUP BY 절에 명시한 모든 컬럼**에 대해 소그룹 합계를 계산해준다.

<br/>

```sql
SELECT 상품ID, 월, SUM(매출액) AS 매출액
FROM 월별매출
GROUP BY CUBE(상품ID, 월);
```

| 상품ID | 월 | 매출액 |
| --- | --- | --- |
| P1 | 1 | 1000 |
| P1 | 2 | 2000 |
| p1 | null | 3000 |
| P2 | 1 | 1000 |
| P2 | 1 | 3000 |
| P2 | null | 4000 |
| P3 | 2 | 4000 |
| P3 | null | 4000 |
| null | 1 | 5000 |
| null | 2 | 6000 |
| null | null | 11000 |

`ROLL UP` 은 상품ID에 대해 합계만 보여주지만, `CUBE` 는 월별 매출액도 보여주게된다.

<br/>

## GROUPING SETS

```sql
SELECT 상품ID, 월, SUM(매출액) AS 매출액
FROM 월별매출
GROUP BY GROUPING SETS(상품ID, 월);
```

| 상품ID | 월 | 매출액 |
| --- | --- | --- |
| p1 | null | 3000 |
| P2 | null | 4000 |
| P3 | null | 4000 |
| null | 1 | 5000 |
| null | 2 | 6000 |

`GROUPING SETS`는 특정 항목에 대한 소계를 계산하는 함수이다.

`ROLL UP`과 `CUBE`는 GROUP BY 결과에 소그룹 합계와 토탈 합계를 보여주지만 `GROUPING SETS`는 각 소그룹별 합계만 간단하게 보여준다.

<br/>

```sql
SELECT 상품ID, 월, SUM(매출액) AS 매출액
FROM 월별매출
GROUP BY GROUPING SETS((상품ID), (월), (상품ID, 월), ());
// == GROUP BY CUBE(상품ID, 월);
```

`GROUPING SETS` 안에 특정 컬럼들을 작성하면 해당 칼럼들에 대해 합계들을 순서로 보여준다.

## GROUPING

```sql
SELECT
    CASE GROUPING(상품ID) WHEN 1 THEN '모든 상품ID' ELSE 상품ID END AS 상품ID,
    CASE GROUPING(월) WHEN 1 THEN '모든 월' ELSE 월 END AS 월,
    SUM(매출액) AS 매출액
FROM 월별매출
GROUP BY ROLLUP(상품ID, 월);
```

| 상품ID | 월 | 매출액 | GROUPING(상품ID) | GROUPING(월) |
| --- | --- | --- | --- | --- |
| P1 | 1 | 1000 | 0 | 0 |
| P1 | 2 | 2000 | 0 | 0 |
| P1 | 모든 월 | 3000 | 0 | 1 |
| P2 | 1 | 1000 | 0 | 0 |
| P2 | 1 | 3000 | 0 | 0 |
| P2 | 모든 월 | 4000 | 0 | 1 |
| P3 | 2 | 4000 | 0 | 0 |
| P3 | 모든 월 | 4000 | 0 | 1 |
| 모든 상품 ID | 모든 월 | 11000 | 1 | 1 |

`GROUPING`은 직접적으로 그룹별 집계를 구하는 함수는 아니지만 위의 집계함수들을 지원하는 함수이다.

집계가 계산된 결과에 대해서는 1의 값을 갖고 그렇지 않은 결과에 대해서는 0의 값을 갖는다.

`CASE WHEN`문을 사용해서 맨 처음에 단순 `ROLL UP`함수만 썼을 때 NULL값으로 표시되었던 곳에 값을 넣어주었다. 집계가 계산된 결과에 대해서만 값을 넣어주면 되기 때문에 GROUPING(컬럼명)=1인 경우에만 '모든상품ID' 또는 '모든월' 값을 부여했고 0인 경우에는 원래대로 상품ID와 월을 써주었다.

<br/>

## 윈도우 함수

`GROUP BY` 는 집약 기능으로 인해 행 수가 줄어든 반면, 윈도우 함수는 행 수가 그대로 남아있다.

윈도우 함수에는 집약의 기능이 없기 때문이다.

이러한 특징을 이용해서 우리는 행과 행간의 관계를 편하게 다룰 수 있게 된다.

`PARTITION BY`를 붙히면 해당 데이터 별로 순위가 지정된다. 붙히지 않으면 전체 행에 대해 적용된다.

<br/>

### 순위함수

**RANK**

```sql
SELECT JOB, ENAME, SAL, 
       RANK() OVER (ORDER BY SAL DESC) JOB_RANK 
FROM EMP;

JOB       ENAME             SAL   JOB_RANK
--------- ---------- ---------- ----------
ANALYST   FORD             3000          1
ANALYST   SCOTT            3000          1
CLERK     MILLER           1300          3
CLERK     ADAMS            1300          3
CLERK     JAMES             950          5
CLERK     SMITH             800          

SELECT JOB, ENAME, SAL, 
       RANK() OVER (PARTITION BY JOB ORDER BY SAL DESC) JOB_RANK 
FROM EMP;

JOB       ENAME             SAL   JOB_RANK
--------- ---------- ---------- ----------
ANALYST   FORD             3000          1
ANALYST   SCOTT            3000          1
CLERK     MILLER           1300          1
CLERK     ADAMS            1300          1
CLERK     JAMES             950          3
CLERK     SMITH             800          4
```

ORDER BY를 포함한 쿼리문에서 특정 컬럼의 순위를 구하는 함수이다. `PARTITION` 내에서 순위를 구할 수도 있고 전체 데이터에 대한 순위를 구할 수도 있다. 동일한 값에 대해서는 같은 순위를 부여하며 중간 순위를 비운다.

예를 들어 동일한 값이 있는 경우 순위는 1,1,3,4,4,6 이런식인 셈

<br/>

**DENSE_RANK**

```sql
SELECT JOB, ENAME, SAL, 
       DENSE_RANK() OVER (PARTITION BY JOB ORDER BY SAL DESC) JOB_RANK 
FROM EMP;

JOB       ENAME             SAL   JOB_RANK
--------- ---------- ---------- ----------
ANALYST   FORD             3000          1
ANALYST   SCOTT            3000          1
CLERK     MILLER           1300          1
CLERK     ADAMS            1300          1
CLERK     JAMES             950          2
CLERK     SMITH             800          3
```

`RANK`와 작동법은 동일하지만, 동일한 값에 대해서는 같은 순위를 부여하고 중간 순위를 비우지 않는다. 

예를들어 동일한 값이 있는 경우 순위는 1,1,2,3,3,4 이런 식

<br/>

**ROW_NUMBER**

```sql
SELECT JOB, ENAME, SAL, 
       ROW_NUMBER() OVER (PARTITION BY JOB ORDER BY SAL DESC) JOB_RANK 
FROM EMP;

JOB       ENAME             SAL   JOB_RANK
--------- ---------- ---------- ----------
ANALYST   FORD             3000          1
ANALYST   SCOTT            3000          2
CLERK     MILLER           1300          1
CLERK     ADAMS            1300          2
CLERK     JAMES             950          3
CLERK     SMITH             800          4
```

`RANK`, `DENSE_RANK`는 동일한 값에 대해 동일 순위를 부여하지만 `ROW_NUMBER`은 동일한 값이어도 고유한 순위를 부여한다.

<br/>

### 그룹 내 행 순서 함수

**FIRST_VALUE**

```sql
SELECT  DEPTNO, ENAME, SAL,
        FIRST_VALUE(ENAME) OVER (PARTITION BY DEPTNO ORDER BY SAL DESC
        ROWS UNBOUNDED PRECEDING) DEPT_RICH
FROM    EMP ;
```

파티션별 윈도우에서 가장 먼저 나온 값을 구할 수 있다.

<br/>

**LAST_VALUE**

```sql
SELECT  DEPTNO, ENAME, SAL,
        LAST_VALUE(ENAME) OVER (PARTITION BY DEPTNO ORDER BY SAL DESC
        ROW BETWEEN CURRENT ROW AND UNBOUNDED FOLLOWING) DEPT_POOR
FROM    EMP ;
```

파티션별 윈도우에서 가장 마지막에 나온 값을 구할 수 있다.

<br/>

**LAG**

```sql
SELECT ENAME, HIREDATE, SAL
     , LAG(SAL) OVER (ORDER BY HIREDATE) as PREV_SAL 
FROM EMP 
WHERE JOB = 'SALESMAN';

ENAME      HIREDATE         SAL   PREV_SAL
---------- --------- ---------- ----------
ALLEN      20-FEB-81       1600
WARD       22-FEB-81       1250       1600
TURNER     08-SEP-81       1500       1250
MARTIN     28-SEP-81       1250       1500

--HIREDATE를 기준으로 정렬하고 본인보다 입사일자가 하나 더 앞선 사원의 급여를 출력

SELECT ENAME, HIREDATE, SAL
     , LAG(SAL, 2, 0) OVER (ORDER BY HIREDATE) as PREV_SAL 
FROM EMP 
WHERE JOB = 'SALESMAN';

ENAME      HIREDATE         SAL   PREV_SAL
---------- --------- ---------- ----------
ALLEN      20-FEB-81       1600          0
WARD       22-FEB-81       1250          0
TURNER     08-SEP-81       1500       1600
MARTIN     28-SEP-81       1250       1250

--HIREDATE를 기준으로 정렬하고 본인보다 입사일자가 두 개 더 앞선 사원의 급여를 출력
--두 개 더 앞선 사원이 없을 경우 0을 출력
```

이전 몇 번째 행의 값을 가져오는 함수이다. 인자를 최대 3개까지 가진다.

두번째 인자는 몇 번째 앞의 행을 가져올지 결정하는 것이며 DEFAULT값은 1이다. 세번째 인자는 가져올 행이 없을 경우 DEFAULT값을 지정해주는 것으로 `NVL`이나 `ISNULL`함수의 기능과 동일하다.

<br/>

**LEAD**

```sql
SELECT ENAME, HIREDATE
     , LEAD(HIREDATE, 1) OVER (ORDER BY HIREDATE) as "NEXTHIRED" 
  FROM EMP;

ENAME      HIREDATE  NEXTHIRED
---------- --------- ---------
SMITH      17-DEC-80 20-FEB-81
ALLEN      20-FEB-81 22-FEB-81
WARD       22-FEB-81 02-APR-81
JONES      02-APR-81 01-MAY-81
BLAKE      01-MAY-81 09-JUN-81
CLARK      09-JUN-81

--HIREDATE를 기준으로 정렬하고 본인보다 HIREDATE가 하나 더 뒤인 날짜를 출력
--없는 경우 NULL
```

이후 몇 번째 행의 값을 가져오는 함수로 LAG와 마찬가지로 인자를 최대 3개까지 갖는다.

<br/>

### 그룹 내 집계 함수

```sql
윈도우 함수() OVER (partition by 컬럼 order by 컬럼 RANGE BETWEEN A AND B)

윈도우 함수() OVER (partition by 컬럼 order by 컬럼 ROWS BETWEEN A AND B)

UNBOUNDED PRECEDING // 최종 출력될 값의 맨 처음 row 값

CURRENT ROW // 현재 row 값

UNBOUNDED FOLLOWING // 최종 출력될 값의 맨 마지막 row 값
```

```sql
// 직업별 월급 누적 합계 구하기
SELECT ENAME, JOB, SAL, 
			 SUM(SAL) OVER (PARTITION BY JOB) AS 월급 누적 합계
FROM EMP

// 직업별 월급 누적 합계 구하고, 월급 순서대로 보여주기
SELECT ENAME, JOB, SAL, 
			 SUM(SAL) OVER (PARTITION BY JOB ORDER BY SAL) AS 월급 누적 합계
FROM EMP
```

```tsx
MAX(열) OVER (PARTITION BY 그룹열)  -- 예시) 최대값
 
SUM(열) OVER (ORDER BY 순서열) -- 누적합
SUM(열) OVER (ORDER BY 순서열 PARTITION BY 그룹열) -- 그룹끼리 누적합
```
<br/>


### SUM

```sql
SELECT MGR, ENAME, SAL, 
       SUM(SAL) OVER (PARTITION BY MGR) MGR_SUM
FROM   EMP ;
```

<br/>

### 그룹 내 비율 함수

RATIO_TO_REPORT

```sql
SELECT ENAME, SAL
     , ROUND(RATIO_TO_REPORT(SAL) OVER (), 2) as R_R
  FROM EMP
 WHERE JOB = 'SALESMAN';

ENAME             SAL        R_R
---------- ---------- ----------
ALLEN            1600        .29
WARD             1250        .22
MARTIN           1250        .22
TURNER           1500        .27

--전체 급여에서 각각이 차지하는 비율 출력
```

파티션 내 전체 SUM값에 대한 행별 컬럼 값의 백분율을 소수점으로 출력한다. 결과값은 0~1 사이이며 개별 비율의 합을 구하면 1이다.

<br/>

**PERCENT_RANK**

```sql
SELECT DEPTNO, ENAME, SAL
     , PERCENT_RANK() OVER (PARTITION BY DEPTNO ORDER BY SAL DESC) as P_R
  FROM EMP;

    DEPTNO ENAME             SAL        P_R
---------- ---------- ---------- ----------10 KING             5000          0
        10 CLARK            2450         .5
        10 MILLER           1300          1
        20 SCOTT            3000          0
        20 FORD             3000          0
        20 JONES            2975         .5
        20 ADAMS            1100        .75
        20 SMITH             800          1
        30 BLAKE            2850          0
        30 ALLEN            1600         .2
        30 TURNER           1500         .4
        30 MARTIN           1250         .6
        30 WARD             1250         .6
        30 JAMES             950          1

--DEPTNO를 기준으로 파티셔닝해서 순서별 백분율 출력
```

파티션별로 가장 먼저 나오는 값을 0, 가장 마지막에 나오는 값을 1로 해서 행 순서별 백분율 출력한다. 구간을 나누어 백분율로 표시한다.

<br/>

**CUME_DIST**

```sql
SELECT DEPTNO, ENAME, SAL
     , CUME_DIST() OVER (PARTITION BY DEPTNO ORDER BY SAL DESC) as CUME_DIST
  FROM EMP;

    DEPTNO ENAME             SAL  CUME_DIST
---------- ---------- ---------- ----------10 KING             5000 .333333333
        10 CLARK            2450 .666666667
        10 MILLER           1300          1
        20 SCOTT            3000         .4
        20 FORD             3000         .4
        20 JONES            2975         .6
        20 ADAMS            1100         .8
        20 SMITH             800          1
        30 BLAKE            2850 .166666667
        30 ALLEN            1600 .333333333
        30 TURNER           1500         .5
        30 MARTIN           1250 .833333333
        30 WARD             1250 .833333333
        30 JAMES             950          1

--DEPTNO를 기준으로 파티셔닝해서 누적 백분율 출력
```

파티션별 전체건수에서 현재 행보다 작거나 같은 건수에 대한 `누적백분율` 을구한다.

<br/>

**NTILE**

```sql
SELECT ENAME, SAL
     , NTILE(4) OVER (ORDER BY SAL DESC) as QUAR_TILE
  FROM EMP ;

ENAME             SAL  QUAR_TILE
---------- ---------- ----------
KING             5000          1
FORD             3000          1
SCOTT            3000          1
JONES            2975          1
BLAKE            2850          2
CLARK            2450          2
ALLEN            1600          2
TURNER           1500          2
MILLER           1300          3
WARD             1250          3
MARTIN           1250          3
ADAMS            1100          4
JAMES             950          4
SMITH             800          4

--전체건수를 ARGUMENT값(여기서는 4)으로 N등분한 결과 출력--14를 4로 나누면 몫이 3 나머지가 2, 나머지2는 앞의 조부터 할당되기 때문에 4,4,3,3으로 나눔
```

파티션별 전체 건수를 ARGUMENT값으로 N등분한 결과를 출력한다.

<br/>

## 권한부여 하기

`DBMS` 사용자를 생성하면 기본적으로 많은 권한을 부여해야한다.

많은 `DBMS` 에서는 `DBMS`  관리자가 사용자별로 권한을 관리해야 하는 부담과 복잡함을 줄이기 위하여 다양한 권한을 그룹으로 묶어 관리할 수 있도록 사용자와 권한 사이에서 중개 역할을 수행하는 `ROLE` 을 제공한다.

<br/>

### GRANT(권한 부여)

```sql
// 사용자 U1, U2, U3에게 professor 테이블에 대한 select 문장을 사용할 수 있는 권한을 부여
Grant select on professor to U1, U2, U3;

// 사용자 U4에게 professor 테이블에 대한 select 문장을 사용할 수 있는 권한을 부여하고 
// 또한 U4에게 받은 권한을 다른 사용자에게 부여할 수 있는 권한을 함께 부여
Grant select on professor to U4 with grant option;

// Lee 사용자에게 department 테이블의 deptName 속성을 참조하는 외래키를 생성하는 권한을 부여
Grant references (deptName) on department to Lee;

// 모든사용자에게 권한부여
Grant select on professor to public; 
```

<br/>

### REVOKE(권한 차단)

```sql
// U1에게만 professor 테이블에 대한 select 권한을 회수하라
Revoke select on professor from U1

// U1, U2, U3는 물론이고 U1,U2,U3가 싸질러놓은 권한까지도 모두 철회하시오
Revoke select on professor from U1, U2, U3 cascade;

// U1, U2, U3가 아무것도 싸질러놓지 않은 경우에만 얘들 권한을 모두 철회하시오.
Revoke select on professor from U1, U2, U3 restrict;

// 모든 사용자 권한 철회
revoke select on professor from public
```

**cascade** 

만약에 사용자 U1이 U2에게 특정 권한을 주었고, 또 U2가 동일 권한을 사용자 U3에게 부여했을 때, 이 경우 사용자 U1이 U2에게 권한 취소를 한다면 어떻게 될까?

`cascade` 옵션은 권한 취소 시에 취소되는 권한으로 인하여 함께 취소가 되어야 하는 권한이 있으면 그 권한도 함께 취소하는 것이며, 즉 U1이 U2에게 권한취소 명령을 내렸을 때, 거기에 딸린 놈 U3의 권한도 철회된다.

**restrict**

만약에 사용자 U1이 U2에게 특정권한을 주었는데, U3가 U2에게 동일권한을 부여하였다면 사용자 U1이 `Revoke select on professor from U2;` 라는 구문을 입력했을 때 U2의 select권한은 없어질까?

이 경우 사용자 U3도 `Revoke select on professor from U2;` 를 입력해야 U2는 professor에대한 select권한이 완전히 없어지게 된다.