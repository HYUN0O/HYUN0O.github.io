---
categories:
  - Algorithm
tags:
  - Programmers
  - SQL
---
# [루시와 엘라 찾기 PGs#59046](https://programmers.co.kr/learn/courses/30/lessons/59046)

<details>
<summary><b>코드 보기</b></summary>
<div markdown="1">

```SQL
SELECT ANIMAL_ID, NAME, SEX_UPON_INTAKE
FROM ANIMAL_INS
WHERE NAME IN ("Lucy", "Ella", "Pickle", "Rogan", "Sabrina", "Mitty")
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

+ 동물 보호소에 들어온 동물 중 이름이 Lucy, Ella, Pickle, Rogan, Sabrina, Mitty인 동물의 아이디와 이름, 성별을 조회하는 SQL 문을 작성해주세요.
+ 결과는 아이디 순으로 조회해주세요.

## 🤟 구현 과정

1. 동물의 정보중 동물의 ID, 이름, 성별을 요구하므로, `SELECT ANIMAL_ID, NAME, SEX_UPON_INTAKE`를 사용하였다.
2. `ANIMAL_INS`라는 테이블에 모든 정보가 들어있으므로, `FROM ANIMAL_INS`를 사용하였다.
3. 동물의 이름이 6개 중 하나, 즉 Lucy, Ella, Pickle, Rogan, Sabrina, Mitty로 이루어진 라는 리스트 안에 있는 경우를 조회하면 되므로, `WHERE NAME IN ("Lucy", "Ella", "Pickle", "Rogan", "Sabrina", "Mitty")`를 사용하였다.
4. 동물의 ID를 오름차순으로 검색하므로 `ORDER BY ANIMAL_ID`를 사용하였다.

---
---

# [이름에 el이 들어가는 동물 찾기 PGs#59047](https://programmers.co.kr/learn/courses/30/lessons/59047)

<details>
<summary><b>코드 보기</b></summary>
<div markdown="1">

```SQL
SELECT ANIMAL_ID, NAME
FROM ANIMAL_INS
WHERE ANIMAL_TYPE = "Dog"
    AND NAME LIKE "%el%"
ORDER BY NAME
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

+ 이름에 EL이 들어가는 개의 아이디와 이름을 조회하는 SQL문을 작성해주세요.
+ 결과는 이름 순으로 조회해주세요.
+ 이름의 대소문자는 구분하지 않습니다.

## 🤟 구현 과정

1. 동물의 정보중 동물의 ID, 이름을 요구하므로, `SELECT ANIMAL_ID, NAME`를 사용하였다.
2. `ANIMAL_INS`라는 테이블에 모든 정보가 들어있으므로, `FROM ANIMAL_INS`를 사용하였다.
3. 동물은 강아지이므로, `WHERE ANIMAL_TYPE = "Dog"`을 사용하였다
	+ 또한, 이름에 'EL'을 포함하여야 하므로, `AND NAME LIKE "%el%"`을 `AND`로 묶었다.
4. 동물의 이름 순으로 검색하므로 `ORDER BY NAME`을 사용하였다.

---
---

# [중성화 여부 파악하기 PGs#59409](https://programmers.co.kr/learn/courses/30/lessons/59409)

<details>
<summary><b>코드 보기</b></summary>
<div markdown="1">

```SQL
SELECT ANIMAL_ID, NAME, IF (SEX_UPON_INTAKE NOT LIKE "%Intact%", "O", "X") AS 중성화
FROM ANIMAL_INS
// ORDER BY ANIMAL_ID
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

+ 동물의 아이디와 이름, 중성화 여부를 아이디 순으로 조회하는 SQL문을 작성해주세요.
+ 중성화가 되어있다면 'O', 아니라면 'X'라고 표시해주세요.
+ 중성화된 동물은 `SEX_UPON_INTAKE` 컬럼에 'Neutered' 또는 'Spayed'라는 단어가 들어있습니다.

## 🤟 구현 과정

1. 동물의 정보중 동물의 ID, 이름, 중성화 여부를 요구하므로, `SELECT ANIMAL_ID, NAME, 중성화`를 사용하였다.
	+ `SEX_UPON_INTAKE` 컬럼에 'Intact'를 포함하고 있으면 중성회 되지 않은 경우와 같다.
	+ `중성화`는 `IF (SEX_UPON_INTAKE NOT LIKE "%Intact%", "O", "X") AS 중성화`로 정의하였다.
2. `ANIMAL_INS`라는 테이블에 모든 정보가 들어있으므로, `FROM ANIMAL_INS`를 사용하였다.
3. 어차피 동물의 ID를 가장 앞에서 검색하므로, 자동으로 ID기준 오름차 순으로 정렬된다. `ORDER BY ANIMAL_ID`를 사용하여도 무방하다.

---
---

# [오랜 기간 보호한 동물 (2) PGs#59411](https://programmers.co.kr/learn/courses/30/lessons/59411)

<details>
<summary><b>코드 보기</b></summary>
<div markdown="1">

```SQL
SELECT I.ANIMAL_ID, I.NAME
FROM ANIMAL_INS AS I JOIN
    ANIMAL_OUTS AS O ON
    I.ANIMAL_ID = O.ANIMAL_ID
ORDER BY O.DATETIME-I.DATETIME DESC
LIMIT 2
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

+ 입양을 간 동물 중, 보호 기간이 가장 길었던 동물 두 마리의 아이디와 이름을 조회하는 SQL문을 작성해주세요. 
+ 결과는 보호 기간이 긴 순으로 조회해야 합니다.

## 🤟 구현 과정

1. 동물의 정보중 동물의 ID, 이름을 요구하므로, `SELECT I.ANIMAL_ID, I.NAME`를 사용하였다. `SELECT O.ANIMAL_ID, O.NAME`를 사용해도, 섞어서 써도 문제 없다.
2.	`ANIMAL_INS`에 `ANIMAL_OUTS`를 `ANIMAL_ID`를 이용해 `JOIN`시켜 아래의 정보들을 attribute로 갖는 table을 만든다.
	+ 자주 두 테이블을 호출해야하므로, 코드의 가독성을 위해 `ANIMAL_INS AS I, ANIMAL_OUTS AS O`로 수정해주었다.

| ANIMAL_ID | ANIMAL_TYPE | DATETIME | INTAKE_CONDITION | NAME | SEX_UPON_INTAKE | ANIMAL_ID | ANIMAL_TYPE | DATETIME | NAME | SEX_UPON_OUTCOME |
|-----------|-------------|----------|------------------|------|-----------------|-----------|-------------|----------|------|------------------|

3. 입양 나간 동물 중, 가장 오래 보호한 동물을 골라야 하므로, 입양일에서 보호시작일을 뺀 값을 기준으로 내림차순으로 정렬하기 위해 `ORDER BY O.DATETIME-I.DATETIME DESC`를 사용하였다.
4. 2마리만 출력하므로, `LIMIT 2`를 사용하였다.

---
---


# [DATETIME 에서 DATE로 형 변환 PGs#59414](https://programmers.co.kr/learn/courses/30/lessons/59414)

<details>
<summary><b>코드 보기</b></summary>
<div markdown="1">

```SQL
SELECT ANIMAL_ID, NAME, DATE_FORMAT(DATETIME, "%Y-%m-%d") AS 날짜
FROM ANIMAL_INS
// ORDER BY ANIMAL_ID
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

+ 각 동물의 아이디와 이름, 들어온 날짜를 조회하는 SQL문을 작성해주세요. 
+ 결과는 아이디 순으로 조회해야 합니다.
+ 날짜는 시각(시-분-초)을 제외한 날짜(년-월-일)만 보여주세요.

## 🤟 구현 과정

1. 동물의 정보중 동물의 ID, 이름, 날짜 여부를 요구하므로, `SELECT ANIMAL_ID, NAME, 날짜`를 사용하였다.
	+ `날짜`는 `DATE_FORMAT`함수를 이용하여`DATE_FORMAT(DATETIME, "%Y-%m-%d") AS 날짜`로 정의하였다. 형식은 [이 곳](https://www.w3schools.com/sql/func_mysql_date_format.asp)에 자세히 나와있다.
2. `ANIMAL_INS`라는 테이블에 모든 정보가 들어있으므로, `FROM ANIMAL_INS`를 사용하였다.
3. 어차피 동물의 ID를 가장 앞에서 검색하므로, 자동으로 ID기준 오름차 순으로 정렬된다. `ORDER BY ANIMAL_ID`를 사용하여도 무방하다.

---
---


