---

layout: post
Title: 학원 수업과 병행한 독학 리액트 
tags: [study]
categories: React

---

### React 독학 16 mogoose 를 이용한 MongoDB 연동 실습

오널의 공부는 이전에 Node.js 서버와 MongoDB 를 연동할 수 있도록 기초 지식을 알아볼 것이다. 그리고 mongoose 를 이용해 서버에 직접 데이터를 CRUD 하는 방법도 알아볼 것이다. 

<br/>

#### 간단 소개 

MongoDB 는 문서 지향적 NoSQL 데이터베이스이다. 이 데이터베이스에 등록하는 데이터들은 유동적인 스키마 _(데이터베이스에 어떤 형식의 데이터를 넣을지에 대한 정보)_ 를 지닐 수 있다. 종류가 같은 데이터라고 하더라도, 새로 등록해야 할 데이터 형식이 바뀐다고 하더라도 기존 데이터를 수정할 필요는 없다. 서버의 데이터 양이 늘어나면 여러 컴퓨터에서 분산하여 처리할 수 있도록 확장이 쉽게 설계되어있다. 

<br/>

**문서란?**

문서 지향적인 데이터베이스에서의 문서는 record 와 개념이 비슷하다. 문서의 데이터 구조는 키 - 값 의 구조로 되어 있다. 또한 문서는 바이너리 형태의 JSON 으로 저장이 되기 때문에 나중에 JSON 형식의 객체를 데이터베이스에 저장할 때 큰 공을 들이지 않고도 데이터를 등록할 수 있다.

새로운 문서를 만들면 `_id` 라는 고윳값을 자동으로 생성한다. 이 값은 시간, 머신 아이디, 프로세스 아이디, 순차 번호로 되어 있어 값의 고유함을 보장한다. 

여러 문서가 들어있는 곳을 컬렉션이라고 한다. MongoDB 는 다른 스키마를 가지고 있는 문서들이 한 컬렉션에서 공존할 수 있다. 

<br/>

**MongoDB의 구조**

![image](https://user-images.githubusercontent.com/89691274/139242240-20247556-ac79-493e-bfb3-3c8a635f6efb.png)

MongDB 의 구조는 위 사진과 같다. 서버 하나에 데이터베이스를 여러 개 가지고 있고, 그 데이터베이스 안에는 여러 개의 컬렉션이 있으며, 컬렉션 안에는 여러 개의 문서가 들어 있다!

<br/>

**스키마 디자인**

MongoDB 에서 스키마를 디자인하는 방식은 다른 DB 와는 완전히 다르다. 블로그 프로젝트를 구성한다고 할 때, MySQL 이나 OracleDB 같은 관계형 데이터베이스에서는 아래와 같은 구조로 스키마를 디자인한다.

![image-20211028195725878](/Users/givvemee/Library/Application Support/typora-user-images/image-20211028195725878.png)

하지만 NoSQL 에서는 모든 것을 문서 하나에 넣는다. 

```json
{
  _id: ObjectIDd,
  title: String,
  body: String,
  username: String,
  createdDate: Data,
  comments: [
    {
      _id: ObjectId,
      text: String,
      createdDate: Date
    }
  ]
}
```

MongoDB 는 댓글을 포스트 문서 내부에 넣는다. 문서 내부에 또 문서가 위치할 수 있는데, 이를 _서브다큐먼트_ 라고 한다. 

문서 하나에는 최대 16MB 의 데이터를 넣을 수 있는데, 100자의 댓글은 보통 .24KB 를 차지한다. 그러므로 약 17000 개의 데이터를 넣을 수 있는 셈이다. 만일 이 용량을 초과할 가능성이 있다면 컬렉션 자체를 분리 시키는 것이 좋다.

<br/>

<br/>

#### MongoDB 서버 준비

서버를 준비하기 위해서는 설치를 해야 한다. 나는 macOS 사용자이므로 Homebrew 를 이용하여 설치할 것이다.

`brew tap mongodb/brew`

`brew install mongodb-community@4.2`

`brew services start mongoldb-community@4.2`

인데 ... 와 안 돼서 돌아버리는 줄 알았음. 이거 두 시간만에 

<br />

<br />

