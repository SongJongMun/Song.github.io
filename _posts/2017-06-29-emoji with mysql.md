---
layout: post
title:  "emoji with mysql"
categories: [DB,mysql,emoji]
tags: [DB,mysql,emoji]
description: insert emoji to mysql
---
### 발단

어제 오전 주간회의 때 팀원분께서 Emoji를 DB에 저장해야 하는데.... 안 넣어진다고 하심

DB의 charset이 utf8이여서 안된다는것을 알고 utf8mb4로 바꿔달라고 DBA분께 말씀을 드렸으나

해당 DB의 다른테이블이 utf8을 쓰고 문자를 바꾸려고 하는 테이블이 해당 테이블의 문자형식을 바꾸게 되면 다른 테이블이나 DB에 어떤 영향이 갈지 모르기 때문에 안해주겠다... 라는 말을 했다고 함

결국 따로 DB를 만들거나 개인 DB에서 테스트 하기로 결정

근데 왜 utf8은 안되고 utf8mb4는 Emoji 입력이 가능한지 궁금해서 찾아봄...

# utf8 / utf8mb4

http://en.wikipedia.org/wiki/Plane_(Unicode)

를 참고하면 utf8과 utf8mb4 차이는 다음과 같이 나온다.

* utf8 : Basic Plane.
* utf8mb4 : Basic Plane + Supplementary Plane.

그리고 중요한거 하나 더

`Emoji 문자열은 모두 4Byte이다`<br>
<br>
따라서 SMP(Supplementary Multilingual Plane) 를 처리하기 위해서

DBMS 가 utf8mb4 를 지원한다면 반드시 이것으로 지정해야 한다.

# 근데 왜 utf8은 지원못함?

## MySQL 5.0과 같이 저버전을 사용하게되면...

MySQL은 기본적으로 UTF-8이 `3Byte까지만` 지원하기때문에
`4Byte로 구성된 Emoji와 같은 문자`가 저장되면 빈 값으로 저장되거나 ?? 등으로 저장된다.