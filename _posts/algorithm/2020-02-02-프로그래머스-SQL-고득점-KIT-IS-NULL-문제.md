---
categories:
  - Algorithm
tags:
  - Programmers
  - SQL
---
# [이름이 없는 동물의 아이디 PGs#59039](https://programmers.co.kr/learn/courses/30/lessons/59039)

<details>
<summary><b>코드 보기</b></summary>
<div markdown="1">

```SQL
SELECT ANIMAL_ID
FROM ANIMAL_INS
WHERE NAME IS NULL
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

## 🤞 출력 형식

+ 동물 보호소에 들어온 동물 중, 이름이 없는 채로 들어온 동물의 ID를 조회하는 SQL 문을 작성해주세요.
+ ID는 오름차순 정렬되어야 합니다.

## 🤟 구현 과정

1. 동물의 정보중 동물의 ID를 요구하므로, `SELECT ANIMAL_ID`를 사용하였다.
2. `ANIMAL_INS`라는 테이블에 모든 정보가 들어있으므로, `FROM ANIMAL_INS`를 사용하였다.
3. 동물의 이름이 없는, 즉 `NULL`인 동물을 검색하므로, `WHERE NAME IS NULL`을 사용하였다.
4. 어차피 동물의 ID만 검색하므로, 자동으로 ID 오름차순으로 정렬된다. `ORDER BY ANIMAL_ID`를 사용하여도 무방하다.

---
---

# [이름이 있는 동물의 아이디 PGs#59407](https://programmers.co.kr/learn/courses/30/lessons/59407)

<details>
<summary><b>코드 보기</b></summary>
<div markdown="1">

```SQL
SELECT ANIMAL_ID
FROM ANIMAL_INS
WHERE NAME IS NOT NULL
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

## 🤞 출력 형식

+ 동물 보호소에 들어온 동물 중, 이름이 있는 동물의 ID를 조회하는 SQL 문을 작성해주세요.
+ ID는 오름차순 정렬되어야 합니다.

## 🤟 구현 과정

1. 동물의 정보중 동물의 ID를 요구하므로, `SELECT ANIMAL_ID`를 사용하였다.
2. `ANIMAL_INS`라는 테이블에 모든 정보가 들어있으므로, `FROM ANIMAL_INS`를 사용하였다.
3. 동물의 이름이 있는, 즉 `NULL`이 아닌 동물을 검색하므로, `WHERE NAME IS NOT NULL`을 사용하였다.
4. 어차피 동물의 ID만 검색하므로, 자동으로 ID 오름차순으로 정렬된다. `ORDER BY ANIMAL_ID`를 사용하여도 무방하다.

---
---

# [NULL 처리하기 PGs#59410](https://programmers.co.kr/learn/courses/30/lessons/59410)

<details>
<summary><b>코드 보기</b></summary>
<div markdown="1">

```SQL
SELECT ANIMAL_TYPE, IFNULL(NAME,"No name"), SEX_UPON_INTAKE
FROM ANIMAL_INS
ORDER BY ANIMAL_ID
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

## 🤞 출력 형식

+ 동물의 생물 종, 이름, 성별 및 중성화 여부를 아이디 순으로 조회하는 SQL문을 작성해주세요.
+ 이름이 없는 동물의 이름은 No name으로 표시해 주세요.

## 🤟 구현 과정

1. 동물의 정보중 동물의 종, 이름, 성별을 요구하므로, `SELECT ANIMAL_TYPE, NAME, SEX_UPON_INTAKE`를 사용하였다.
2.  그 중,  `NAME`가 `NULL`이면 No name으로 처리해주어야 하므로 `NAME`을 `IFNULL(NAME,"No name")`으로 변경하였다.
3. `ANIMAL_INS`라는 테이블에 모든 정보가 들어있으므로, `FROM ANIMAL_INS`를 사용하였다.
4. 동물의 ID를 오름차순으로 검색하므로 `ORDER BY ANIMAL_ID`를 사용하였다.

## 🍀 Pain Points

1. ISNULL(A,B)
	- A가 NULL일 경우, B를 Return함.

