---
categories:
  - Algorithm
tags:
  - Programmers
  - SQL
---
# [최댓값 구하기 PGs#59415](https://programmers.co.kr/learn/courses/30/lessons/59415)

<details>
<summary><b>코드 보기</b></summary>
<div markdown="1">

```SQL
SELECT DISTINCT DATETIME
FROM ANIMAL_INS
WHERE DATETIME >= ALL(SELECT DATETIME FROM ANIMAL_INS)
```

</div>
</details>


## ☝ 입력 형식

+ `ANIMAL_INS` 테이블은 동물 보호소에 들어온 동물의 정보를 담은 테이블입니다.
+ `ANIMAL_INS` 테이블 구조는 다음과 같으며, `ANIMAL_ID`, `ANIMAL_TYPE`, `DATETIME`, `INTAKE_CONDITION`, `NAME`, `SEX_UPON_INTAKE`는 각각 동물의 아이디, 생물 종, 보호 시작일, 보호 시작 시 상태, 이름, 성별 및 중성화 여부를 나타냅니다.

|**NAME**|**TYPE**|**NULLABLE**|
|--|--|--|
|ANIMAL_ID|VARCHAR(N)|FALSE
|ANIMAL_TYPE|VARCHAR(N)|FALSE
|DATETIME|DATETIME|FALSE
|INTAKE_CONDITION|VARCHAR(N)|FALSE|
|NAME|VARCHAR(N)|TRUE|
|SEX_UPON_INTAKE|VARCHAR(N)|FALSE|

## 🤞 출력 형식

+ 가장 최근에 들어온 동물은 언제 들어왔는지 조회하는 SQL 문을 작성해주세요.

## 🤟 구현 과정

1. 동물의 정보중 보호 시작일만 요구하므로, `SELECT DATETIME`를 사용하였다.

2. 그러나, 같은 시각에 보호가 시작된 동물이 있을 수 있으므로,  `SELECT DISTINCT DATETIME`으로 변경하였다.

3. `ANIMAL_INS`라는 테이블에 모든 정보가 들어있으므로, `FROM ANIMAL_INS`를 사용하였다.

4. `DATETIME`이 가장 큰 경우를 찾는 것이므로, 다른 모든 `DATETIME`보다 크거나 같아야 한다. 그러므로 subquery를 이용하여`WHERE DATETIME >= ALL(SELECT DATETIME FROM ANIMAL_INS)`을 사용하였다.

---
---

# [최솟값 구하기 PGs#59038](https://programmers.co.kr/learn/courses/30/lessons/59038)

<details>
<summary><b>코드 보기</b></summary>
<div markdown="1">

```SQL
SELECT DATETIME
FROM ANIMAL_INS
ORDER BY DATETIME
LIMIT 1
```

</div>
</details>


## ☝ 입력 형식

+ `ANIMAL_INS` 테이블은 동물 보호소에 들어온 동물의 정보를 담은 테이블입니다.
+ `ANIMAL_INS` 테이블 구조는 다음과 같으며, `ANIMAL_ID`, `ANIMAL_TYPE`, `DATETIME`, `INTAKE_CONDITION`, `NAME`, `SEX_UPON_INTAKE`는 각각 동물의 아이디, 생물 종, 보호 시작일, 보호 시작 시 상태, 이름, 성별 및 중성화 여부를 나타냅니다.

|**NAME**|**TYPE**|**NULLABLE**|
|--|--|--|
|ANIMAL_ID|VARCHAR(N)|FALSE
|ANIMAL_TYPE|VARCHAR(N)|FALSE
|DATETIME|DATETIME|FALSE
|INTAKE_CONDITION|VARCHAR(N)|FALSE|
|NAME|VARCHAR(N)|TRUE|
|SEX_UPON_INTAKE|VARCHAR(N)|FALSE|

## 🤞 출력 형식

+ 동물 보호소에 가장 먼저 들어온 동물은 언제 들어왔는지 조회하는 SQL 문을 작성해주세요.

## 🤟 구현 과정

1. 동물의 정보중 보호 시작일만 요구하므로, `SELECT DATETIME`를 사용하였다.

2. `ANIMAL_INS`라는 테이블에 모든 정보가 들어있으므로, `FROM ANIMAL_INS`를 사용하였다.

최댓값 구하기와는 다른 방법으로 문제를 해결하고 싶어서 다른 로직으로 구현하였다.  `DATETIME`으로 정렬한 후, 가장 큰 한 가지만 출력하기로 했다.

3. `DATETIME`을 오름차순으로 정렬하기 위해, `ORDER BY DATETIME`을 사용하였다.

4. `DATETIME`이 가장 작은 한 가지만 출력하기 위해, `LIMIT 1`을 사용하였다.

---
---

# [동물 수 구하기 PGs#59406](https://programmers.co.kr/learn/courses/30/lessons/59406)

<details>
<summary><b>코드 보기</b></summary>
<div markdown="1">

```SQL
SELECT COUNT(*)
FROM ANIMAL_INS
```

</div>
</details>


## ☝ 입력 형식

+ `ANIMAL_INS` 테이블은 동물 보호소에 들어온 동물의 정보를 담은 테이블입니다.
+ `ANIMAL_INS` 테이블 구조는 다음과 같으며, `ANIMAL_ID`, `ANIMAL_TYPE`, `DATETIME`, `INTAKE_CONDITION`, `NAME`, `SEX_UPON_INTAKE`는 각각 동물의 아이디, 생물 종, 보호 시작일, 보호 시작 시 상태, 이름, 성별 및 중성화 여부를 나타냅니다.

|**NAME**|**TYPE**|**NULLABLE**|
|--|--|--|
|ANIMAL_ID|VARCHAR(N)|FALSE
|ANIMAL_TYPE|VARCHAR(N)|FALSE
|DATETIME|DATETIME|FALSE
|INTAKE_CONDITION|VARCHAR(N)|FALSE|
|NAME|VARCHAR(N)|TRUE|
|SEX_UPON_INTAKE|VARCHAR(N)|FALSE|

## 🤞 출력 형식

+ 동물 보호소에 동물이 몇 마리 들어왔는지 조회하는 SQL 문을 작성해주세요.

## 🤟 구현 과정

1. 동물의 총 마리 수를 요구하므로, `SELECT COUNT(*)`를 사용하였다.

2. `ANIMAL_INS`라는 테이블에 모든 정보가 들어있으므로, `FROM ANIMAL_INS`를 사용하였다.

---
---

# [중복 제거하기 PGs#59408](https://programmers.co.kr/learn/courses/30/lessons/59408)

<details>
<summary><b>코드 보기</b></summary>
<div markdown="1">

```SQL
SELECT COUNT(DISTINCT NAME)
FROM ANIMAL_INS
```

</div>
</details>


## ☝ 입력 형식

+ `ANIMAL_INS` 테이블은 동물 보호소에 들어온 동물의 정보를 담은 테이블입니다.
+ `ANIMAL_INS` 테이블 구조는 다음과 같으며, `ANIMAL_ID`, `ANIMAL_TYPE`, `DATETIME`, `INTAKE_CONDITION`, `NAME`, `SEX_UPON_INTAKE`는 각각 동물의 아이디, 생물 종, 보호 시작일, 보호 시작 시 상태, 이름, 성별 및 중성화 여부를 나타냅니다.

|**NAME**|**TYPE**|**NULLABLE**|
|--|--|--|
|ANIMAL_ID|VARCHAR(N)|FALSE
|ANIMAL_TYPE|VARCHAR(N)|FALSE
|DATETIME|DATETIME|FALSE
|INTAKE_CONDITION|VARCHAR(N)|FALSE|
|NAME|VARCHAR(N)|TRUE|
|SEX_UPON_INTAKE|VARCHAR(N)|FALSE|

## 🤞 출력 형식

+ 동물 보호소에 들어온 동물의 이름은 몇 개인지 조회하는 SQL 문을 작성해주세요.
+ 이름이 NULL인 경우는 집계하지 않으며 중복되는 이름은 하나로 칩니다.

## 🤟 구현 과정

1. 동물의 이름의 가짓수를 요구하므로, `SELECT COUNT(NAME)`를 사용하였다.

2. 하지만, 중복되는 이름은 하나로 치므로, `NAME`를 `DISTINCT NAME`으로 바꾸어주었다.

3. `ANIMAL_INS`라는 테이블에 모든 정보가 들어있으므로, `FROM ANIMAL_INS`를 사용하였다.


