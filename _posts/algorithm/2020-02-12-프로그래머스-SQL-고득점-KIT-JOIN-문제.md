---
categories:
  - Algorithm
tags:
  - Programmers
  - SQL
---
# [없어진 기록 찾기 PGs#59042](https://programmers.co.kr/learn/courses/30/lessons/59042)

<details>
<summary><b>코드 보기</b></summary>
<div markdown="1">

```SQL
SELECT ANIMAL_ID, NAME
FROM ANIMAL_OUTS
WHERE ANIMAL_ID NOT IN (SELECT ANIMAL_ID
                        FROM ANIMAL_INS);
                        
// JOIN 미사용↑ ↓JOIN 사용

SELECT O.ANIMAL_ID, O.NAME
FROM ANIMAL_INS AS I
    RIGHT OUTER JOIN ANIMAL_OUTS AS O
    ON I.ANIMAL_ID = O.ANIMAL_ID 
WHERE SEX_UPON_INTAKE IS NULL AND SEX_UPON_OUTCOME IS NOT NULL
```

</div>
</details>


## ☝ 입력 형식

+ `ANIMAL_INS` 테이블은 동물 보호소에 들어온 동물의 정보를 담은 테이블입니다.
+ `ANIMAL_INS` 테이블 구조는 다음과 같으며, `ANIMAL_ID`, `ANIMAL_TYPE`, `DATETIME`, `INTAKE_CONDITION`, `NAME`, `SEX_UPON_INTAKE`는 각각 동물의 아이디, 생물 종, 보호 시작일, 보호 시작 시 상태, 이름, 성별 및 중성화 여부를 나타냅니다.

| **NAME**         | **TYPE**   | **NULLABLE** |
|------------------|------------|--------------|
| ANIMAL_ID        | VARCHAR(N) | FALSE        |
| ANIMAL_TYPE      | VARCHAR(N) | FALSE        |
| DATETIME         | DATETIME   | FALSE        |
| INTAKE_CONDITION | VARCHAR(N) | FALSE        |
| NAME             | VARCHAR(N) | TRUE         |
| SEX_UPON_INTAKE  | VARCHAR(N) | FALSE        |

+ `ANIMAL_OUTS` 테이블은 동물 보호소에서 입양 보낸 동물의 정보를 담은 테이블입니다.
+ `ANIMAL_OUTS` 테이블 구조는 다음과 같으며, `ANIMAL_ID`, `ANIMAL_TYPE`, `DATETIME`, `NAME`, `SEX_UPON_OUTCOME`는 각각 동물의 아이디, 생물 종, 입양일, 이름, 성별 및 중성화 여부를 나타냅니다.

| **NAME**         | **TYPE**   | **NULLABLE** |
|------------------|------------|--------------|
| ANIMAL_ID        | VARCHAR(N) | FALSE        |
| ANIMAL_TYPE      | VARCHAR(N) | FALSE        |
| DATETIME         | DATETIME   | FALSE        |
| NAME             | VARCHAR(N) | TRUE         |
| SEX_UPON_OUTCOME | VARCHAR(N) | FALSE        |

## 🤞 출력 형식

+ 입양을 간 기록은 있는데, 보호소에 들어온 기록이 없는 동물의 ID와 이름을 ID 순으로 조회하는 SQL문을 작성해주세요.

## 🤟 구현 과정

### JOIN 미사용
1. 동물의 정보중 동물의 ID, 이름을 요구하므로, `SELECT ANIMAL_ID, NAME`를 사용하였다.
2. `ANIMAL_OUTS`라는 테이블에 있는 정보를 비교하고자 하므로, `FROM ANIMAL_OUTS`를 사용하였다.
3. `ANIMAL_OUTS`에 ID가 있으면서 `ANIMAL_INS`에는 ID가 없는 경우를 검색하고 싶으므로, Subquery를 이용하여 `WHERE ANIMAL_ID NOT IN (SELECT ANIMAL_ID FROM ANIMAL_INS)`를 사용해주었다.

### JOIN 사용
1. 동물의 정보중 동물의 ID, 이름을 요구하므로, `SELECT O.ANIMAL_ID, O.NAME`를 사용하였다. `ANIMAL_INS`에 없는 데이터를 사용하므로, `SELECT I.ANIMAL_ID, I.NAME`를 사용하거나, 섞어서 쓰면 안된다.
2.	`ANIMAL_OUTS`에 `ANIMAL_INS`를 `ANIMAL_ID`를 이용해 `RIGHT OUTER JOIN`시켜 아래의 정보들을 attribute로 갖는 table을 만든다.
	+ 자주 두 테이블을 호출해야하므로, 코드의 가독성을 위해 `ANIMAL_INS AS I, ANIMAL_OUTS AS O`로 수정해주었다.

| ANIMAL_ID | ANIMAL_TYPE | DATETIME | INTAKE_CONDITION | NAME | SEX_UPON_INTAKE | ANIMAL_ID | ANIMAL_TYPE | DATETIME | NAME | SEX_UPON_OUTCOME |
|-----------|-------------|----------|------------------|------|-----------------|-----------|-------------|----------|------|------------------|

3. 보호소로 들어온 기록은 없으며, 나간 기록은 없다면 `SEX_UPON_INTAKE`는 `IS NULL`일 것이고, `SEX_UPON_OUTCOME`은 `IS NOT NULL`이어야 한다.
그러므로, `WHERE SEX_UPON_INTAKE IS NULL AND SEX_UPON_OUTCOME IS NOT NULL`을 사용하였다.

## 🍀 Pain Points

JOIN 문제라서 나름대로 JOIN을 사용해서 풀어보고자 했는데, 오히려 끔찍한 SQL문이 탄생했다.

내가 SQL을 잘 못 다뤄서 그런건가?

 이 문제는 JOIN을 사용하지 않은 SQL문이 훨신 깔끔하고, 직관적으로 느껴진다.

---
---

# [있었는데요 없었습니다 PGs#59043](https://programmers.co.kr/learn/courses/30/lessons/59043)

<details>
<summary><b>코드 보기</b></summary>
<div markdown="1">

```SQL
SELECT I.ANIMAL_ID, I.NAME
FROM ANIMAL_INS AS I, ANIMAL_OUTS AS O
WHERE I.DATETIME > O.DATETIME AND I.ANIMAL_ID = O.ANIMAL_ID
ORDER BY I.DATETIME
                      
// JOIN 미사용↑ ↓JOIN 사용

SELECT I.ANIMAL_ID, I.NAME
FROM ANIMAL_INS AS I
    JOIN ANIMAL_OUTS AS O
    ON I.ANIMAL_ID = O.ANIMAL_ID 
WHERE I.DATETIME > O.DATETIME
ORDER BY I.DATETIME
```

</div>
</details>


## ☝ 입력 형식

+ `ANIMAL_INS` 테이블은 동물 보호소에 들어온 동물의 정보를 담은 테이블입니다.
+ `ANIMAL_INS` 테이블 구조는 다음과 같으며, `ANIMAL_ID`, `ANIMAL_TYPE`, `DATETIME`, `INTAKE_CONDITION`, `NAME`, `SEX_UPON_INTAKE`는 각각 동물의 아이디, 생물 종, 보호 시작일, 보호 시작 시 상태, 이름, 성별 및 중성화 여부를 나타냅니다.

| **NAME**         | **TYPE**   | **NULLABLE** |
|------------------|------------|--------------|
| ANIMAL_ID        | VARCHAR(N) | FALSE        |
| ANIMAL_TYPE      | VARCHAR(N) | FALSE        |
| DATETIME         | DATETIME   | FALSE        |
| INTAKE_CONDITION | VARCHAR(N) | FALSE        |
| NAME             | VARCHAR(N) | TRUE         |
| SEX_UPON_INTAKE  | VARCHAR(N) | FALSE        |

+ `ANIMAL_OUTS` 테이블은 동물 보호소에서 입양 보낸 동물의 정보를 담은 테이블입니다.
+ `ANIMAL_OUTS` 테이블 구조는 다음과 같으며, `ANIMAL_ID`, `ANIMAL_TYPE`, `DATETIME`, `NAME`, `SEX_UPON_OUTCOME`는 각각 동물의 아이디, 생물 종, 입양일, 이름, 성별 및 중성화 여부를 나타냅니다.

| **NAME**         | **TYPE**   | **NULLABLE** |
|------------------|------------|--------------|
| ANIMAL_ID        | VARCHAR(N) | FALSE        |
| ANIMAL_TYPE      | VARCHAR(N) | FALSE        |
| DATETIME         | DATETIME   | FALSE        |
| NAME             | VARCHAR(N) | TRUE         |
| SEX_UPON_OUTCOME | VARCHAR(N) | FALSE        |

## 🤞 출력 형식

+ 보호 시작일보다 입양일이 더 빠른 동물의 아이디와 이름을 조회하는 SQL문을 작성해주세요.
+ 이때 결과는 보호 시작일이 빠른 순으로 조회해야합니다.

## 🤟 구현 과정

### JOIN 미사용
1. 동물의 정보중 동물의 ID, 이름을 요구하므로, `SELECT I.ANIMAL_ID, I.NAME`를 사용하였다. `SELECT O.ANIMAL_ID, O.NAME`를 사용해도, 섞어서 써도 문제 없다.
2. `ANIMAL_INS`, `ANIMAL_OUTS` 두 테이블에 있는 정보를 모두 사용해야 하므로, `ANIMAL_INS, ANIMAL_OUTS`를 사용하였다.
	+ 자주 두 테이블을 호출해야하므로, 코드의 가독성을 위해 `ANIMAL_INS AS I, ANIMAL_OUTS AS O`로 수정해주었다.
3. 보호 시작일보다 입양일이 더 빠른 경우를 찾고 싶으므로,`ANIMAL_OUTS`의 `DATETIME`<sub>입양일</sub>이 `ANIMAL_INS`의 `DATETIME`<sub>보호 시작일</sub>보다 더 작야아 한다.
그러므로, `WHERE I.DATETIME > O.DATETIME AND I.ANIMAL_ID = O.ANIMAL_ID`을 사용하였다.
4. 동물의 보호 시작일이 빠른 순으로 검색하므로 `ORDER BY I.DATETIME`를 사용하였다.

### JOIN 사용
1. 동물의 정보중 동물의 ID, 이름을 요구하므로, `SELECT I.ANIMAL_ID, I.NAME`를 사용하였다. `SELECT O.ANIMAL_ID, O.NAME`를 사용해도, 섞어서 써도 문제 없다.
2.	`ANIMAL_INS`에 `ANIMAL_OUTS`를 `ANIMAL_ID`를 이용해 `JOIN`시켜 아래의 정보들을 attribute로 갖는 table을 만든다.
	+ 자주 두 테이블을 호출해야하므로, 코드의 가독성을 위해 `ANIMAL_INS AS I, ANIMAL_OUTS AS O`로 수정해주었다.

| ANIMAL_ID | ANIMAL_TYPE | DATETIME | INTAKE_CONDITION | NAME | SEX_UPON_INTAKE | ANIMAL_ID | ANIMAL_TYPE | DATETIME | NAME | SEX_UPON_OUTCOME |
|-----------|-------------|----------|------------------|------|-----------------|-----------|-------------|----------|------|------------------|

3. 보호 시작일보다 입양일이 더 빠른 경우를 찾고 싶으므로,`ANIMAL_OUTS`의  `DATETIME`<sub>입양일</sub>이 `ANIMAL_INS`의 `DATETIME`<sub>보호 시작일</sub>보다 더 작야아 한다.
그러므로, `WHERE I.DATETIME > O.DATETIME`을 사용하였다.
4. 동물의 보호 시작일이 빠른 순으로 검색하므로 `ORDER BY I.DATETIME`를 사용하였다.


---
---

# [오랜 기간 보호한 동물 (1) PGs#59044](https://programmers.co.kr/learn/courses/30/lessons/59044)

<details>
<summary><b>코드 보기</b></summary>
<div markdown="1">

```SQL
SELECT I.NAME, I.DATETIME
FROM ANIMAL_INS AS I
    LEFT OUTER JOIN ANIMAL_OUTS AS O
    ON I.ANIMAL_ID = O.ANIMAL_ID
WHERE SEX_UPON_OUTCOME IS NULL
ORDER BY I.DATETIME
LIMIT 3
```

</div>
</details>


## ☝ 입력 형식

+ `ANIMAL_INS` 테이블은 동물 보호소에 들어온 동물의 정보를 담은 테이블입니다.
+ `ANIMAL_INS` 테이블 구조는 다음과 같으며, `ANIMAL_ID`, `ANIMAL_TYPE`, `DATETIME`, `INTAKE_CONDITION`, `NAME`, `SEX_UPON_INTAKE`는 각각 동물의 아이디, 생물 종, 보호 시작일, 보호 시작 시 상태, 이름, 성별 및 중성화 여부를 나타냅니다.

| **NAME**         | **TYPE**   | **NULLABLE** |
|------------------|------------|--------------|
| ANIMAL_ID        | VARCHAR(N) | FALSE        |
| ANIMAL_TYPE      | VARCHAR(N) | FALSE        |
| DATETIME         | DATETIME   | FALSE        |
| INTAKE_CONDITION | VARCHAR(N) | FALSE        |
| NAME             | VARCHAR(N) | TRUE         |
| SEX_UPON_INTAKE  | VARCHAR(N) | FALSE        |

+ `ANIMAL_OUTS` 테이블은 동물 보호소에서 입양 보낸 동물의 정보를 담은 테이블입니다.
+ `ANIMAL_OUTS` 테이블 구조는 다음과 같으며, `ANIMAL_ID`, `ANIMAL_TYPE`, `DATETIME`, `NAME`, `SEX_UPON_OUTCOME`는 각각 동물의 아이디, 생물 종, 입양일, 이름, 성별 및 중성화 여부를 나타냅니다.

| **NAME**         | **TYPE**   | **NULLABLE** |
|------------------|------------|--------------|
| ANIMAL_ID        | VARCHAR(N) | FALSE        |
| ANIMAL_TYPE      | VARCHAR(N) | FALSE        |
| DATETIME         | DATETIME   | FALSE        |
| NAME             | VARCHAR(N) | TRUE         |
| SEX_UPON_OUTCOME | VARCHAR(N) | FALSE        |

## 🤞 출력 형식

+ 아직 입양을 못 간 동물 중, 가장 오래 보호소에 있었던 동물 3마리의 이름과 보호 시작일을 조회하는 SQL문을 작성해주세요.
+ 결과는 보호 시작일 순으로 조회해야 합니다.

## 🤟 구현 과정

1. 동물의 정보중 동물의 ID, 이름을 요구하므로, `SELECT I.ANIMAL_ID, I.NAME`를 사용하였다. `ANIMAL_OUTS`에 없는 데이터를 사용하므로, `SELECT O.ANIMAL_ID, O.NAME`를 사용하거나 섞어서 쓰면 안된다. 
2.	`ANIMAL_INS`에 `ANIMAL_OUTS`를 `ANIMAL_ID`를 이용해 `JOIN`시켜 아래의 정보들을 attribute로 갖는 table을 만든다.
	+ 자주 두 테이블을 호출해야하므로, 코드의 가독성을 위해 `ANIMAL_INS AS I, ANIMAL_OUTS AS O`로 수정해주었다.

| ANIMAL_ID | ANIMAL_TYPE | DATETIME | INTAKE_CONDITION | NAME | SEX_UPON_INTAKE | ANIMAL_ID | ANIMAL_TYPE | DATETIME | NAME | SEX_UPON_OUTCOME |
|-----------|-------------|----------|------------------|------|-----------------|-----------|-------------|----------|------|------------------|

3. 아직 입양을 못 간 동물들 중에서 검색해야하므로, `WHERE SEX_UPON_OUTCOME IS NULL`을 사용하였다.
4. 동물의 보호 시작일이 빠를 수록 오래 보호한 동물이므로, `ORDER BY I.DATETIME`를 사용하였다.
5. 동물 3마리만 검색하므로, `LIMIT 3`를 사용하였다.

---
---

# [보호소에서 중성화 한 동물 PGs#59045](https://programmers.co.kr/learn/courses/30/lessons/59045)

<details>
<summary><b>코드 보기</b></summary>
<div markdown="1">

```SQL
SELECT I.ANIMAL_ID, I.ANIMAL_TYPE, I.NAME
FROM ANIMAL_INS AS I
    JOIN ANIMAL_OUTS AS O
    ON I.ANIMAL_ID = O.ANIMAL_ID
WHERE I.SEX_UPON_INTAKE LIKE "Intact %" 
    AND O.SEX_UPON_OUTCOME NOT LIKE "Intact %" 
ORDER BY I.ANIMAL_ID
```

</div>
</details>


## ☝ 입력 형식

+ `ANIMAL_INS` 테이블은 동물 보호소에 들어온 동물의 정보를 담은 테이블입니다.
+ `ANIMAL_INS` 테이블 구조는 다음과 같으며, `ANIMAL_ID`, `ANIMAL_TYPE`, `DATETIME`, `INTAKE_CONDITION`, `NAME`, `SEX_UPON_INTAKE`는 각각 동물의 아이디, 생물 종, 보호 시작일, 보호 시작 시 상태, 이름, 성별 및 중성화 여부를 나타냅니다.

| **NAME**         | **TYPE**   | **NULLABLE** |
|------------------|------------|--------------|
| ANIMAL_ID        | VARCHAR(N) | FALSE        |
| ANIMAL_TYPE      | VARCHAR(N) | FALSE        |
| DATETIME         | DATETIME   | FALSE        |
| INTAKE_CONDITION | VARCHAR(N) | FALSE        |
| NAME             | VARCHAR(N) | TRUE         |
| SEX_UPON_INTAKE  | VARCHAR(N) | FALSE        |

+ `ANIMAL_OUTS` 테이블은 동물 보호소에서 입양 보낸 동물의 정보를 담은 테이블입니다.
+ `ANIMAL_OUTS` 테이블 구조는 다음과 같으며, `ANIMAL_ID`, `ANIMAL_TYPE`, `DATETIME`, `NAME`, `SEX_UPON_OUTCOME`는 각각 동물의 아이디, 생물 종, 입양일, 이름, 성별 및 중성화 여부를 나타냅니다.

| **NAME**         | **TYPE**   | **NULLABLE** |
|------------------|------------|--------------|
| ANIMAL_ID        | VARCHAR(N) | FALSE        |
| ANIMAL_TYPE      | VARCHAR(N) | FALSE        |
| DATETIME         | DATETIME   | FALSE        |
| NAME             | VARCHAR(N) | TRUE         |
| SEX_UPON_OUTCOME | VARCHAR(N) | FALSE        |

## 🤞 출력 형식

+ 보호소에 들어올 당시에는 중성화되지 않았지만, 보호소를 나갈 당시에는 중성화된 동물의 아이디와 생물 종, 이름을 조회하는 아이디 순으로 조회하는 SQL 문을 작성해주세요.
+ 중성화를 거치지 않은 동물은 `성별 및 중성화 여부`에 Intact, 중성화를 거친 동물은 `Spayed` 또는 `Neutered`라고 표시되어있습니다.

## 🤟 구현 과정

1. 동물의 정보중 동물의 ID, 이름을 요구하므로, `SELECT I.ANIMAL_ID, I.ANIMAL_TYPE, I.NAME`를 사용하였다. `SELECT O.ANIMAL_ID, O.ANIMAL_TYPE, O.NAME`를 사용하거나 섞어서 써도 상관없다.
2.	`ANIMAL_INS`에 `ANIMAL_OUTS`를 `ANIMAL_ID`를 이용해 `JOIN`시켜 아래의 정보들을 attribute로 갖는 table을 만든다.
	+ 자주 두 테이블을 호출해야하므로, 코드의 가독성을 위해 `ANIMAL_INS AS I, ANIMAL_OUTS AS O`로 수정해주었다.

| ANIMAL_ID | ANIMAL_TYPE | DATETIME | INTAKE_CONDITION | NAME | SEX_UPON_INTAKE | ANIMAL_ID | ANIMAL_TYPE | DATETIME | NAME | SEX_UPON_OUTCOME |
|-----------|-------------|----------|------------------|------|-----------------|-----------|-------------|----------|------|------------------|

3.
	1. 보호 시작일에는 중성화 되지 않았어야 하므로, `WHERE I.SEX_UPON_INTAKE LIKE "Intact %" `를 사용하였다.
	2. 입양일에는 중성화가 되어있어야 하므로, `    AND O.SEX_UPON_OUTCOME NOT LIKE "Intact %" `를 이어서 사용하였다.
4. 동물의 ID순으로 조회를 해야하므로, `ORDER BY I.ANIMAL_ID`를 사용하였다.

## 🍀 Pain Points

### 1. LIKE
`=`을 사용하면 뒤에 오는 문장과 데이터가 일치하는 데이터만 검색하지만, `LIKE`를 사용하면, 뒤에 붙는 오는 문장을 **포함**하는 데이터를 검색할 수 있다.


