---
categories:
  - Algorithm
tags:
  - Programmers
  - SQL
---
# [모든 레코드 조회하기 PGs#59034](https://programmers.co.kr/learn/courses/30/lessons/59034)

<details>
<summary><b>코드 보기</b></summary>
<div markdown="1">

```SQL
SELECT *
FROM ANIMAL_INS 
ORDER BY ANIMAL_ID
```

</div>
</details>

- - -

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

+ 동물 보호소에 들어온 모든 동물의 정보를 ANIMAL_ID순으로 조회하는 SQL문을 작성해주세요.

## 🤟 구현 과정

1. 동물의 모든 정보를 요구하므로, `SELECT *`을 사용하였다.

2. `ANIMAL_INS`라는 테이블에 모든 정보가 들어있으므로, `FROM ANIMAL_INS`를 사용하였다.

3. `ANIMAL_ID`순으로 조회하므로, `ORDER BY ANIMAL_ID`를 사용하였다.


---
---


# [역순 정렬하기 PGs#59035](https://programmers.co.kr/learn/courses/30/lessons/59035)

<details>
<summary><b>코드 보기</b></summary>
<div markdown="1">

```SQL
SELECT NAME, DATETIME
FROM ANIMAL_INS
ORDER BY ANIMAL_ID DESC
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

+ 동물 보호소에 들어온 모든 동물의 이름과 보호 시작일을 조회하는 SQL문을 작성해주세요. 이때 결과는 ANIMAL_ID 역순으로 보여주세요.

## 🤟 구현 과정

1. 동물의 정보중 이름과 보호 시작일만 요구하므로, `SELECT NAME, DATETIME`를 사용하였다.

2. `ANIMAL_INS`라는 테이블에 모든 정보가 들어있으므로, `FROM ANIMAL_INS`를 사용하였다.

3. `ANIMAL_ID`순으로 조회하므로, `ORDER BY ANIMAL_ID`를 사용하으나,

4. `ANIMAL_ID`의 역순으로 조회하므로, `ORDER BY ANIMAL_ID DESC`로 변경하였다.


---
---


# [아픈 동물 찾기 PGs#59036](https://programmers.co.kr/learn/courses/30/lessons/59036)


<details>
<summary><b>코드 보기</b></summary>
<div markdown="1">

```SQL
SELECT ANIMAL_ID, NAME
FROM ANIMAL_INS
WHERE INTAKE_CONDITION = "Sick"
ORDER BY ANIMAL_ID
```

</div>
</details>

- - -

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

+ 동물 보호소에 들어온 동물 중 아픈 동물<sup>`INTAKE_CONDITION`이 Sick 인 경우</sup>의 아이디와 이름을 조회하는 SQL 문을 작성해주세요. 이때 결과는 아이디 순으로 조회해주세요.

## 🤟 구현 과정

1. 동물의 정보중 아이디와 이름만 요구하므로, `SELECT ANIMAL_ID, NAME`를 사용하였다.

2. `ANIMAL_INS`라는 테이블에 모든 정보가 들어있으므로, `FROM ANIMAL_INS`를 사용하였다.

3. 아픈 동물만 찾고자 하므로, `WHERE INTAKE_CONDITION="Sick"`를 사용하였다.

4. `ANIMAL_ID`순으로 조회하므로, `ORDER BY ANIMAL_ID`를 사용하였다.


---
---


# [어린 동물 찾기 PGs#59037](https://programmers.co.kr/learn/courses/30/lessons/59037)


<details>
<summary><b>코드 보기</b></summary>
<div markdown="1">

```SQL
SELECT ANIMAL_ID, NAME
FROM ANIMAL_INS
WHERE NOT INTAKE_CONDITION = "AGED"
ORDER BY ANIMAL_ID
```

</div>
</details>

- - -

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

+ 동물 보호소에 들어온 동물 중 젊은 동물<sup>`INTAKE_CONDITION`이 Aged가 아닌 경우</sup>의 아이디와 이름을 조회하는 SQL 문을 작성해주세요.
+ 결과는 아이디 순으로 조회해주세요.

## 🤟 구현 과정

1. 동물의 정보중 아이디와 이름만 요구하므로, `SELECT ANIMAL_ID, NAME`를 사용하였다.

2. `ANIMAL_INS`라는 테이블에 모든 정보가 들어있으므로, `FROM ANIMAL_INS`를 사용하였다.

3. 나이들지 않은 동물만 찾고자 하므로, `WHERE NOT INTAKE_CONDITION = "AGED"`를 사용하였다.

4. `ANIMAL_ID`순으로 조회하므로, `ORDER BY ANIMAL_ID`를 사용하였다.


---
---


# [동물의 아이디와 이름 PGs#59403](https://programmers.co.kr/learn/courses/30/lessons/59403)


<details>
<summary><b>코드 보기</b></summary>
<div markdown="1">

```SQL
SELECT ANIMAL_ID, NAME
FROM ANIMAL_INS
ORDER BY ANIMAL_ID
```

</div>
</details>

- - -

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

+ 동물 보호소에 들어온 모든 동물의 아이디와 이름을 ANIMAL_ID순으로 조회하는 SQL문을 작성해주세요.

## 🤟 구현 과정

1. 동물의 정보중 아이디와 이름만 요구하므로, `SELECT ANIMAL_ID, NAME`를 사용하였다.

2. `ANIMAL_INS`라는 테이블에 모든 정보가 들어있으므로, `FROM ANIMAL_INS`를 사용하였다.

3. `ANIMAL_ID`순으로 조회하므로, `ORDER BY ANIMAL_ID`를 사용하였다.


---
---


# [여러 기준으로 정렬하기 PGs#59404](https://programmers.co.kr/learn/courses/30/lessons/59404)


<details>
<summary><b>코드 보기</b></summary>
<div markdown="1">

```SQL
SELECT ANIMAL_ID, NAME, DATETIME
FROM ANIMAL_INS
ORDER BY NAME, DATETIME DESC
```

</div>
</details>

- - -

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

+ 동물 보호소에 들어온 모든 동물의 아이디와 이름, 보호 시작일을 이름 순으로 조회하는 SQL문을 작성해주세요.
+ 단, 이름이 같은 동물 중에서는 보호를 나중에 시작한 동물을 먼저 보여줘야 합니다.

## 🤟 구현 과정

1. 동물의 정보중 아이디와 이름, 보호 시작일만 요구하므로, `SELECT ANIMAL_ID, NAME, DATETIME`를 사용하였다.

2. `ANIMAL_INS`라는 테이블에 모든 정보가 들어있으므로, `FROM ANIMAL_INS`를 사용하였다.

3. 정렬 1순위 조건은 `NAME`이며, 정렬 2순위 조건은 `DATETIME`이지만, 보호시작일은 역순으로 정렬해야 하므로 `ORDER BY NAME, DATETIME DESC`를 사용하였다.


---
---


# [상위 N개 레코드 PGs#59405](https://programmers.co.kr/learn/courses/30/lessons/59405)


<details>
<summary><b>코드 보기</b></summary>
<div markdown="1">

```SQL
SELECT NAME
FROM ANIMAL_INS
ORDER BY DATETIME
LIMIT 1
```

</div>
</details>

- - -

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

+ 동물 보호소에 가장 먼저 들어온 동물의 이름을 조회하는 SQL 문을 작성해주세요.

## 🤟 구현 과정

1. 동물의 정보중 이름만 요구하므로, `SELECT NAME`를 사용하였다.

2. `ANIMAL_INS`라는 테이블에 모든 정보가 들어있으므로, `FROM ANIMAL_INS`를 사용하였다.

3. 먼저 들어온 동물이 우선시되므로, `ORDER BY DATETIME`를 사용하였다.

4. 가장 먼저 들어온 동물 1마리만 검색하고자 하므로, `LIMIT 1`을 사용하였다.









