
# MySqlTutorial
## 설치
윈도우 : [링크](http://itgroovy.tistory.com/793)  
centOS : [링크](https://zetawiki.com/wiki/CentOS_MySQL_%EC%84%A4%EC%B9%98)  

## SQL이란
SQL이란 Structured Query Language의 약자로, 구조화된 질의 언어라는 뜻이다. 처음 이 `구조화된 쿼리(질의) 언어`를 들으면 뭔 소린가 싶다. 사실 필자도 SQL이 대체 어느부분이 구조적이라는 건지는 잘 모르겠다. 다만 SQL이란.. 일반 프로그래밍 언어처럼 if else의 연속이 아니라 `SELECT * FROM DUAL;`과 같이 문장형 언어겠구나~ 싶은 감은 갖고 있다.  
어찌됐든 중요한건 SQL이란건 RDBMS를 효과적으로 만들고 조작하기 위해 만들어진 언어라는 것이다.  

## RDBMS란
RDBMS란 Relative Database Management System의 약자로, 관계형 데이터베이스를 의미한다. `관계`라는 단어가 처음에는 생소할 수 있다. 따라 유정하우스의 서열관계를 나타내는 DB를 예로 들어 설명하겠다.  

### 테이블
우선 유정하우스의 멤버는 이동규, 전유정, 남혜미, 김아정 총 4명이 있다. 이를 테이블로 표현해보겠다.  
  
`TBL_USERS`  

|NAME|
|----|
|이동규|
|전유정|
|남혜미|
|김아정|

  
`TBL_USERS`는 테이블 이름이다.  

### 서열관계 테이블
사실 서열관계는 1:N 관계가 아니라서 다음과 같이 표현할 수 있다.  

|NAME|RANK|
|----|----|
|이동규|1|
|전유정|2|
|남혜미|3|
|김아정|4|

  
유정하우스의 서열순위를 명확히 표현한 테이블이다.  
비슷한 방법으로 사용자에 대해 저장해야할 정보들이 더 있다면 얼마든지 추가할 수 있다.  

NAME|RANK|HOME
----|----|----
이동규|1|서울
전유정|2|논산
남혜미|3|인천
김아정|4|TK

  
하지만 다음과 같은 경우는 어떻게 할까?  
`유정하우스 멤버가 놀러갔던 장소들을 나타내는 테이블을 만들어보세요`  
  
그럼 갑자기 곤란해진다. 각 멤버가 놀러간 장소가 한두곳이 아니기 때문이다.  
우선 기존의 방식으로 표현하자면  

NAME|PLAYGROUND
----|----
이동규|건대,홍대
전유정|논산,딸기밭
남혜미|인천,국제공항
김아정|대구,뭐있냐;

  
이런식으로 만들 수 있겠다. 하지만 이는 좋은 방법이 아니다. SQL은 일반 프로그래밍 언어처럼 유연하지가 못해서 PLAYGROUND 열의 `건대,홍대` 중 건대만 없애봐! 라는 명령이 쉽지않다. (일반 자바프로그래밍에서도 이는 매우 귀찮은 작업이다. 문자열 '건대,홍대'에서 '건대'를 지우고 ',' 또한 지우는 예외처리는 상당히 귀찮다.)  
  
그래서 우리는 이와같은 구조를 1:N 구조라고 부른다. 동규라는 사람이 `1`명, 그 사람이 놀러간 곳은 `N`곳이기 때문이다. 그리고 관계형 테이블을 적극 활용한 테이블을 보여주겠다.  

`TBL_USERS`  

NAME|
----|
이동규|
전유정|
남혜미|
김아정|

  
`TBL_USER_PLAYGROUNDS`  

USER_NAME|PLAYGROUND
----|-------------|
이동규|건대
이동규|홍대
전유정|논산
전유정|딸기밭
남혜미|인천
남혜미|국제공항
김아정|대구
김아정|뭐있냐;

  
얼핏보면 이게 뭐지 싶을 수 있다. 하지만 이와 표현한 이유가 있다. 바로 `Foreign Key` 때문이다.  
가볍게 설명하자면 `TBL_USER_PLAYGROUNDS`라는 테이블의 `USER_NAME` 열은 `TBL_USER`의 `NAME`과 연결되어 있다. `Foreign Key`의 장점은 만약 유정하우스에서 누군가 탈퇴하게되어 `TBL_USER`의 한 column(열) 즉 이동규 column이 삭제되었을 때 연결된 `TBL_USER_PLAYGROUNDS`의 `USER_NAME`이 이동규인 row(행)는 모두 삭제된다. 이는 데이터 관리에 있어서 진짜진짜 편한 기능이다.  
  
하지만 저 테이블이 아직 완전한 형태는 아니다. 만약 유정하우스에 19학번 이동규도 가입했다고 치자. 그러면 `TBL_USERS` 테이블은 다음과 같이 바뀔 것이다.  

|NAME|
|----|
|이동규|
|전유정|
|남혜미|
|김아정|
|동규|

  
문제를 알겠는가? 이동규가 둘이다. 하나여도 극혐인데 둘이면 이는 매우 곤란하다.  
게다가 `TBL_USER_PLAYGROUNDS`는 더 가관이다.  

USER_NAME|PLAYGROUND
----|-------------|
이동규|건대
이동규|홍대
전유정|논산
전유정|딸기밭
남혜미|인천
남혜미|국제공항
김아정|대구
김아정|뭐있냐;
이동규|계룡
이동규|너무조와

  
대체 어떤 이동규가 건대, 홍대에서 노는 민간인인지, 어떤 이동규가 계룡에서 노는 군바리인지 구분할 수가 없다. 어쩌면 홍대, 계룡에서 노는 혼종일수도 있다. 이는 아무도 모른다.  
  
이와 같은 문제를 해결하기 위해서 SQL은 `Primary Key`를 지원한다. 우선 개선된 테이블을 먼저 보이겠다.  
`TBL_USERS`

ID|NAME|  
---|----|  
20162489|이동규|  
20161818|전유정|
20161234|남혜미|
20169876|김아정|
20190001|이동규|
  
눈치 빠른 사람이라면 이미 알겠지만 유니크한 `ID`가 추가됨으로써 동명이인도 구분할 수 있게 되었다. 바로 `ID` column이 `Primary Key`로 지정된 column 이다. 말 그대로 한 row를 대표하는 column인 것이다.  
덕분에 `TBL_USER_PLAYGROUNDS`도 다음과 같이 개선되었다.  

USER_NAME|PLAYGROUND
----|-------------|
20190001|건대
20190001|홍대
20161818|논산
20161818|딸기밭
20161234|인천
20161234|국제공항
20169876|대구
20169876|뭐있냐;
20162489|계룡
20162489|너무조와

  
이제야 군바리 이동규를 찾아낼 수 있게 되었다.  

## MySQL
SQL의 개념을 알게되었으니 이제 MySQL을 써보자  

### 데이터베이스 만들기
우선 MySQL 명령어를 입력할 수 있는 터미널까지는 세팅이 되어 있을 것이다.  
아래 명령어를 차례로 입력하자
```
CREATE DATABASE TUTORIAL;
SHOW DATABASES;
```

그럼 방금 만든 TUTORIAL 데이터베이스가 보일 것이다.  
아래 명령어는 TUTORIAL 데이터베이스를 사용하겠다는 것이다.
```
USE TUTORIAL;
```

곧이어서 다음 명령어를 입력해보자
```
SHOW TABLES;
```

그럼 `Empty set`이 포함된 메세지가 뜰 것이다.  
당연하겠지만 테이블이 없다는 뜻이다.  

### 테이블 만들기
우리는 현재 `TBL_USERS`와 `TBL_USER_PLAYGROUNDS` 두 개의 테이블이 필요하다.  
두 테이블은 아래와 같은 모양을 하고 있다.

ID|NAME|  
---|----|  
20162489|이동규|  
20161818|전유정|
20161234|남혜미|
20169876|김아정|
20190001|이동규|

USER_NAME|PLAYGROUND
----|-------------|
20190001|건대
20190001|홍대
20161818|논산
20161818|딸기밭
20161234|인천
20161234|국제공항
20169876|대구
20169876|뭐있냐;
20162489|계룡
20162489|너무조와

이런 모양의 테이블을 만드는 SQL문은 다음과 같다.
```
CREATE TABLE tbl_users (id VARCHAR(8) not null, name VARCHAR(20) not null, PRIMARY KEY (id));
CREATE TABLE tbl_user_playgrounds (user_id VARCHAR(8) not null, playground TEXT not null, FOREIGN KEY (user_id) REFERENCES tbl_users (id) ON DELETE CASCADE ON UPDATE CASCADE);
```

우선 TBL_USERS를 만드는
```
CREATE TABLE tbl_users (id VARCHAR(8) not null, name VARCHAR(20) not null, PRIMARY KEY (id));
```
부터 보자.  
`CREATE TABLE`은 테이블을 만들겠다는 의미로, `CREATE TABLE tbl_users`는 이름이 tbl_users인 테이블을 만들겠다는 뜻이다.  
벌써부터 느낌이 오겠지만 그냥 영어 문장을 쓰는 느낌이 강하다.  
괄호안의 내용은 tbl_users의 구조가 담겨있다. 각각의 column을 명시하며 각 column은 `,`로 구분된다.  
```
id VARCHAR(8) not null
```
이는 우리가 컬럼을 만들건데~ 이름은 id, 자료형은 varchar, 그리고 널값은 들어갈 수 없어! 라는 뜻이다.  
그리고 varchar에는 최대 8글자까지 들어갈거야~ (byte 수가 아니다.)  
즉 varchar에는 최대 8글자짜리 문자열이 들어갈 수 있는 것이다.
```
name VARCHAR(20) not null
```
같은 방법으로 해석하자면 이름은 name, 자료형은 20글자 짜리 varchar, 널값은 허용하지 않는 컬럼이 만들어 진것이다.  
```
PRIMARY KEY (id)
```
마지막 이놈은 이 테이블에서 id가 PRIMARY KEY야! 라는 뜻으로 이해하기 쉽다.  
하나 주의해야 할 것은 PRIMARY KEY로 지정된 column은 중복될 수 없다. 그래서 학번이나 주민등록번호 같이 중복될 일이 없는 값이 들어갈 column으로 신중히 결정해야한다.  
  
그 다음 테이블을 보겠다.
```
CREATE TABLE tbl_user_playgrounds (user_id VARCHAR(8) not null, playground TEXT not null, FOREIGN KEY (user_id) REFERENCES tbl_users (id) ON DELETE CASCADE ON UPDATE CASCADE);
```

마찬가지로
```
CREATE TABLE tbl_user_playgrounds
```
tbl_user_playgrounds라는 이름의 테이블을 만들게! 라는 뜻이고  

```
user_id VARCHAR(8) not null
```
이름은 user_id, 자료형은 8글자짜리 varchar, 널 값은 허용하지 않는 컬럼을 만든다.

```
playground TEXT not null
```
여기서 새로운 자료형 `TEXT`를 소개한다. `TEXT`는 크기를 예측하기 어려울 때, 즉 매우 긴 문자열이 들어갈 수도 있는 column에 사용한다. 하지만 용량이 큰 만큼 속도가 매우 느리니 신중히 사용해야한다.  

```
FOREIGN KEY (user_id) REFERENCES tbl_users (id) ON DELETE CASCADE ON UPDATE CASCADE
```
꽤 길다. 하지만 하나씩 보면 별거 아니니 겁먹지 말자.  
아까 PRIMARY KEY와 같이 FOREIGN KEY로 `user_id`를 지정한다. 그리고 이 `user_id`를 어떤 테이블의 어떤 컬럼과 연결할건지 `REFERENCES`뒤에 명시하는 것이다.  
즉
```
REFERENCES tbl_users (id)
```
`tbl_users` 테이블의 `id` column와 연결하겠다! 라는 것이다.

```
ON DELETE CASCADE ON UPDATE CASCADE
```
이건 연결된 `tbl_users`의 `id`가 삭제되거나 값이 바뀌면 같이 바뀐다는 뜻인데 여기선 이해하지 않고 그냥 넘어가도 좋다.  

그럼 
```
Query OK, 0 rows affected (0.01 sec)
```
이와 같은 메세지가 뜨면서 성공을 알릴 것이다.

```
show tables;
```
명령어를 쳐보자. 우리가 만든 테이블을 확인 할 수 있다.  

### ROW(데이터) 삽입하기
이제 우리 유정하우스 멤버를 `tbl_users`에 넣을 것이다.  
멤버는 `이동규`, `전유정`, `김아정`, `남혜미`로, SQL문은 다음과 같다.

```
INSERT INTO tbl_users VALUES('20162489', '이동규');
INSERT INTO tbl_users VALUES('20161818', '전유정');
INSERT INTO tbl_users VALUES('20161234', '남혜미');
INSERT INTO tbl_users VALUES('20169876', '김아정');
commit;
```

위의 INSERT 문으로 테이블 내에 데이터를 삽입 할 수 있다.  
```
INSERT INTO tbl_users
```
tbl_users 내에 row를 삽입할거야! 라는 뜻이고  
`VALUES` 뒤에 row에 대한 값이 명시된다.
```
VALUES('20162489', '이동규')
```
잘 보자. 뭔가 이상하다. `20162489`가 ID이고, `이동규`가 이름인건 내가 봤을 때는 쉽게 알 수 있지만 MYSQL이 판단하는 근거는 무엇일까? 프로그램 입장에서는 `20162489`가 NAME일 수도 있는거 아닌가?  
답은 CREATE 문에 있다.

```
CREATE TABLE tbl_users (id VARCHAR(8) not null, name VARCHAR(20) not null, PRIMARY KEY (id));
```
테이블을 만들 때 순서를 잘 보자. id가 맨 먼저, name이 두 번째로 만들어졌다.  
따라서 값을 넣어줄 때도 id, name 순서로 값을 넣어준다.  
  
사실 아래와 같은 문장을 쓰면 그 순서도 명시할 수 있다.
```
INSERT INTO tbl_users(name, id) VALUES('김박사', '1111');
```
하지만 필자는 컬럼이 많을 때 저 컬럼 순서를 모두 적어주는게 매우 귀찮아서 잘 쓰지 않는다.  
여기까지가 튜토리얼을 위한 DB 세팅이다.  
SQL자체가 어려운건 아니지만 그 문장 구조를 단 번에 외우는 것은 쉽지 않다.  
필요할 때마다 검색이나 본문을 참고하여 반복적으로 하다보면 익숙해질 수 있다.  
그러니 부디 SQL문을 외우려하지말고 그 구조를 보며 이해하려고 하길 바란다.
