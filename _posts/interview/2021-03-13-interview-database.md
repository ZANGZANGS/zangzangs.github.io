---
layout: post
current: post
thumbnail: 'assets/images/thumbnail/meeting.png'
cover:
navigation: True
title: '[Interview] 데이터베이스'
date: 2021-03-12 10:18:00
tags: [interview]
class: post-template
subclass: 'post'
author: zangzangs
---

### Database 기술면접 준비

---

#### | 트랜잭션
* 데이터베이스 내에 하나의 그룹으로 처리해야하는 명령문을 모아놓은 작업단위 또는 데이터 처리의 한 단위

#### | ACID, 트랜잭션의 특징
* Atomicity (원자성) 
  트랜잭션과 관련된 일은 모두 실행되던지 모두 실행되지 않도록 하던지를 보장하는 특성이다.
* Consistency (일관성)
   트랜잭션이 성공했다면, 데이터베이스는 그 일관성을 유지해야 한다. 일관성은 특정한 조건을 두고, 그 조건을 만족하는지를 확인하는 방식으로 검사할 수 있다.
* Isolation (독립성)
  독립성. 트랜잭션을 수행하는 도중에 다른 연산작업이 끼어들지 못하도록 한다. 임계영역을 두는 것으로 달성할 수 있다.
* Durability (지속성)
  성공적으로 트랜잭션이 수행되었다면, 그 결과는 완전히 반영이 되어야 한다. 완전히 반영되면 로그를 남기게 되는데, 후에 이 로그를 이용해서 트랜잭션 수행전 상태로 되돌릴 수 있어야 한다. 때문에 트랜잭션은 로그저장이 완료된 시점에서 종료가 되어야 한다.

#### 데이터베이스 언어의 종류 3가지

* DDL(Data Definition Language)
  데이터베이스를 정의하는 언어이며, 데이터리를 생성, 수정, 삭제하는 등의 데이터의 전체의 골격을 결정하는 역할을 하는 언어 입니다.
  
  |종류|역할|
  |---|---|
  |CREATE|데이터베이스, 테이블 등을 생성하는 역할을 합니다.|
  |ALTER|테이블을 수정하는 역할을 합니다.|
  |DROP|데이터베이스,테이블을 삭제|
  |RENAME|데이터베이스의 객체 이름을 변경|
  |COMMENT|데이터에 주석 등을 추가한다.|
  |TRUNCATE|테이블을 초기화 한다|


* DML(Data Manipulation Language)
  정의된 데이터베이스에 입력된 레코드를 조회하거나 수정하거나 삭제하는 등의 역할을 하는 언어를 말합니다.
  |종류|역할|
  |---|---|
  |SELECT|데이터를 조회하는 명령어|
  |INSERT|데이터 삽입하는 명령어|
  |UPDATE| 데이터를 수정하는 명령어|
  |DELETE|데이터를 삭제하는 명령어|
  |MERGE|데이터가 테이블에 없으면 INSERT, 있다면 UPDATE를 수행한다. UPSERT 라는 별명이 있다.|


* DCL(Data Control Language)
  데이터베이스에 접근하거나 객체에 권한을 주는등의 역할을 하는 언어를 입니다.
  |종류|역할|
  |---|---|
  |GRANT|특정 데이터베이스 사용자에게 특정 작업에 대한 수행권한을 부여|
  |REVOKE|특정 데이터베이스 사용자에게 특정 작업에 대한 수행 권한을 박탈, 회수|

* TCL(Transaction Control Language)
  데이터의 보안, 무결성, 회복, 병행 수행제어 등을 제어하는 역할을 하는 언어입니다.

  |종류|역할|
  |---|---| 
  |COMMIT|트랜잭션의 작업 결과를 저장 반영한다.|
  |ROLLBACK| 데이터베이스를 마지막 COMMIT 시점으로 복원|
  |SAVEPOIN| ROOLBACK시 저장위치까지 트랜잭션의 일부만 롤백 할 수 있다.|
  |SET TRANSACTION|transaction 지정|

* 오라클 힌트 (Oracle Hint)
오라클 옵티마이저에게 SQL문 실행을 위한 데이터를 스캐닝하는 경로, 조인하는 방법 등을 알려주기 위해 SQL 작성자가 직접 작성해주는 구문이다.

* 옵티마이저란?



* 정규화

* 튜플

* 카디널리티
  

* JOIN

* GROUP BY, HAVING

* NoSQL
  전통적인 데이터베이스 RDBMS 보다 덜 제한적인 모델을 이용한다.
* 
* 