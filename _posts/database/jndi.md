---
layout: post
current: post
cover: 'assets/images/waves.jpg'
navigation: True
title: 제목을 입력합니다
date: 2021-03-12 10:18:00
tags: fiction
class: post-template
subclass: 'post'
author: zzs
---

# JNDI(Java Naming and Directory Interface)



데이터베이스  Connection을 WAS Server에서 제어하면서 하나의 커넥션 풀을 가진다.



## 장점

데이터베이스 설정정보를 관리가 쉽다.

어플리케이션 레벨에서 데이터베이스 Connection에 필요한 설정 정보들을 설정해 놓는다. 

어플리케이션은 하나이더라도 여러 종류의 데이터베이스를 사용할 수 있다는 점에서 해당 어플리케이션 개발자가 아니라면 정보를 찾는데 꽤나 시간이 걸린다.

커넥션 풀을 효율적으로 사용할 수 있다.

'>>' 해당 내용 이해 안됨.. https://eongeuni.tistory.com/43

보안상의 이점이 있다. JNDI를 사용하게 되면 DB Connection 정보를 일반 개발자에게 노출되지 않는다.



https://zunoxi.github.io/infra/2020/06/20/infra-db-jdbc/







# Refference

https://eongeuni.tistory.com/43

