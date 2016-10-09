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



SQL includes numerous functions for manipulating data. You've already seen some
of these being used for aggregation (`SUM` and `COUNT`) but there are functions
that operate on individual values as well. Probably the most important of these
are `IFNULL` and `NULLIF`. `IFNULL` allows us to specify a value to use in
place of `NULL`.

We can represent unknown sexes with "U" instead of `NULL`:

    SELECT species_id, sex, IFNULL(sex, 'U') AS non_null_sex
    FROM surveys;

The lone "sex" column is only included in the query above to illustrate where
`IFNULL` has changed values; this isn't a usage requirement.

> ### Challenge:
>
> Write a query that returns 30 instead of `NULL` for values in the
> `hindfoot_length` column.

> ### Challenge:
>
> Write a query that calculates the average hind-foot length of each species,
> assuming that unknown lengths are 30 (as above).

`IFNULL` can be particularly useful in `JOIN`. When joining the `species` and
`surveys` tables earlier, some results were excluded because the `species_id`
was `NULL`. We can use `IFNULL` to include them again, re-writing the `NULL` to
a valid joining value:

    SELECT surveys.year, surveys.month, surveys.day, species.genus, species.species
    FROM surveys
    JOIN species
    ON surveys.species_id = IFNULL(species.species_id, 'AB');

> ### Challenge:
>
> Write a query that returns the number of genus of the animals caught in each
> plot, using `IFNULL` to assume that unknown species are all of the genus
> "Rodent".

The inverse of `IFNULL` is `NULLIF`. This returns `NULL` if the first argument
is equal to the second argument. If the two are not equal, the first argument
is returned. This is useful for "nulling out" specific values.

We can "null out" plot 7:

    SELECT species_id, plot_id, NULLIF(plot_id, 7) AS partial_plot_id
    FROM surveys;

Some more functions which are common to SQL databases are listed in the table
below:

| Function                     | Description                                                                                     |
|------------------------------|-------------------------------------------------------------------------------------------------|
| `ABS(n)`                     | Returns the absolute (positive) value of the numeric expression *n*                             |
| `LENGTH(s)`                  | Returns the length of the string expression *s*                                                 |
| `LOWER(s)`                   | Returns the string expression *s* converted to lowercase                                        |
| `NULLIF(x, y)`               | Returns NULL if *x* is equal to *y*, otherwise returns *x*                                      |
| `ROUND(n)` or `ROUND(n, x)`  | Returns the numeric expression *n* rounded to *x* digits after the decimal point (0 by default) |
| `TRIM(s)`                    | Returns the string expression *s* without leading and trailing whitespace characters            |
| `UPPER(s)`                   | Returns the string expression *s* converted to uppercase                                        |

Finally, some useful functions which are particular to SQLite are listed in the
table below:

| Function                            | Description                                                                                                                                                                    |
|-------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `IFNULL(x, y)`                      | Returns *x* if it is non-NULL, otherwise returns *y*                                                                                                                           |
| `RANDOM()`                          | Returns a random integer between -9223372036854775808 and +9223372036854775807.                                                                                                |
| `REPLACE(s, f, r)`                  | Returns the string expression *s* in which every occurrence of *f* has been replaced with *r*                                                                                  |
| `SUBSTR(s, x, y)` or `SUBSTR(s, x)` | Returns the portion of the string expression *s* starting at the character position *x* (leftmost position is 1), *y* characters long (or to the end of *s* if *y* is omitted) |

> ### Challenge:
>
> Write a query that returns genus names, sorted from longest genus name down
> to shortest.

## Aliases

As queries get more complex names can get long and unwieldy. To help make things
clearer we can use aliases to assign new names to things in the query.

We can alias both table names:

    SELECT surv.year, surv.month, surv.day, sp.genus, sp.species
    FROM surveys AS surv
    JOIN species AS sp
    ON surv.species_id = sp.species_id;

And column names:

    SELECT surv.year AS yr, surv.month AS mo, surv.day AS day, sp.genus AS gen, sp.species AS sp
    FROM surveys AS surv
    JOIN species AS sp
    ON surv.species_id = sp.species_id;

The `AS` isn't technically required, so you could do

    SELECT surv.year yr
    FROM surveys surv;

but using `AS` is much clearer so it is good style to include it.

> ### Challenge (optional):
>
> SQL queries help us *ask* specific *questions* which we want to answer about our data. The real skill with SQL is to know how to translate our scientific questions into a sensible SQL query (and subsequently visualize and interpret our results).
>
> Have a look at the following questions; these questions are written in plain English. Can you translate them to *SQL queries* and give a suitable answer?  
> 
> 1. How many plots from each type are there?  
> 
> 2. How many specimens are of each sex are there for each year?  
> 
> 3. How many specimens of each species were captured in each type of plot?  
> 
> 4. What is the average weight of each taxa?  
> 
> 5. What is the percentage of each species in each taxa?  
> 
> 6. What are the minimum, maximum and average weight for each species of Rodent?  
>
> 7. What is the average hindfoot length for male and female rodent of each species? Is there a Male / Female difference?  
> 
> 8. What is the average weight of each rodent species over the course of the years? Is there any noticeable trend for any of the species?  

> Proposed solutions:
>
> 1. Solution: `SELECT plot_type, count(*) AS num_plots  FROM plots  GROUP BY plot_type  ORDER BY num_plots DESC`
>
> 2. Solution: `SELECT year, sex, count(*) AS num_animal  FROM surveys  WHERE sex IS NOT null  GROUP BY sex, year`
>
> 3. Solution: `SELECT species_id, plot_type, count(*) FROM surveys JOIN plots ON surveys.plot_id=plots.plot_id WHERE species_id IS NOT null GROUP BY species_id, plot_type`
>
> 4. Solution: `SELECT taxa, AVG(weight) FROM surveys JOIN species ON species.species_id=surveys.species_id GROUP BY taxa`
>
> 5. Solution: `SELECT taxa, 100.0*count(*)/(SELECT count(*) FROM surveys) FROM surveys JOIN species ON surveys.species_id=species.species_id GROUP BY taxa`
>
> 6. Solution: `SELECT surveys.species_id, MIN(weight) as min_weight, MAX(weight) as max_weight, AVG(weight) as mean_weight FROM surveys JOIN species ON surveys.species_id=species.species_id WHERE taxa = 'Rodent' GROUP BY surveys.species_id`
>
> 7. Solution: `SELECT surveys.species_id, sex, AVG(hindfoot_length) as mean_foot_length  FROM surveys JOIN species ON surveys.species_id=species.species_id WHERE taxa = 'Rodent' AND sex IS NOT NULL GROUP BY surveys.species_id, sex`
>
> 8. Solution: `SELECT surveys.species_id, year, AVG(weight) as mean_weight FROM surveys JOIN species ON surveys.species_id=species.species_id WHERE taxa = 'Rodent' GROUP BY surveys.species_id, year`


Previous: [SQL Aggregation](02-sql-aggregation.html)
