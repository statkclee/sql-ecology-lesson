---
layout: lesson
root: .
lastupdated: April 16, 2015
contributors: ["Ethan White","Greg Wilson","Josh Herr","Sophie Clayton","Tracy Teal", "Aleksandra Pawlik"]
maintainers: ["Paula Andrea Martinez", "Timothée Poisot"]
domain: Ecology
topic: SQL
software: SQL
dataurl: http://dx.doi.org/10.6084/m9.figshare.1314459
status: Teaching
---

<!-- USING THIS LESSON TEMPLATE -->
<!-- Lesson specific information is taken from the YAML header at the top of the page -->

<!-- THE LESSON INFORMATION -->

<!-- Get the information from _data/info.yml -->

# 데이터 카펜트리 SQL 학습교재

데이터 카펜트리 목표는 과학기술 연구원들에게 데이터로 작업하는데 필요한 
기본적인 개념, 기술, 도구사용법을 전수해서, 연구원들이
더 빠른 시간내, 더 적은 고통으로, 더 많은 것을 수행하게 한다.
다음 학습교재는 [Data Carpentry SQL for Ecology](http://www.datacarpentry.org/sql-ecology-lesson/)을 
번역한 것이다.


**SQL 교안 기여자 :  {{page.contributors | join: ', ' %}}**


**SQL 교안 유지보수 담당자: {{page.maintainers | join: ', ' %}}**

**SQL 교안 한국어 번역: 이광춘**

#### 학습교안 상태: {{ page.status }}
<!--
  [Information on Lesson Status Categories]()
-->

<!-- ###### INDEX OF LESSONS ON THIS TOPIC ###### -->

## 학습교재:

|       영문                      |                국문                  |
|---------------------------------|--------------------------------------|
| 1. [Lesson 00 Introduction to SQL](00-sql-introduction.html) | 1. [00. SQL 소개](kr/00-sql-introduction.html)|
| 2. [Lesson 01 Basic queries](01-sql-basic-queries.html)      | 2. [01. 질의문(쿼리) 기초](kr/01-sql-basic-queries.html)|
| 3. [Lesson 02 Aggregation](02-sql-aggregation.html)          | 3. [02. 집계](kr/02-sql-aggregation.html)|
| 4. [Lesson 03 Joins and aliases](03-sql-joins-aliases.html)  | 4. [03. 병합(Join)과 별칭](kr/03-sql-joins-aliases.html)|

## 데이터

학습에 사용되는 데이터는 [{{page.dataurl %}}]({{page.dataurl %}}) 사이트에서 다운로드 받아 둔다.


### 사전 준비물

데이터 카펜트리는 직접 키보드에 손을 올려 실습하는 것으로 워크샵 참석자분들이 
직접 본인 컴퓨터(노트북)를 자져와서 효율적인 작업흐름이 되도록 적절한 도구를 설정해서 준비해 와야 된다.
*이번 학습과정에 사전 기술과 도구사용법에 대한 지식이 전혀 없다고 가정한다.*
하지만, 아래에 기술된 소프트웨어에 대한 사본을 컴퓨터에 준비하는 것은 필요하다.
학습교재를 워크샵에서 최대한 효과적으로 활용하기 위해서 수업 *전에* 
모든 것이 제대로 설치되어 준비되었는지 확인하면 좋다.


{% if page.software == "Python" %}
{% include pythonSetup.html %}
{% elsif page.software == "Spreadsheets" %}
{% include spreadsheetSetup.html %}
{% elsif page.software == "R" %}
{% include rSetup.html %}
{% elsif page.software == "SQL" %}
{% include sqlSetup.html %}
{% else %}
{% include anySetup.html %}
{% endif %}

<p><strong>트위터</strong>: @datacarpentry
