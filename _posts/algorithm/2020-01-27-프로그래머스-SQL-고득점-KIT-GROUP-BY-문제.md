---
categories:
  - Algorithm
tags:
  - Programmers
  - SQL
---
# [고양이와 개 PGs#59040](https://programmers.co.kr/learn/courses/30/lessons/59040)

<details>
<summary><b>코드 보기</b></summary>
<div markdown="1">

```SQL
SELECT ANIMAL_TYPE, COUNT(*)
FROM ANIMAL_INS
GROUP BY ANIMAL_TYPE
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

+ 동물 보호소에 들어온 동물 중 고양이와 개가 각각 몇 마리인지 조회하는 SQL문을 작성해주세요.
+ 고양이가 개보다 먼저 조회해주세요.

## 🤟 구현 과정

1. 동물의 정보중 동물의 종류와 마리수를 요구하므로, `SELECT ANIMAL_TYPE, COUNT(*)`를 사용하였다.
	- `COUNT(*)`이 종류별 마릿수가 되기 위해 뒤에서 `GROUP BY`를 사용하였다.
2. `ANIMAL_INS`라는 테이블에 모든 정보가 들어있으므로, `FROM ANIMAL_INS`를 사용하였다.
3.  동물의 종류로 구분하여 데이터를 나누고 싶으므로, `GROUP BY ANIMAL_TYPE`를 사용하였다.

---
---

# [동명 동물 수 찾기 PGs#59041](https://programmers.co.kr/learn/courses/30/lessons/59041)

<details>
<summary><b>코드 보기</b></summary>
<div markdown="1">

```SQL
SELECT NAME, COUNT(*)
FROM ANIMAL_INS
GROUP BY NAME
HAVING COUNT(NAME) >= 2
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

+ 동물 보호소에 들어온 동물 이름 중 두 번 이상 쓰인 이름과 해당 이름이 쓰인 횟수를 조회하는 SQL문을 작성해주세요.
+ 결과는 이름이 없는 동물은 집계에서 제외하며, 결과는 이름 순으로 조회해주세요.

## 🤟 구현 과정

1. 동물의 정보중 이름의 종류와 횟수를 요구하므로, `SELECT NAME, COUNT(*)`를 사용하였다.
	- `COUNT(*)`이 이름별 마릿수가 되기 위해 뒤에서 `GROUP BY`를 사용하였다.
2. `ANIMAL_INS`라는 테이블에 모든 정보가 들어있으므로, `FROM ANIMAL_INS`를 사용하였다.
3.  동물의 종류로 구분하여 데이터를 나누고 싶으므로, `GROUP BY NAME`를 사용하였다.
4. `GROUP BY`로 묶되 두 마리 이상 있는 것만 표기하고 싶으므로, `HAVING COUNT(NAME) >= 2`를 통해 조건을 사용하였다.

---
---

# [입양 시각 구하기 (1) PGs#59412](https://programmers.co.kr/learn/courses/30/lessons/59412)

<details>
<summary><b>코드 보기</b></summary>
<div markdown="1">

```SQL
SELECT HOUR(DATETIME) AS HOUR, COUNT(*)
FROM ANIMAL_OUTS
GROUP BY HOUR(DATETIME)
HAVING HOUR >= 9 and HOUR <= 19
```

</div>
</details>


## ☝ 입력 형식

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

+ 9시부터 19시까지, 각 시간대별로 입양이 몇 건이나 발생했는지 조회하는 SQL문을 작성해주세요.
+ 결과는 시간대 순으로 정렬해야 합니다.

## 🤟 구현 과정

1. 동물이 입양나간 시간(hour)을 알고 싶으므로, `SELECT HOUR(DATETIME) AS HOUR, COUNT(*)`를 사용하였다.
	- `HAVING`에서 사용하기 위해,  `AS HOUR`로 재정의해주었다.
2. `ANIMAL_OUT`라는 테이블에 모든 정보가 들어있으므로, `FROM ANIMAL_OUT`를 사용하였다.
3.  시간별 입양 발생 건 수를 알고 싶으므로, 시간별로 묶어주기 위해 `GROUP BY HOUR(DATETIME)`를 사용하였다.
4. `GROUP BY`로 묶되 9시~19시 사이에 일어난 입양에 관해서만 알고 싶으므로, `HAVING HOUR >= 9 and HOUR <= 19`을 사용하였다.

---
---

# [입양 시각 구하기 (2) PGs#59413](https://programmers.co.kr/learn/courses/30/lessons/59413)

<details>
<summary><b>코드 보기</b></summary>
<div markdown="1">

```SQL
SET @now = -1;

SELECT (@now := @now+1) AS HOUR,
        (SELECT COUNT(*)
         FROM ANIMAL_OUTS
         WHERE HOUR(DATETIME) = @now) AS COUNT
FROM ANIMAL_OUTS
WHERE @now < 23
```

</div>
</details>


## ☝ 입력 형식

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

+ 0시부터 23시까지, 각 시간대별로 입양이 몇 건이나 발생했는지 조회하는 SQL문을 작성해주세요.
+ 결과는 시간대 순으로 정렬해야 합니다.

## 🤟 구현 과정

1. 동물이 입양나간 시간(`HOUR`)을 담당하는 변수 `@now`를 선언하였고, `SELECT`문에서 `HOUR`로 명명하였다.
2. subquery를 이용해 `@now`의 시간에 입양된 동물의 수를 `COUNT(*)`로 세었고, `SELECT`문에서 `COUNT`로 명명하였다.
3.  `ANIMAL_OUT`라는 테이블에 모든 정보가 들어있으므로, `FROM ANIMAL_OUT`를 사용하였다.
4. 23시까지만 알면 되므로, `WHERE @NOW < 23`을 사용하였다.

## 🍀 Pain Points

1. 데이터에는 7~19시까지의 기록 밖에 없음.
	- 0~23시까지 조회하는 SQL문을 작성 하려면 변수를 통한 조작을 해야함.

