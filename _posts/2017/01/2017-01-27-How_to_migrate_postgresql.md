---
layout: post
title:  "postgreSQL dump(migration) 하기 "
date:   2017-01-27 18:30:00 +0900
categories: [db]
tags : [postgresql]
---

postgreSQL의 dump를 하는 방법 (migration)

그리고 복구하는 방법을 알아보자

<!--more-->

postgreSQL의 dump 하는 방법은 여러가지가 있다.

설명은 postgreSQL 9.6 이상 버전 기준으로 한다.

dump를 할 때는 postgreSQL을 설치하면 제공해주는

`pg_dump`를 이용해서 하면 된다.

{% highlight shell %}
 pg_dump [database] > migration.sql
{% endhighlight %}

가장 간단하게 하는 것은 이렇게 하면 된다.

pg_dump의 옵션은

  1.  `-a`, `--data-only`  :  data만 dump
  1.  `-b`, `--blobs`      :  매우 큰 object일 때, `--schema`, `--table`, `--schema-only` 일 때 기본으로 동작함
  1.  `-c`, `--clean`      :  dump 뜰 때 database를 drop
  1.  `-C`, `--create`     :  DDL을 줌 (CREATE ~~ )
  1.  `-E [encoding]`, `--enconding=[encoding]` : encoding을 바꿔서 줌

 등등


열거하기가 너무 많아서 가장 자주 쓰일만한 것들 위주로만 설명한다 ~~죄다 영어기도 하다~~


{% highlight shell %}
 pg_dump mydb -s > migration.sql # schema만
 pg_dump mydb -s -t mytable1 -t mytable2 > migration.sql # 선택한 table의 schema만
 pg_dump mydb -a -t mytable1 -t mytable2 > migration.sql # 선택한 table의 data만
{% endhighlight %}


가장 자주 쓰일만한 옵션들만 위주로 했다

설명하지 않은 옵션들은

[PostgreSQL pg_dump docs](https://www.postgresql.org/docs/current/static/app-pgdump.html)를 보면 자세히 설명하고 있다.~~다 영어인 건 함정아닌 함정~~