---
layout: lesson
root: ../
title: Databases using SQL
minutes: 60
---

## 실습환경

_주목: 워크샵 시작전에 참석자들이 준비를 해 와야 됩니다._

1. 파이어폭스(Firefox) 설치
2. SQLite 관리자 부가기능 설치: **메뉴 (파이어폭스 우측 상단 세번째 줄, <kbd>`Alt`</kbd>를 누르면 나옴) -> 도구 
   -> 부가 기능(Add-ons) -> 부가 기능 검색 -> SQLite Manager -> 설치 -> 지금 재시작**
4. `SQLite Manager`를 메뉴에 추가: **메뉴 -> Customize, SQLite Manager 아이콘을 끌어 메뉴에 놓기, Customize 나오기**
5. `SQLite Manager` 열기: **메뉴 -> 도구 -> `SQLite Manager`**

# 동기 모티브

프로젝트 작업흐름을 맞춰보자. 앞서, 엑셀과 오픈리파인을 사용해서 
난잡하고 각자 취향에 맞춰 멋대로 생성된 데이터를 정제해서 컴퓨터가 읽을 수 있게 만들었다.
이제 데이터 작업흐름 다음단계로 옮겨가서, 컴퓨터가 데이터를 읽어드리고 나서, 
데이터 분석과 시각화에 사용한다.


## 데이터셋 기술

실습에 사용할 데이터는 아리조나 남부 작은 포유류 군락에 대한 시계열 데이터다.
식물군락에 대한 설치류와 개미의 서식이 미치는 영향을 거의 40년동안 연구한 프로젝트의 일부다.
설치류는 24개 구획지에서 표집되었는데 서로 다른 실험계획에 따라 특정 설치류는 특정 구획에만 접근이 가능하도록 통제했다.


본 데이터는 100편 이상 논문에 사용된 실제 데이터다.
금번 워크샵을 위해서 다소 단순화했지만, 
[전체 데이터셋](http://esapubs.org/archive/ecol/E090/118/)을 다운로드할 수도 있고,
금일 학습한 동일한 도구를 사용해서 추가적인 작업 수행도 가능하다.

## 질문

먼저, [Portal Project dataset](https://figshare.com/articles/Portal_Project_Teaching_Database/1314459) 에서
정제된 스프레드쉬트를 다운로드 받아 살펴본다.

* `surveys.csv`
* `species.csv`
* `plots.csv`

> ### 도전과제
>
> 각 파일마다 담겨진 정보는 무엇인가? 구체적으로 다음 연구질문을 갖는다면:
> 
> * 시간에 따라 2000년대 Dipodomys 종에 대해 어떤 정보를 확인할 수 있는가?
> * 매년 각 종별로 평균 체중은 얼마인가?
> 
> 상기 질문에 대답하기 위해서 필요한 것은 무엇인가?
> 어떤 파일이 데이터를 담고 있는가?
> 수작업으로 작업을 수행한다면 어떤 작업을 수행해야 할까?

## 학습목표

위에서 언급된 질문에 답을 하려면, 다음과 같은 기본 데이터 연산작업을 수행할 필요가 있다:

* 데이터 일부 부분집합을 선택한다. (행과 열)
* 데이터 일부 부분집합을 집단으로 묶는다.
* 수학 연산과 기타 필요한 계산을 수행한다.
* 스프레드쉬트에 흩어진 데이터를 모아 조합한다. 

참고자료: **[분할-적용-병합(Split-Apply-Combine) 전략](https://statkclee.github.io/r-novice-gapminder/12-plyr-kr.html)**

부가적으로, 수작업으로 상기 작업을 수행하고 싶지는 않다! 
본인 스스로 필요한 데이터를 찾고, 여러 스프레드쉬트를 클릭하고, 수작업으로 열을 정렬하는 대신에,
컴퓨터가 해당 작업을 수행하도록 지시한다.

특히, 데이터가 변경되는 경우에 분석작업을 반복하기 쉬운 도구를 사용한다.
실제로 원본 소스 데이터를 변경하지 않고 이 모든 검색작업을 컴퓨터가 자동으로 수행했으면 좋겠다.

데이터를 데이터베이스에 담아두고 나서, SQL을 사용하면 상기 목적을 달성하는데 도움이 된다.

# 데이터베이스

## 왜 관계형 데이터베이스를 사용하나?

* 분석에서 데이터가 분리된다.
    * 분석할 때 데이터가 우연히 변경되는 위험을 회피한다.
    * 데이터가 변경되게 되면, 질의문 쿼리만 다시 실행할 수 있다.
* 데이터양이 큰 경우 빠르다.
* 데이터 입력시 데이터 품질을 높인다. (액세스, Filemaker 등에서 지원되는 자료형 제약, 폼(Form) 사용)
* 관계형 데이터베이스 질의 쿼리 개념은 R 혹은 파이썬 같은 프로그래밍 언어를 사용해서 유사한 작업 수행방법을 이해하는 핵심이다.

## 데이터베이스 관리 시스템 (DBMS)

관계형 데이터로 작업할 때 사용되는 데이터베이스 관리 시스템은 상당히 많다.
SQLite를 사용할 것이지만, 기본적으로 워크샵에서 다루는 모든 내용은 
다른 상용/오픈 데이터베이스 시스템( MySQL, PostgreSQL, MS Access, Filemaker Pro)에도 적용된다.
차이점이 있다면 데이터를 가져오고 내보내는 방법, [자료형 세부사항](#datatypediffs)에서 찾을 수 있다.

## 관계형 데이터베이스

먼저 기존에 만들어 놓은 데이터베이스(`portal_mammals.sqlite`)를 다운로드해서 살펴보자.
메뉴바에 있는 "파일 열기" 아이콘을 클릭해서, `portal_mammals.sqlite` 데이터베이스를 연다.

`SQLite Manager` 화면 좌측편을 보게되면 데이터베이스에 포함된 테이블이 확인된다.
각 테이블은 앞에서 살펴본 `csv` 파일 각각에 대응된다.
테이블에 포함된 내용물을 확인하려면, 화면 중앙에 위치한 "Browse and Search" 탭으로 이동한다.
그러면, 아주 익숙한 뷰(테이블 사본)가 나타난다. 
이를 통해서, 데이터베이스가 무엇인지 이해하는데 도움이 되었으면 한다.
데이터베이스는 단지 테이블을 한데 모은 것이고, 테이블을 서로 연결(관계형 데이터베이스의 관계에 해당)시킬 수 있도록 테이블에 값이 포함되어 있다.


가장 왼쪽 탭, "Structure"는 각 테이블에 대한 메타데이터를 제공한다.
여기에는 *필드(field)*라고 불리는 열이 기술된다. (데이터베이스 테이블 행은 *레코드(record)*라고 부른다.)
"Structure" 뷰를 쭉 따라 내려가면, 필드 목록, 필드 라벨, *자료형(type)*이 나타난다.
각 필드는 한가지 유형의 자료형(숫자형 혹은 문자형) 데이터를 포함한다. 
`surveys` 테이블에는 거의 대부분 필드가 숫자형(정수형)인 반면에 `species` 테이블은 거의 모두 텍스트형이다.

"Execute SQL" 탭이 지금은 휑하니 비워져 있다 - 이곳이 데이터베이스에서 정보를 조회하는데 필요한 질의문(쿼리문)을 타이핑하는 곳이다.


요약하면: 

* 관계형 데이터베이스는 데이터를 필드(열)와 레코드(행)로 구성된 테이블로 저장한다.
* 테이블에 데이터는 자료형이 있는데, 필드에 모든 값은 동일한 자료형([자료형 세부사항](#datatype))을 갖는다.
* 질의문을 통해 데이터를 찾아내고 열을 기반으로 연산작업을 수행한다.

## 데이터베이스 설계

* 모든 행-열 조합은 단일 *원자*값을 담게 된다. 즉, 별도로 작업되는 부분을 포함하고 있지 않음.
* 정보 유형마다 필드는 하나.
* 중복 정보는 없음.
    * 정보 범주마다 구성된 테이블 한개를 다른 테이블과 쪼갠다.
    * 식별자가 필요: 테이블 사이 공통된 식별자를 통해 다시 연결(foreign key)한다.

## 가져오기

질의문을 스스로 작성해서 시작하기 전에, 데이터베이스를 혼자 힘으로 생성시킨다.
앞서 다운로드 받은 `csv` 파일 3개로 데이터베이스를 생성한다.
현재 열린 데이터베이스를 닫고나서, 다음 지시절차를 따른다:


1. 새로 데이터베이스를 생성: **Database -> New Database**
2. 가져오기 실행: **Database -> Import**
3. 가져오기할 파일 선택
4. 파일명(surveys, species, plots)과 매칭되는 테이블 이름 부여, 혹은 기본디폴트설정으로 놔두면 `SQLite Manager`가 자동으로 테이블명을 부여함.
5. 첫번째 행이 칼럼명을 갖는다면, *First row contains column names* 를 체크하여 선택한다.
6. 구분자와 인용부호 선택옵션이 CSV 파일에 잘 반영되었는지 확실히 한다.  ‘Ignore trailing Separator/Delimiter’ *선택되지 않음*을 확인한다.
7. **OK**를 클릭한다.
8. 데이블을 변경하시겠습니까? 질의가 들어오면, **OK**를 클릭한다.
9. 각 필드별로 자료형을 설정한다: `species_id`, `genus`, `sex` 같은 필드는 TEXT로 설정, `day`,
   `month`, `year`, `weight` 같은 필드는 INTEGER로 설정한다.

> ### 도전과제
>
> `plots`, `species` 테이블을 가져온다.

기존 테이블에 신규 데이터를 덧붙이는데도 동일한 작업흐름을 사용한다.

## 데이터를 기존 테이블에 추가

1. Browse & Search -> Add
1. 데이터를 `csv` 파일에 추가하고 덧붙이기(`append`) 실행


## <a name="datatypes"></a> 자료형

|    자료형                          |        설명                                                                                              |
|------------------------------------|:---------------------------------------------------------------------------------------------------------|
| CHARACTER(n)                       | Character string. Fixed-length n                                                                         |
| VARCHAR(n) or CHARACTER VARYING(n) | Character string. Variable length. Maximum length n                                                      |
| BINARY(n)                          | Binary string. Fixed-length n                                                                            |
| BOOLEAN                            | Stores TRUE or FALSE values                                                                              |
| VARBINARY(n) or BINARY VARYING(n)  | Binary string. Variable length. Maximum length n                                                         |
| INTEGER(p)                         | Integer numerical (no decimal).                                                                          |
| SMALLINT                           | Integer numerical (no decimal).                                                                          |
| INTEGER                            | Integer numerical (no decimal).                                                                          |
| BIGINT                             | Integer numerical (no decimal).                                                                          |
| DECIMAL(p,s)                       | Exact numerical, precision p, scale s.                                                                   |
| NUMERIC(p,s)                       | Exact numerical, precision p, scale s. (Same as DECIMAL)                                                 |
| FLOAT(p)                           | Approximate numerical, mantissa precision p. A floating number in base 10 exponential notation.          |
| REAL                               | Approximate numerical                                                                                    |
| FLOAT                              | Approximate numerical                                                                                    |
| DOUBLE PRECISION                   | Approximate numerical                                                                                    |
| DATE                               | Stores year, month, and day values                                                                       |
| TIME                               | Stores hour, minute, and second values                                                                   |
| TIMESTAMP                          | Stores year, month, day, hour, minute, and second values                                                 |
| INTERVAL                           | Composed of a number of integer fields, representing a period of time, depending on the type of interval |
| ARRAY                              | A set-length and ordered collection of elements                                                          |
| MULTISET                           | A variable-length and unordered collection of elements                                                   |
| XML                                | Stores XML data                                                                                          |


## <a name="datatypediffs"></a> SQL 자료형 빠른 참조

데이터베이스 시스템 별로 서로 다른 정의를 자료형에 대해 사용된다.

다음 표에는 다양한 데이터베이스 제품별로 가장 많이 사용되는 자료형 명칭이 나타나 있다:

|   자료형                                                | Access                    | SQLServer            | Oracle             | MySQL          | PostgreSQL    |
|:--------------------------------------------------------|:--------------------------|:---------------------|:-------------------|:---------------|:--------------|
| boolean                                                 | Yes/No                    | Bit                  | Byte               | N/A            | Boolean       |
| integer                                                 | Number (integer)          | Int                  | Number             | Int / Integer  | Int / Integer |
| float                                                   | Number (single)           | Float / Real         | Number             | Float          | Numeric       |
| currency                                                | Currency                  | Money                | N/A                | N/A            | Money         |
| string (fixed)                                          | N/A                       | Char                 | Char               | Char           | Char          |
| string (variable)                                       | Text (<256) / Memo (65k+) | Varchar              | Varchar / Varchar2 | Varchar        | Varchar       |
| binary object	OLE Object Memo	Binary (fixed up to 8K)   | Varbinary (<8K)           | Image (<2GB)	Long | Raw	Blob          | Text	Binary | Varbinary     |

이전: [Index](../index.html) 다음: [질의문(쿼리) 기초.](01-sql-basic-queries.html)
