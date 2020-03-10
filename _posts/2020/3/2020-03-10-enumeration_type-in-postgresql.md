---
layout: post
title:  "PostgreSQL의 Enumeration type"
date:   2020-03-10 20:21:00 +0900
categories: [db]
tags : [postgresql]
---
PostgreSQL의 Enumeration type에 대해서 알고 있는 내용과 알게된 내용을 정리하려고 한다.

<!--more-->

PostgreSQL 11 버전을 사용하며 이를 기준으로 작성한다.

우리가 흔히 사용하는 Enum Type(Enumeration Type, Enumerated Type)의 사용방법과 정렬방법 등에 대해서 알게된 내용을 간단하게만 정리하려고 한다.

사용방법은

```sql
CREATE TYPE schema.name AS ENUM('elem1', 'elem2', , 'elem3', [...]);
```

이다

이 때, PostgreSQL의 ENUM은 정의 순서대로 우선순위를 할당한다.

즉, 위에서는 `'elem1'`, `'elem2'`, `'elem3'` 순으로 우선순위가 정해진다.

이 우선순위에 관한 것을 실제 사용예제와 함께 살펴보자

```sql
-- Enum Type 정의
CREATE TYPE public.priority AS ENUM('High', 'Medium', 'Low');


-- 예제용 테이블 작성
CREATE TABLE IF NOT EXISTS public.todo_list(
   job_name TEXT,
   priority public.priority
);


-- 데이터 삽입
INSERT INTO public.todo_list (
  job_name, priority
) VALUES
 ('job1', 'High'::public.priority),
 ('job2', 'Medium'::public.priority),
 ('job3', 'Low'::public.priority);
```

이렇게 있을 때, `priority`로 정렬을 해보면

```sql
/*
  job_name, priority
  job1,     High
  job2,     Medium
  job3,     Low
*/
SELECT * FROM public.todo_list ORDER BY priority ASC;

/*
  job_name, priority
  job3,     Low
  job2,     Medium
  job1,     High
*/
SELECT * FROM public.todo_list ORDER BY priority DESC;
```

이렇게 결과가 나오게 된다.

[공식문서](https://www.postgresql.org/docs/11/datatype-enum.html)의 [Enum type Ordering](https://www.postgresql.org/docs/11/datatype-enum.html#id-1.5.7.15.6)에서도 설명하고 보여주고 있듯이 결과가 나오게 된다.

즉 PostgreSQL의 Enum Type은 Ordering이 선언한 순서대로 우선순위를 가지게 되며, `ORDER BY`를 통해 정렬을 했을 때 위의 예제 코드와 같이 결과가 나오게 된다.

또한 Enum Type은 Case Sensitive, 즉 대소문자를 구별하기 때문에 `High`와 `HIGH`는 서로 다른 타입이 되게 된다. 이 점을 유의하자.