# sqld 기본 및 활용


## SQL 문장의 종류

**`FROM` - `WHERE` - `GROUP BY` - `HAVING` - `SELECT` - `ORDER BY`**

- **(비절차적) 데이터 조작어 (DML) →** SELECT, INSERT, UPDATE, DELETE
- **데이터 정의어 (DDL) →** CREATE, ALTER, DROP, RENAME
- **데이터 제어어 (DCL) →** GRANT, REVOKE
- **트랜잭션 제어어 (TCL) →** COMMIT, ROLLBACK

<br />

## 테이블명 설정

- 영문자로 시작해야함
- 중복되면 안됨
- 예약서 사용불가
- A-Z, a-z, 0-9, _ ,#, $ 의 문자만 허용한다.
- 끝은 항상 ;로 끝남

abc_123 (0) /  _abc (x)   / 123abc (x)

<br />

## CREATE

```tsx
CREATE TABLE member(
		id VARCHAR2(20),
    password VARCHAR2(20),
    name VARCHAR2(20)
)
```

<br />

- NOT NULL

```tsx
CREATE TABLE member(
		id VARCHAR2(20) NOT NULL,
    password VARCHAR2(20) NOT NULL,
    name VARCHAR2(20)
)
```

<br />

- DEFAULT

```tsx
CREATE TABLE member(
		id VARCHAR2(20) NOT NULL,
    password VARCHAR2(20) DEFAULT '1234',
    name VARCHAR2(20) DEFAULT 'NICKNAME' NOT NULL
)
```

**`DEFAULT`** 옵션은 새로운 레코드가 테이블에 삽입될 때 해당 컬럼에 값을 제공하지 않을 경우 사용되는 기본 값을 설정합니다.

<br />

- UNIQUE

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

<br />

- CHECK

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

<br />

- PRIMARY

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

PRIMARY KEY 설정

<br />

```tsx
CREATE TABLE member(
		id VARCHAR2(20) CONSTRAINT pk_id PRIMARY KEY,
    password VARCHAR2(20),
    name VARCHAR2(20)
)
```

PRIMARY KEY 하며 제약 조건에 이름 달기

<br />

```tsx
CREATE TABLE orders (
	order_id INT,
	product_id INT,
	quantity INT,
	PRIMARY KEY (order_id, product_id)
);
```

여러 컬럼을 조합하여 복합 기본 키(Composite Primary Key) 설정하기

<br />

- FOREIGN KEY

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

member_score에서 memeber에 없는 id를 삽입하려 한다면 `DEPENDENT` 오류 발생한다.

<br />

- 외래키 삭제 옵션

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
		// 부모 키를 삭제 시 자식 키를 NULL로 변경한다.
		FOREIGN KEY(id) REFERENCES member(id) ON DELETE SET NULL
)
```

<br />

- 칼럼에 주석 달기

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

<br />

- indexing

```tsx
CREATE TABLE tableName (
	columnName dataType(SIZE) NOT NULL,
	columnName dataType(SIZE) DEFAULT,
)

CREATE INDEX indexName ON tableName (columnName)
```

<br />

## ALTER

```tsx
// 칼럼 추가
ALTER TABLE tableName ADD (columnName dataType(SIZE)) 

// 사이즈 변경, 데이터 타입 변경
ALTER TABLE tableName MODDIFY (columnName dataType(SIZE))

// NOT NULL 옵션 추가
ALTER TABLE tableName MODIFY columnName VARCHAR(100) NOT NULL;

// 컬럼 삭제
ALTER TABLE tableName DROP (columnName) 
```

`ALTER` 명령어는 여러개의 column을 동시에 수정할 수 없다. 하나에 한개의 작업만 수행할 수 있다.

<br />

- 제약조건 추가하기

```tsx
// unique 옵션 추가
ALTER TABLE tableName ADD CONSTRAINT 제약조건이름 UNIQUE (columnName);

// PK키 설정 추가
ALTER TABLE tableName ADD CONSTRAINT 제약조건이름 PRIMARY KEY (columnName);

// 외래키 설정 추가
ALTER TABLE child_tableName ADD CONSTRAINT 제약조건이름
FOREIGN KEY (child_columnName) REFERENCES parent(parent_columnName);

// 외래키 삭제
ALTER TABLE 테이블이름 DROP PRIMARY KEY

// check 옵션 추가
ALTER TABLE products ADD CONSTRAINT 제약조건이름 CHECK (price > 0);
```

제약 조건에는 이름을 지정해주는 것이 일반적으로 좋은 관행이다.

<br />

## DROP / TRUNCATE

```tsx
// 테이블 삭제
DROP TABLE tableName 

// 칼럼값만 남기고 테이블의 모든 데이터 삭제
TRUNCATE TABLE tableName 
```

<br />

## RENAME

```tsx
RENAME origin_table_name TO change_table_name
```

테이블 이름 변경하기

<br />

## INSERT

```tsx
// 삽입 컬럼을 명시하지 않았을 경우 모든 컬럼을 삽입해야한다.
INSERT INTO tableName VALUES (data, data, data...); 

// 특정 데이터만 삽입하는 경우 삽입 컬럼을 명시해야한다.
INSERT INTO tableName (columnName, columnName) VALUES (data, data); 
```

<br />

## UPDATE

```tsx
// 특정 column 값 변경
UPDATE tableName SET columnName = data WHERE 조건 

// 두개 한번에 변경
UPDATE tableName SET columnName1 = data1, columnName2 = data2 WHERE 조건 
```

<br />

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

TRUNCATE- DDL, ROLLBACK 불가능, AUTO COMMIT, 테이블 최초 생성 시 용량만 남기고 모두반환

테이블을 최초 생성된 초기상태로 만듬

DELETE- DML, ROLLBACK 가능(로그 남음), 사용자 COMMIT, 용량반환하지 않음, 데이터만 삭제

<br />

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

<br />

- alias 사용

```tsx
SELECT tableA.name, tableA.age, tableB.job
FROM tableA, tableB

SELECT A.name, A.age, B.job
FROM tableA A, tableB B
```

이렇게 다중 테이블에서 SELECT할 경우 테이블 이름을 계속 쓰는건 불편하므로 `alias`로 줄여서 나타낼 수 있다.

<br />

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

<br />

## JOIN

- INNER JOIN

```tsx
SELECT A.name, A.age,  B.name

FROM tableA A, tableB B

WHERE A.name = B.name
```

<br />

- LEFT OUTER JOIN

```tsx
SELECT A.video, A.category, B.member
FROM neflix A
LEFT OUTER JOIN neflixCast B
ON A.video = B.video // 어떤 조건으로 JOIN할 건지 정의
```

**`LEFT OUTER JOIN`**은 왼쪽 테이블의 모든 레코드와 오른쪽 테이블의 일치하는 레코드를 가져오며, 오른쪽 테이블의 레코드가 일치하지 않는 경우에는 NULL 값을 가진 레코드가 생성된다.

```tsx
SELECT A.video, A.category, B.member
FROM neflix A
LEFT OUTER JOIN neflixCast B
ON A.video = B.video 
WHERE B.member IS NOT NULL 
```

이렇게 하면 INNER JOIN과 같은 출력값을 같게 된다.

<br />

- RIGHT OUTER JOIN

```tsx
SELECT A.video, A.category, B.member
FROM neflix A
RIGHT OUTER JOIN neflixCast B
ON A.video = B.video 
```

**`RIGHT OUTER JOIN`**은 왼쪽 테이블의 일치하는 레코드와 오른쪽 테이블의 모든 레코드를 가져오며, 왼쪽 테이블의 레코드가 일치하지 않는 경우에는 NULL 값을 가진 레코드가  생성된다.

<br />

- FULL OUTER JOIN

```tsx
SELECT A.video, A.category, B.member
FROM neflix A
FULL OUTER JOIN neflixCast B
ON A.video = B.video 
```

**`FULL OUTER JOIN`**은 양쪽 테이블의 모든 레코드를 가져오며, 양쪽 테이블 중 어느 한쪽이라도 일치하지 않는 경우에는 NULL 값을 가진 레코드가 생성된다.

<br />

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
```

<br />

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

<br />

## HAVING

```tsx
SELECT customer_id, COUNT(order_id) AS order_count
FROM orders
GROUP BY customer_id
HAVING order_count >= 10;
```

`**Having**`절은 `**WHERE**`절과 비슷하게 특정 조건을 만족하는 그룹만을 선택하지만, 

`**GROUP BY**`로  그룹화된 결과 중에서 특정 조건을 만족하는 그룹만을 선택할 수 있다.

<br />

## CASE WHEN

`**CASE WHEN**`을 사용하면 기존에 있는 열의 조건에 따른 새로운 값을 갖는 새로운 열이 만들어지게 된다.

```tsx
SELECT column1, column2, column3
CASE WHEN column1 = 1 THEN 'ONE' 
		 WHEN column2 = 2 THEN 'TWO' ELSE 'THREE'
```

`**ELSE**`를 빼고 쓰면 일치하지 않는 값들에 대해선 NULL이 된다.

<br />

```tsx
SELECT column1, column2, column3
CASE column1 WHEN 1 THEN 'ONE' 
						 WHEN 2 THEN 'TWO' ELSE 'THREE'
```
<br />

## 내장 함수

- 날짜 함수

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

<br />

- REPLACE

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

<br />

- SUBSTR

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

<br />

- UPPER / LOWER

```tsx
// 대문자 변경
SELECT UPPER("abcd") FROM DUAL // ABCD

// 소문자 변경
SELECT LOWER("ABCD") FROM DUAL // ABCD

SELECT * FROM member WHERE ID = UPPER("collection");
```

<br />

- 숫자 함수

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
<br />

- TOP

```tsx
SELECT TOP 3 columnName
FROM tableName
ORDER BY columnName DESC
```

columnName으로 정렬하고 상위 3개 출력

<br />

```tsx
SELECT TOP 3 WITH TIES columnName
FROM tableName
ORDER BY columnName DESC
```

만약 같은 값이 있다면 같이 출력

<br />

```tsx
SELECT TOP 50 PERCENT columnName
FROM tableName
ORDER BY columnName DESC
```

상위 50퍼센트 출력

<br />

- 집계 함수


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

<br />

- 단일행 NULL 관련 함수

```tsx
// ISNULL( A , B ) → A column의 값이 NULL이라면 B로 대체 (SQL)
SELECT column1, column2, ISNULL(column3, '값 없음') FROM tableName

// NVL(A , B) → A column의 값이 NULL이라면 B로 대체 (ORACLE)
SELECT column1, column2, NVL(column3, '값 없음') FROM tableName

// NULLIF(A, B) → A column의 값이 B와 같으면 NULL을, 같지 않으면 A column의 값을 return
SELECT column1, column2, NULLIF(column3, 1) FROM tableName

// NULL이 아닌 최초의 표현식 나타냄, 모든게 NULL이면 NULL return
SELECT column1, column2, COALESCE(column3, column4, column5) IS FROM tableName
```

<br />

## Transaction

Transaction은 데이터베이스의 논리적 연산단위로서 밀접히 관련되어 분리될 수 없는 한 개 이상의 데이터베이스 조작을 가리킨다.

- `**원자성**` - 트랙잭션에서 정의된 연산들은 모두 성공적으로 실행되던지 전혀 실행되지 않은 상태로 남아있어야 한다.
- `**일관성**` - 트랜잭션이 실행되기 전의 데이터베이스 내용이 잘못되어 있지 않다면 트랙잭션이 실행된 이ㅜㅎ에도 데이터베이스의 내용에 잘못이 있으면 안된다.
- `**고립성**` - 트랜잭션이 실행되는 도중에 다른 트랜잭션의 영향을 받아 잘못된 결과를 만들어서는 안된다
- `**지속성**` - 트랜잭션이 성공적으로 수행되면 그 트랜잭션이 갱신한 데이터베이스의 내용은 영구적으로 저장된다.

- `**Dirty Read**` - 수정되었지만 commit이 되지 않은 데이터를 읽는 것
- `**Non-Repeatbale Read**` - 조회 / 수정 / 조회 순으로 일어나면 두 조회의 결과물이 다를 수 있다. ( 조회된 레코드의 값이 변경 됨 )
- `**Phantom Read**` -  조회 / 삽입 or 삭제 / 조회 순으로 일어나서 두 조회의 결과물이 다를 수 있다. ( 새로운 값이 생겨나거나 없어짐 )

<br />

## 0 나누기

- A / 0 → 0
- 0 / A → ERROR
- 0 / NULL, NULL / 0, A / NULL, NULL / A → NULL

<br />

## null

- 모르는 값을 의미한다.
- 값의 부재를 의미한다.
- NULL과의 모든 비교는 Unknown을 반환한다.
- NULL과의 모든 연산은 NULL을 반환한다.

<br />

## ORACLE과 SQL

- orcle은 ‘’로 입력하면 null로 인식 / sql은 ‘’로 인식
- Oracle에서는 NULL을 가장 큰 값으로 간주한다.
- SQL에서는 NULL을 가장 작은 값으로 간주한다.

<br />