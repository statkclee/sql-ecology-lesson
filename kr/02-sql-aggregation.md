---
layout: lesson
root: ../
title: SQL 집계(Aggregation)
minutes: 30
---

## 갯수 세기와 GROUP BY

집계 기능을 통해 특정값 기준으로 레코드를 군집으로 묶거나 군집에 포함된 조합된 값을 계산할 수 있다.

`surveys` 테이블로 가서 얼마나 많은 개체가 존재하는지 파악하자.
단순히 와일드카드를 사용하게 되면 레코드(행) 갯수를 셀 수 있다.

    SELECT COUNT(*)
    FROM surveys;

또한, 개체전체에 대한 전체 무게도 알아낼 수 있다.

    SELECT COUNT(*), SUM(weight)
    FROM surveys;

단위를 킬로그램으로 소수점 아래 3자리에서 반올림해서 출력값을 조정할 수도 있다:

    SELECT ROUND(SUM(weight)/1000.0, 3)
    FROM surveys;

SQL 기본 구문에 포함된 다른 집계내는 함수에는 `MAX`, `MIN`, `AVG` 도 포함된다.

> ### 도전과제
>
> 조사기간에 걸쳐 생포된 모든 동물에 대한 전체 무게, 평균 체중, 최대 체중, 최소 체중을 계산하는 질의문을 작성하시오.
> 작성된 질의문을 변경해서 체중이 5에서 10사이 체중에 대한 값만 뽑아내도록 작성하시오.

이제, 각 종족별로 얼마나 많은 개체가 포함되어 있는지 살펴보자. 
이런 유형의 작업에 필요한 것이 `GROUP BY` 절이다.

    SELECT species_id, COUNT(*)
    FROM surveys
    GROUP BY species_id;

`GROUP BY` 절은 SQL에 어떤 필드 혹은 여러 필드를 기준으로 데이터 집계를 계산할지 지정한다.
다수 필드로 집단을 묶고자 할 경우, `GROUP BY` 절에 콤마 구분자로 필드명을 구분한다.

> ### 도전과제
>
> 다음 결과를 출력하는 질의문을 작성하시오:
>
> 1. 매년 얼마나 많은 개체가 집계되는가?
a) 전체;
b) 각 종족별로.
> 2. 매년 각 종족별 평균 체중을 집계하시오.
1번 질의문을 변경하여 2번 질의문과 결합하여 질의문을 하나로 작성할 수 있는가?

## `HAVING` 예약어

앞선 수업에서, 예약어 `WHERE`를 살펴봤다. 특정 기준에 근거해서 결과값을 필터링한다.
SQL은 집계합수 함수에서 나온 결과를 `HAVING` 예약어를 통해 필터링하는 메커니즘도 제공한다. 

예를 들어, 개체수가 10 보다 큰 값을 갖는 종에 대한 정보만 추출하도록 마지막 작성한 질의문을 맞춰 작성할 수도 있다:

    SELECT species_id, COUNT(surveys.species_id)
    FROM surveys
    GROUP BY species_id
    HAVING COUNT(surveys.species_id) > 10;

`HAVING` 키워드는 `WHERE` 키워드처럼 정확하게 동일하게 동작하지만,
데이터베이스 필드명 대신에 집계함수를 사용한다.

질의문에 `AS`를 사용해서 칼럼명을 변경하면, `HAVING` 절은 이 정보를 사용해서 
질의문에 가독성을 높인다. 
예를 들어, 앞선 질의문에 `COUNT(surveys.species_id)`  표현식에 `occurrences` 같은 명칭을 부여할 수 있다.
이를 반영한 질의문은 다음과 같다:


    SELECT species_id, COUNT(surveys.species_id) AS occurrences
    FROM surveys
    GROUP BY species_id
    HAVING occurrences > 10;

두가지 질의문에서 주목할 점은 `HAVING` 절이 `GROUP BY` *다음에* 온다는 점이다.
이를 생각하는 방식은 다음과 같다:
데이터를 불러오면(`SELECT`), 필터링 되고(`WHERE`), 그리고 나서 집단으로 병합된다(`GROUP BY`);
마지막으로, 집단으로 묶인 것중 일부만 선택한다(`HAVING`).

> ### 도전과제
>
> `species` 테이블에서 `taxa` 각각에 포함된 `genus` 갯수를 뽑아내는데, 
`genus` 가 10 보다 큰 값을 갖는 `taxa`에 대해서만 추출하는 질의문을 작성한다.

## 집계된 결과 정렬

집계결과가 담긴 열이 포함해서, 특정 열을 기준으로 결과를 정렬할 수도 있다.
생포된 종족별로 개체수를 세어 보는데, 개체수를 기준으로 정렬한다.

    SELECT species_id, COUNT(*)
    FROM surveys
    GROUP BY species_id
    ORDER BY COUNT(species_id);


## 질의문을 저장해서 재사용

한번이상 동일한 연산작업을 반복하는 것이 일반적이다. 예를 들어, 모니터링 혹은 보고서 작성 목적이 여기에 포함된다.
SQL에는 이런 작업을 수행하는 매우 강력한 매커니즘이 제공된다: **뷰(View)**.
뷰는 질의문의 한가지 형태로 데이터베이스에 저장되어, 정보를 조회, 필터링, 갱신도 가능케 한다.
뷰를 생각하는 한가지 방식은 테이블로 볼 수 있는데, 최종 질의 결과를 산출하기 전에 
여기저기 흩어진 테이블에서 정보를 읽어 들이고, 집계하고, 필터링한다. 

질의문에서 뷰를 생성하는 하려면 `CREATE VIEW viewname AS`를 질의문 바로 앞에 추가한다.
예를 들어, 개인 프로젝트로 2000 년 5월에서 9월까지 수집된 데이터만 관계된다고 가정하자.
개인 프로젝트로 필요한 데이터를 추출하는 질의문은 다음과 같다:

    SELECT *
    FROM surveys
    WHERE year = 2000 AND (month > 4 AND month < 10)

하지만, 해당 데이터에 대한 질문을 받을 때마다 매번 질의문을 작성하고 싶지는 않다!
뷰를 생성시켜 이런 문제를 해결해보자:

    CREATE VIEW summer_2000 AS
    SELECT *
    FROM surveys
    WHERE year = 2000 AND (month > 4 AND month < 10)

*View* 메뮤에서 *Create View*를 사용해서 뷰를 추가하고 나서
테이블과 마찬가지로 *Views* 탭에서 결과를 확인할 수 있다.

이제, 훨씬 더 간단한 표기법으로 결과에 접근할 수 있게 된다:

    SELECT *
    FROM summer_2000;

> ### 도전과제
>
> 매년 잡힌 각 종별 개체수를 반환하는 질의문을 작성한다.
> 단, 가장 최근 레코드부터 시작되도록 내림차순으로 작성하는데,
> 각 연도별로 가장 많은 종부터 가장 작은 종순으로 정렬되게 만든다.
> 질의문 결과를 `VIEW`로 저장한다.

## Null 값

앞절(`summer_2000`)에서 생성한 뷰를 사용해서 `null` 값에 대해 알아보자.
SQL에서 결측값은 특수값 `NULL` 로 식별된다.
`summer_2000` 뷰를 스크롤해서 살펴보자.
결측값이 포함된 레코드를 심심찮게 볼 수 있다.
결측값이 포함된 행을 어떻게 필터링할 수 있는지 생각해 보자.

`species_id` 필드에 결측값이 포함된 모든 레코드를 찾아내려면, 질의문을 다음과 같이 작성할 수 있다:

    SELECT *
    FROM summer_2000
    WHERE species_id IS NULL

`species_id` 필드에 결측값이 포함되지 않은 모든 레코드를 뽑아내려면,
작성한 질의문에 `NOT` 예약어를 추가한다.

    SELECT *
    FROM summer_2000
    WHERE species_id IS NOT NULL

"PE" 종으로 질의문을 한정시키면, 훨씬 파악하기 쉽다:

    SELECT *
    FROM summer_2000
    WHERE species_id == 'PE'

레코드가 6개만 존재한다. `weight` 칼럼을 살펴보고, 평균 체중이 얼마나 나가는지 알아보는 것은 쉽다.
SQL을 사용해서 평균체중을 계산하려면, `NULL` 값을 무시하고 SQL이 자동으로 다음과 같이 질의문을 동작시키기를 희망한다: 

    SELECT AVG(weight)
    FROM summer_2000
    WHERE species_id == 'PE'

하지만, 좀더 똑똑하게 평균을 구하게 되면 실수를 범하게 된다:

    SELECT SUM(weight), COUNT(*), SUM(weight)/COUNT(*)
    FROM summer_2000
    WHERE species_id == 'PE'

COUNT 명령어는 레코드 6개를 모두 포함(`null` 값을 갖는 것조차 포함)하지만, 
SUM에는 `weight` 필드에 값을 갖는 레코드 4개만 포함시켜 계산을 수행해서 
틀린 평균값이 산출된다.
하지만, 합계를 산출하는 명령어를 약간만 변경시키면 동일한 전략이 *제대로* 동작되게 된다:

    SELECT SUM(weight), COUNT(weight), SUM(weight)/COUNT(weight)
    FROM summer_2000
    WHERE species_id == 'PE'

`weight` 필드를 개별적으로 갯수를 세게되면, 필드에 결측값을 갖는 레코드는 무시하고 넘어간다.
이것이 까다로운 `NULL`이 갖는 의미를 보여주고 있다 - COUNT(*) 와 COUNT(필드명)이 다른 값을 반환한다.

또다른 사례는 부정(negative) 질의문을 작성할 때다 - 암컷이 아닌 동물 모두를 세어보자:

    SELECT COUNT(*) 
    FROM summer_2000
    WHERE sex != 'F'

이제 수컷이 아닌 동물을 다른 방식으로 세어보자:

    SELECT COUNT(*) 
    FROM summer_2000
    WHERE sex != 'M'

하지만, 총계로 두 값을 비교하면 다음과 같다:

    SELECT COUNT(*) 
    FROM summer_2000

더하면 총합과 맞지 않는 것이 확인된다.
부정문에 `NULL`값이 SQL에서 자동으로 포함되지 않기 때문이다.
그래서 "not x" 질의문을 던지면, SQL은 데이터를 세가지 범주로 나눈다: 'x', 'not NULL, not x', NULL, 
그리고 나서, 'not NULL', 'not x' 집단을 반환한다.
흔히 이런 산출결과가 원하는 바이기도 하지만, 결측값도 포함되기를 원하기도 한다! 이런 경우에 질의문을 다음과 같이 변경한다:

    SELECT COUNT(*)
    FROM summer_2000
    WHERE sex != 'M' OR sex IS NULL

이전: [질의문(쿼리) 기초](../kr/01-sql-basic-queries.html) 다음: [병합(Join)과 별칭](../kr/03-sql-joins-aliases.html)
