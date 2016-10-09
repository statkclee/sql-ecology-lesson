---
layout: lesson
root: ../
title: 질의문(쿼리) 기초
minutes: 30
---

## 첫번째 질의문 작성

**surveys** 테이블을 사용해서 시작해봅시다. 
데이터에는 언제 생포되었는지, 생포된 구획이 어디인지, 종별 ID, 성별, 체중이 그램단위로 포함된 모든 개체 정보가 담겨있다.

**surveys** 테이블에서 `year` 연도 칼럼만 뽑아내는 SQL 질의문을 작성한다.

    SELECT year
    FROM surveys;

`SELECT` 와 `FROM`이 대문자로 작성되었는데 이유는 SQL 예약어이기 때문이다.
SQL은 대소문자를 구별하지 하지 않아 문제가 되지 않지만, 가독성에 도움을 주고, 질의문을 작성하는 좋은 스타일이 된다.

추가 정보가 더 필요하면, `SELECT` 바로 다음에 새로운 칼럼을 필드 목록으로 추가한다:

    SELECT year, month, day
    FROM surveys;

혹은, 와일드카드 문자 `*` 기호를 사용해서 테이블에 모든 칼럼을 선택한다.

    SELECT *
    FROM surveys;

### 유일무이한 값(Unique Value)

재빨리 어떤 종이 표집되었는지 유일무이한 값만 필요하다면, `DISTINCT` 예약어를 사용한다.

    SELECT DISTINCT species_id
    FROM surveys;

칼럼하나 이상 선택하게 되면, 유일무이한 값들이 짝지어 반환된다.

    SELECT DISTINCT year, species_id
    FROM surveys;

### 계산된 값

질의문에 값을 계산할 수도 있다. 예를 들어, 날짜별로 각 개체별 중량을 알고자 하지만, 
그램 대신에 단위를 킬로그램으로 뽑아낼 수도 있다.

    SELECT year, month, day, weight/1000.0
    FROM surveys;

질의문을 실행할 때, `weight / 1000.0` 표현식이 각 행마다 평가되고, 새로운 칼럼에 해당열에 덧붙여지게 된다.
표현식은 어떤 필드에도 사용할 수 있는데, 산칙연산자(`+`, `-`, `*`, and `/`)와 다양한 내장함수가 적용된다.
예를 들어, 사람이 읽기 쉽게 값을 반올림하는 것도 가능하다.

    SELECT plot_id, species_id, sex, weight, ROUND(weight / 1000.0, 2)
    FROM surveys;

> ## 도전과제
>
> `year`, `month`, `day`, `species_id`를 뽑아내고, `weight`는 밀리그램으로 산출되는 질의문을 작성한다.

## 필터링(Filtering)

데이터베이스로 데이터를 필터링하는 것도 가능하다 - 특정 조건을 만족하는 데이터만 선택해서 뽑아냄.
예를 들어, `DM` 이라는 특정한 종족코드를 갖는 _Dipodomys merriami_ 종만 해당되는 데이터를 뽑아내고자 한다.
이런 유형의 질의문을 작성하는데 `WHERE`절을 추가하면 된다:

    SELECT *
    FROM surveys
    WHERE species_id='DM';

숫자로도 동일한 작업을 수행할 수 있다.
이번에 2000년 이후 데이터만 필요한 경우 쿼리문을 다음과 같이 작성한다:

    SELECT * FROM surveys
    WHERE year >= 2000;

`AND` 와 `OR`를 조합해서 좀더 고급스런 조건을 질의문으로 작성할 수도 있다.
예를 들어, 2000년부터 *Dipodomys merriami* 종에 대한 데이터가 필요한 경우 다음과 같이 질의문을 작성한다:

    SELECT *
    FROM surveys
    WHERE (year >= 2000) AND (species_id = 'DM');

괄호가 없어도 되지만, 다시 한번 강조하지만, 가독성에 큰 도움이 된다.
또한, 의도한 방식대로 `AND` 와 `OR`를 컴퓨터가 조합하게 확실히 한다.

*Dipodomys*에 해당되는 종족(`DM`, `DO`, `DS`이 해당코드)에 대한 데이터가 필요한 경우, `OR` 연산자를 사용해서
조합한다.

    SELECT *
    FROM surveys
    WHERE (species_id = 'DM') OR (species_id = 'DO') OR (species_id = 'DS');

> ### 도전과제
>
> `year`, `month`, `day`, `species_id`를 뽑아내고, `weight`는 킬로그램으로 산출되는 질의문을 작성한다.
> 이번에는 `Plot 1`에서 생포된 체중이 75 그램 이상이 되는 개체만 필터링해서 뽑아낸다.


## 더 복잡한 질의문 작성

2000년부터 _Dipodomys_ 3종에 대한 데이터를 추출하는데 생성한 상기 쿼리문을 조합하자.
이번에는 `IN` 예약어를 사용해서 질의문을 더 이해하기 쉽게 작성한다.
`IN`을 사용하게 되면 `WHERE (species_id = 'DM') OR (species_id = 'DO') OR (species_id = 'DS')`와 동일하지만,
코드가 깔끔하고 가독성이 더 좋게 된다:

    SELECT *
    FROM surveys
    WHERE (year >= 2000) AND (species_id IN ('DM', 'DO', 'DS'));

단순한 질의문으로 시작해서, 하나씩 하나씩 순차적으로 절을 추가해 나간다.
물론 좀더 복잡한 질의문으로 작성해 가면서 테스트 검정을 동시에 수행한다.
복잡한 질의문을 작성할 때, 원하는 바를 얻었는지 확인하면서 작성해 나가는데 있어,
좋은 전략이 된다. 종종, 질의문을 적용시킬 임시 데이터베이스에 데이터의 일부를 뽑아내서 작업하는 것도 
일반적이다. 특히, 용량이 좀더 크고, 더 복잡하게 구성된 데이터베이스에 작업을 하기 전에 이런 전략을 취한다.

질의문이 복잡해지면, 주석이 추가되면 도움이 된다. SQL에서 주석은 `--` 으로 시작되고 효과는 행 끝칸까지 미친다.
예를 들어, 앞선 질의문을 주석을 단 버젼은 다음과 같이 작성될 수 있다:

    -- Dipodomys 종에 대한 2000년 이후 데이터를 뽑아냄
    -- surveys 테이블에 모든 칼럼을 선택한다.
    SELECT * FROM surveys
    -- `year` 칼럼에서 표집하는데 2000년도 포함한다.
    WHERE (year >= 2000)
    -- Dipodomys 종은 `species_id` 칼럼에 DM, DO, DS 코드값으로 표현된다.
    AND (species_id IN ('DM', 'DO', 'DS'));

SQL 질의문이 흔히 일반적인 영어처럼 읽히지만, 주석을 다는 것은 *항상* 도움이 된다; 특히, 훨씬 더 복잡한
질의문을 작성할 때 더욱 그렇다.

## 정렬

`ORDER BY` 예약어를 사용해서 질의문 결과를 정렬한다.
**species** 테이블로 되돌아 가서 `taxa`를 기준으로 알파벳 순으로 정렬한다.

먼저 **species** 테이블에 들어있는 것을 살펴본다.
`species_id`에 대한 species_id, genus, species, taxa 정보가 담긴 테이블이다.
별도 테이블에 이런 정보를 담아 처리하는 것은 좋은 생각인데, 이유는 
핵심이 되는 **surveys** 테이블에 모든 정보를 담아둘 필요는 없기 때문이다.

    SELECT *
    FROM species;

이제 `taxa`를 기준으로 정렬해보자.

    SELECT *
    FROM species
    ORDER BY taxa ASC;

`ASC` 예약어는 오름차순으로 정렬하도록 지시하는 기능을 갖고 있다.
`DESC` 예약어를 사용해서 내림차순으로 정렬시킬 수도 있다.

    SELECT *
    FROM species
    ORDER BY taxa DESC;

`ASC` 예약어가 기본디폴트 설정이다.

한번에 몇몇 필드를 동시에 정렬할 수도 있다.
정말 알파벳순으로 정렬시키려면, `genus` 다음에 `species` 순으로 정렬시킨다.

    SELECT *
    FROM species
    ORDER BY genus ASC, species ASC;

> ### 도전과제 
>
> `surveys` 테이블에서 `year`, `species_id`, `weight`는 킬로그램으로 작성된 필드 기준으로 뽑아내는데,
> 가장 체중이 많이 나가는 것이 최상단에 위치되게 정렬시킨다.


## 실행 순서

순서에 대해 주목할 점. 실제로 정렬할 칼럼을 화면에 표시할 필요는 없다.
예를 들어, `species_id`로 새를 정렬하고자 하지만, `genus` 와 `species`만 뽑아낼 수도 있다.

    SELECT genus, species
    FROM species
    WHERE taxa = 'Bird'
    ORDER BY species_id ASC;

상기 작업이 가능할 수 있는데 이유는 정렬이 필드 선택보다 연산 파이프라인에서 앞쪽에서 실행되기 때문이다.

컴퓨터는 기본적으로 다음 작업을 수행한다:

1. `WHERE`에 따라 행을 필터링함.
2. `ORDER BY`로 결과를 정렬함.
3. 요청받은 칼럼 혹은 표현식을 화면에 출력함.

SQL 절이 작성되는 순서는 고정되어 있다: `SELECT`, `FROM`, `WHERE`, 그리고 나서 `ORDER BY`. 
질의문을 한줄로 쭉 작성하는 것도 가능하지만, 각 절마다 구분해서 작성하는 것을 추천한다.

> ### 도전과제
>
> 지금까지 학습한 것을 질의문 하나도 조합한다.
> `surveys` 테이블을 사용해서 질의문을 작성한다.
> 날짜 관련된 필드 세개, `species_id`, `weight`는 킬로그램으로 소수점 아래 두자리까지 필드를 뽑아낸다.
> 1999년에 생포된 개체에 대해 `species_id` 기준 알파벳 오름차순으로 정렬한다.
> 질의문을 한줄로 작성하고 나서, 절을 각각을 별도 행으로 나눠서 작성하고 나서, 둘을 비교해서 어떤 질의문이 더 또렷히 
> 읽을 수 있는지 평가한다.

이전: [SQL 소개](../00-sql-introduction.html) 다음: [SQL 총합](../02-sql-aggregation.html)
