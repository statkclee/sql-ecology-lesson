---
layout: lesson
root: ../
title: 병합(Join)과 별칭
minutes: 30
---

## 병합(Join)

테이블 두개를 결합하는데 SQL 예약어 `JOIN` 명령어를 사용하는데 `FROM` 명령어 다음에 위치한다.

`JOIN` 명령어 자체로 외적값이 도출된다. 첫번째 테이블 각 행이 두번째 테이블 각 행과 짝지어 진다.
어떤 방식으로든지 연관된 데이터를 갖고 있는 두 테이블을 조합할 때 일반적으로 이런 방식은 원하는 바가 아니다.

이를 위해서, 컴퓨터에게 두 테이블 사이 연결링크를 제공하는 단어 `ON`을 사용하여 의도를 전달한다.
원하는 바는 동일한 종족코드를 갖는 데이터만 병합시키는 것이다.

    SELECT *
    FROM surveys
    JOIN species
    ON surveys.species_id = species.species_id;

`ON`은 `WHERE` 같은데, 검증조건에 따라 행을 필터링한다.
`table.colname` 형태를 사용해서 어느 테이블이 어느 칼럼을 참조하고 있는지 데이터베이스 관리자에게 
의도를 정확하게 전달한다.

`JOIN`  명령어 출력결과는 첫번째 테이블에서 나온 칼럼과 두번째 테이블에서 나온 칼럼이 모두 포함된다.
상기 질의문을 실행되면, 출력결과는 다음 칼럼명이 부여된 테이블이 만들어지게 된다.

| record_id | month | day | year | plot_id | species_id | sex | hindfoot_length | weight | species_id | genus | species | taxa |
|---|---|---|---|---|---|---|---|---|---|---|---|---|
| ... |||||||||||||   
| 96  | 8  | 20  | 1997  | 12  | **DM**  |  M |  36  |  41  | **DM** | Dipodomys  | merriami  | Rodent  |
| ... |||||||||||||| 

다른 방식으로, 축약해서 `USING` 키워드를 사용할 수도 있다.
이런 경우에, 데이터베이스 관리자에게 `surveys` 테이블과 `species` 테이블을 결합하는데 두 테이블에 모두 공통으로 존재하는
`species_id` 칼럼을 사용한다고 전달하게 된다.

    SELECT *
    FROM surveys
    JOIN species
    USING (species_id);

출력결과는 **species_id** 칼럼 하나만 갖게 된다.

| record_id | month | day | year | plot_id | species_id | sex | hindfoot_length | weight  | genus | species | taxa |
|---|---|---|---|---|---|---|---|---|---|---|---|
| ... ||||||||||||
| 96  | 8  | 20  | 1997  | 12  | DM  |  M |  36  |  41  | Dipodomys  | merriami  | Rodent  |
| ... |||||||||||||

흔히, 테이블 모두에서 필드 모두가 필요하지는 않다. `non-join` 질의문의 필드명을 `table.colname` 방식으로 사용한다.

예를 들어, 각종이 포획된 연도정보가 필요하지만 `species_id` 대신에 실제 종명만 필요한 경우 질의문을 다음과 같이 작성한다.

    SELECT surveys.year, surveys.month, surveys.day, species.genus, species.species
    FROM surveys
    JOIN species
    ON surveys.species_id = species.species_id;

| year | month | day | genus | species |
|---|---|---|---|---|
| ... |||||
| 1977 | 7 | 16 | Neotoma | albigula|
| 1977 | 7 | 16 | Dipodomys | merriami|
|...||||||

> ### 도전과제:
>
> `site`에서 포획된 모든 개체에 대한 `genus`, `species`, `weight` 필드를 반환하는 질의문을 작성한다.

병합(Join)을 정렬, 필터링, 집계와 결합해서 사용할 수 있다. 
서로 다른 처리 유형별로 개체 평균 중량을 구하고자 한다면, 질의문을 다음과 같이 작성한다.

    SELECT plots.plot_type, AVG(surveys.weight)
    FROM surveys
    JOIN plots
    ON surveys.plot_id = plots.plot_id
    GROUP BY plots.plot_type;

> ### 도전과제:
>
> 내림차순으로 각 구획지에서 생포된 동물에 대한 `genus` 숫자를 뽑아내는 질의문을 작성한다.

> ### 도전과제:
>
> 각 설치류 종별로 평균 체중을 계산하는 질의문을 작성한다 (즉, `taxa` 필드에 `Rodent` 값을 갖는 종을 포함시킨다)


## 함수

SQL에는 데이터를 조작하는데 사용되는 함수가 많다. 
집계(`SUM`, `COUNT`)에 사용된 함수를 이미 살펴봤지만, 
각 개별값에도 적용할 수 있는 함수도 있다.
아마도, 가장 중요한 것은 `IFNULL`, `NULLIF`일 것이다.
`IFNULL` 함수는 `NULL` 자리에 대신할 값을 지정할 수 있게 해준다.

성별을 확인되지 않는 `NULL` 값 대신에 "U"로 대신 표현한다:

    SELECT species_id, sex, IFNULL(sex, 'U') AS non_null_sex
    FROM surveys;

"sex" 칼럼은 단지 `IFNULL`을 통해 값이 변경되었는지 확인하는 용도로 포함되었다;
실제로 이렇게 사용하는 경우는 없다.

> ### 도전과제:
>
> `hindfoot_length`  칼럼에 `NULL` 값 대신에 30을 반환하는 질의문을 작성한다.

> ### 도전과제:
>
> 확인되지 않는 길이는 (위와 같이) 30 으로 가정하고, 각 종별로 후족부(hind-foot) 평균길이를 
> 계산하는 질의문을 작성한다.

`JOIN`에서 `IFNULL` 함수를 특히 유용하게 사용할 수 있다.
`species` , `surveys`  테이블을 앞에서 병합할 때,
일부 결과값이 제외되었는데 이유는 `species_id`가 `NULL`인 경우가 있기 때문이다.
`IFNULL` 함수를 사용해서, 적법한 병합값으로 `NULL` 값을 다시 작성해서, 누락된 값을 다시 포함시킨다:

    SELECT surveys.year, surveys.month, surveys.day, species.genus, species.species
    FROM surveys
    JOIN species
    ON surveys.species_id = IFNULL(species.species_id, 'AB');

> ### 도전과제:
>
> 각 포획지에서 사로잡힌 동물종수를 반환하는 질의문을 작성한다.
> `IFNULL` 함수를 사용해서, 미확인된 종은 모두 설치류 "Rodent"로 가정하고 치환시킨다.

`IFNULL` 함수의 역함수는 `NULLIF`다. 첫번째 인자와 두번째 인자가 동일한 경우 
`NULL` 값을 반환한다. 두 인자가 동일하지 않는 경우, 첫번째 인자가 반환된다.
특정 값을 `NULL`값화하는데 유용하게 사용된다.

다음 질의문으로 7번 포획지를 `NULL` 값화한다:


    SELECT species_id, plot_id, NULLIF(plot_id, 7) AS partial_plot_id
    FROM surveys;

SQL 데이터베이스마다 공통적으로 사용될 수 있는 함수가 다음 표에 나열되어 있다:

|  함수명                      | 부연 설명                                                                                       |
|------------------------------|-------------------------------------------------------------------------------------------------|
| `ABS(n)`                     | Returns the absolute (positive) value of the numeric expression *n*                             |
| `LENGTH(s)`                  | Returns the length of the string expression *s*                                                 |
| `LOWER(s)`                   | Returns the string expression *s* converted to lowercase                                        |
| `NULLIF(x, y)`               | Returns NULL if *x* is equal to *y*, otherwise returns *x*                                      |
| `ROUND(n)` or `ROUND(n, x)`  | Returns the numeric expression *n* rounded to *x* digits after the decimal point (0 by default) |
| `TRIM(s)`                    | Returns the string expression *s* without leading and trailing whitespace characters            |
| `UPPER(s)`                   | Returns the string expression *s* converted to uppercase                                        |

마지막으로, SQLite에서만 사용되는 함수는 다음 표에 나열되어 있다:

| Function                            | Description                                                                                                                |
|-------------------------------------|----------------------------------------------------------------------------------------------------------------------------|
| `IFNULL(x, y)`                      | Returns *x* if it is non-NULL, otherwise returns *y*                                                                       |
| `RANDOM()`                          | Returns a random integer between -9223372036854775808 and +9223372036854775807.                                            |
| `REPLACE(s, f, r)`                  | Returns the string expression *s* in which every occurrence of *f* has been replaced with *r*                              |
| `SUBSTR(s, x, y)` or `SUBSTR(s, x)` | Returns the portion of the string expression *s* starting at the character position *x* (leftmost position is 1), *y* characters long (or to the end of *s* if *y* is omitted) |

> ### 도전과제:
>
> 가장 긴 종명부터 가장짧은 종명으로 내림차순으로 정렬하여 종명(`genus`)을 뿌려주는 질의문을 작성한다.

## 별칭(Aliases)

질의문이 복잡해짐에 따라, 명칭이 질어지고 이상해질 수 있다.
이런 문제를 깔끔하게 처리하는데, 별칭(alias)를 사용해서 질의문의 테이블명과 칼럼명 등에 새로운 명칭을 부여한다.



두 테이블에 별칭을 붙인다:

    SELECT surv.year, surv.month, surv.day, sp.genus, sp.species
    FROM surveys AS surv
    JOIN species AS sp
    ON surv.species_id = sp.species_id;

칼럼명에도 별칭을 붙인다:

    SELECT surv.year AS yr, surv.month AS mo, surv.day AS day, sp.genus AS gen, sp.species AS sp
    FROM surveys AS surv
    JOIN species AS sp
    ON surv.species_id = sp.species_id;

`AS`가 기술적으로 꼭 필요한 것은 아니라서, 다음과 같이 작성하는 것도 가능하다:

    SELECT surv.year yr
    FROM surveys surv;

하지만, `AS` 키워드를 사용하는 것이 훨씬 더 깔끔해서, 포함시키는 것이 좋은 코딩 스타일로 권장된다.

> ### 도전과제 (선택):
>
> SQL 질의문을 사용해서 데이터를 통해 답을 얻을 수 있는 특정한 질문을 만들어 내는데 도움이 된다.
> SQL 실전 기술은 과학적 질문을 의미있는 SQL 질의문으로 번역하는 방법을 알고 있느냐다.
> 그리고, 이렇게 나온 결과를 시각화하고 결과를 해석한다.
> 다음 질문을 살펴본다; 질문이 일반적인 평이한 문장으로 작성되어 있다.
> SQL 질의문으로 번역해서 적합한 대답이 도출되었는가?
> 
> 1. 포획지가 유형별로 얼마나 있나?
> 
> 2. 연도별로, 성별로 얼마나 많은 표본시료가 있나?
> 
> 3. 각종별로 얼마나 많은 표본이 포획지마다 생포되었는가?
> 
> 4. 각 `taxa` 별로 평균 무게는 얼마나 될까?
> 
> 5. 각 `taxa`별로 각종별 퍼센티지는 얼마나 될까?
> 
> 6. "Rodent" 종별로 최소, 최대, 평균 무게는 얼마나 될까?
>
> 7. 각 종별로 수컷과 앞컷 설치류(rodent) 후족부 평균 길이는 얼마나 될까? 수컷과 앞컷 사이 차이가 있나?
> 
> 8. 수년에 걸쳐 각 설치류별로 평균 무게는 얼마나 될까? 종별로 눈에 띄는 추세가 발견되었는가?

> 여러 해답중 하나:
>
> 1. 해답: `SELECT plot_type, count(*) AS num_plots  FROM plots  GROUP BY plot_type  ORDER BY num_plots DESC`
>
> 2. 해답: `SELECT year, sex, count(*) AS num_animal  FROM surveys  WHERE sex IS NOT null  GROUP BY sex, year`
>
> 3. 해답: `SELECT species_id, plot_type, count(*) FROM surveys JOIN plots ON surveys.plot_id=plots.plot_id WHERE species_id IS NOT null GROUP BY species_id, plot_type`
>
> 4. 해답: `SELECT taxa, AVG(weight) FROM surveys JOIN species ON species.species_id=surveys.species_id GROUP BY taxa`
>
> 5. 해답: `SELECT taxa, 100.0*count(*)/(SELECT count(*) FROM surveys) FROM surveys JOIN species ON surveys.species_id=species.species_id GROUP BY taxa`
>
> 6. 해답: `SELECT surveys.species_id, MIN(weight) as min_weight, MAX(weight) as max_weight, AVG(weight) as mean_weight FROM surveys JOIN species ON surveys.species_id=species.species_id WHERE taxa = 'Rodent' GROUP BY surveys.species_id`
>
> 7. 해답: `SELECT surveys.species_id, sex, AVG(hindfoot_length) as mean_foot_length  FROM surveys JOIN species ON surveys.species_id=species.species_id WHERE taxa = 'Rodent' AND sex IS NOT NULL GROUP BY surveys.species_id, sex`
>
> 8. 해답: `SELECT surveys.species_id, year, AVG(weight) as mean_weight FROM surveys JOIN species ON surveys.species_id=species.species_id WHERE taxa = 'Rodent' GROUP BY surveys.species_id, year`


이전: [SQL 집계(Aggregation)](02-sql-aggregation.html)
