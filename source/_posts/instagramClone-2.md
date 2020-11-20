---
title: instagram Clone하기 (Backend) 02-Prisma Setting하기 (2) 
author: Hamin
date: 2020-03-28 14:51:25
tags: [node.js, prisma, graphql, typescript]
categories: 
- [Projects]
- [Backend, Prisma]
thumbnail: /gallery/prisma.png
---

저번 포스팅에 이어서 Prisma 세팅을 마무리하고 Datamodel을 만들어 보도록 하겠습니다.
이 프로젝트 내용은 {% link NomadCoders https://academy.nomadcoders.co/ [external] [title] %} 를 참고하여 진행할 예정입니다.

<!-- more -->

## 2.0. Introduction to Prisma
- prisma는 ORM (Object-relational mapping, 객체 관계 연결)
- prisma는 데이터베이스 관련한 어려운 문제들 해결해 줌.
- typeorm이나 djago orm, sequelize 같은 다른 ORM도 있음.
- prisma는 어플리케이션에 필요한 모델을 graphql로 정의할 수 있다는 점에서 특별함.
- prisma가 graphql로 정의한 모델을 이해한 후에 이런 코드들을 만들어 줌.
- User 모델에 ID, email, posts가 있음.
- npm install -g prisma
- prisma login -k mykey
- prisma init
- 기존 데이터베이스를 사용할 지 새로 만들지 선택 가능.
- 생성된 파일들을 깃허브에서 숨기고 싶다면 => .gitignore에 generated 추가.
- user model 추가 => prisma deploy.
- yarn global add prisma.
- prisma deploy => 서비스를 배포 시 자동으로 갱신.
- 데이터 모델만 변경하고 prisma deploy를 실행하면 됨.

## 2.1. Datamodel with Prisma
- 데이터 모델 추가
- prisma 기능에는 unique 기능, default 기능 있음.
- directive나 naming convention에 관한 것도 있으니 도큐먼트 참고.
- relation기능으로 following과 followers 묶기.
- prisma deploy 명령어 맘에 들지 않으니 package.json에 script를 추가.
- 이제 yarn deploy로 배포.
- endpoint는 서버라고 보면 됨.
- graphql playground 서버가 us.prisma.sh/~~ 주소에 생성 됨. 이 주소는 사용자에게 알려주면 안됨.
- 옆의 Docs를 보면 모든 명령을 실행시켜볼 수 있음. 모든 user를 조회할 수 있고, 모든 Like도 조회 가능.
- fileConnection, createComment, createFile, update.. 모든 명령을 실행시킬 수 있음.
- subscription도 mutation도 create, delete, update deleteUser 다 있음.
- 이 주소를 절대 알려줘서는 안됨. 모든 명령어를 보고 실행해볼 수 있음..
- prisma의 장점 => 데이터 모델만 작성하면 자동으로 관리 패널을 만들어 줌.

## 2.2. Testing Prisma
- create는 모든 모델에 할 수 있음. createUser, createPost 처럼 모든 모델에.
- delete는 User에만 가능. deletePost나 deleteLike는 실행 불가능.
- Room과 Message도 추가.
- messages는 message의 배열 타입.
- upsert는 update 혹은 insert 임.
- graphql 파일만 작성하면 resolver도 생성되고, 서버도 만들어지고 관리 패널도 만들어짐.

### createUser
  - command
  ~~~json
  mutation {
    createUser(data:{username:"Gerrit", email:"gerrit@yankees.com"}){
      id
    }
  }
  ~~~

  - result
  ~~~ json
  {
    "data": {
      "createUser": {
        "id": "ck77oedm81kue0905wt78x0kx"
      }
    }
  }
  ~~~

### 조회하기
  
  - command
  ~~~json
    {
      user(where:{id:"ck77ojlbt3a0t0961vm0dgsbb"}){
        username
      }
    }
  ~~~

  - result
  ~~~json
  {
    "data": {
      "user": {
        "username": "paulo"
      }
    }
  }
  ~~~

### update 하기
  - command
  ~~~json
  mutation {
    updateUser(data:{firstName:"gerrit", lastName:"Cole"}where:{id:"ck77oedm81kue0905wt78x0kx"}){
      username
    }
  }
  ~~~

  - result
  ~~~json
  {
  "data": {
      "updateUser": {
        "username": "gerrit"
      }
    }
  }
  ~~~

### following 만들기
  - command
  ~~~json
  mutation {
    updateUser(data:{following:{
      connect:
      {
        id: "ck77ojlbt3a0t0961vm0dgsbb"
      }
    }}where:{id:"ck77oedm81kue0905wt78x0kx"}){
      username
      firstName
      lastName
      following{
        id
      }
      followers{
        id
      }
    }
  }
  ~~~

  - result
  ~~~json
  {
    "data": {
      "updateUser": {
        "username": "gerrit",
        "followers": [],
        "following": [
          {
            "id": "ck77ojlbt3a0t0961vm0dgsbb"
          }
        ],
        "lastName": "Cole",
        "firstName": "gerrit"
      }
    }
  }
  ~~~
  
## 2.3. Integrating Prisma in our Server
- prisma.yml 파일 커밋하면 안됨. gitignore에 추가해 주어야.
- 만약 prisma에 문제가 생긴다면, url과 모든 서비스를 변경해야 함.
- prisma.yml에 generate라는게 있음. generator와 output이 있음.
- generated를 보면 prisma-schema 폴더에 index, prisma-schema 파일이 있는데 이 파일을 통해 prisma와 정보를 주고 받게 됨.
- 서비스를 배포할 때마다 prisma cloud에서 무언가를 생성하는데, 그것을 다운로드 받아야 함.
- prisma 서버와 정보를 주고받으려면 prisma client를 다운로드 받아야 함.
- package.json에 prisma 명령어 추가 => deploy 명령어를 수행하고 나면 generated 명령어를 수행하도록 할 것.
- yarn prisma를 해 주면 deploy가 실행되고 변경 사항들이 업로드 될 것. 그리고 나면 client가 generate 될 것.
- prisma client가 추가 됨.
- prisma와 상호작용 할 client는 javascript.
- prisma가 자동으로 client를 만들어 줌, 이 client는 사용자 정보를 체크할 수 있음.
- 이렇게 하면 데이터베이스에서 사용자 정보를 불러오고, prisma 엔드포인트를 보호할 수 있음.
- 사람들이 서버에 정보를 요청, 서버는 prisma를 요청.
- 이렇게 하는 것이 사람들이 서버에 직접 요청하도록 하는 것 보다 더 안전!
- 프론트 엔드가 서버에 요청을 하면 서버가 prisma에 요청. 서버가 중간에 있다고 볼 수 있다.

{% asset_img prismaOutline.png %}

## 2.4. Resolvers with Prisma
- models.graphql 파일을 만들어 api 아래에 이동 (api 아래의 graphql 파일만 읽을 수 있도록 했기 때문에).
- graphql은 prisma 문법을 이해하지 못하기 때문에 두 개의 파일 모두 만들어야 함. (문법 약간 다름 @들어가는거 차이).
- userById.graphql에서 model을 import 해야 함. 그렇지 않으면 이 쿼리는 User가 무엇인지 모를 것.

명령
~~~json
{
  allUsers {
    id
    firstName
  }
  userById(id:"ck73au8pi2lkr0b45ejrt0j7q"){
    firstName
  }
}
~~~

결과
~~~json
{
  "data": {
    "allUsers": [
      {
        "id": "ck733pwde25dp0b45ezb9iw9e",
        "firstName": ""
      },
      {
        "id": "ck73au8pi2lkr0b45ejrt0j7q",
        "firstName": "suhyun"
      }
    ],
    "userById": {
      "firstName": "suhyun"
    }
  }
}
~~~

- Prisma의 단점! => $fragment()라는 걸 추가해 줘야 posts 실행 가능.
  왜냐하면 공격받고 싶어하지 않기 떄문.
  아래처럼 무한한 코드를 만들 수 있고, Prisma 서버가 먹통이 될 수 있기 때문에.(post에서 post로 끝없는 코드가 나올 수 있다.)
  이런 공격을 막기 위해서 그렇다.

~~~json
{
  allUsers {
    id
    firstName
    posts {
      id
      user {
        posts {
          id
          user {
            id
            posts {
              id
              user {
                posts {
                  user {
                    id
                  }
                }
              }
            }
          }
        }
      }
    }
  }
  userById(id:"ck73au8pi2lkr0b45ejrt0j7q"){
    firstName
  }
}
~~~
  

