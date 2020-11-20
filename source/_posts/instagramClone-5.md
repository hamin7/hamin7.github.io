---
title: instagram Clone하기 - Backend Resolvers (2) 
author: Hamin
date: 2020-04-07 23:27:20
tags: [node.js, prisma, graphql, typescript]
categories: 
- [Projects]
- [Backend, Prisma]
thumbnail: /gallery/prisma.png
---

이번 포스팅에서는 editUser, seeUser, Computed Fields, upload, editPost Resolver를 만들어 보겠습니다
이 프로젝트 내용은 {% link NomadCoders https://academy.nomadcoders.co/ [external] [title] %} 를 참고하여 진행할 예정입니다.

<!-- more -->

## 3.11. editUser and seeUser Resolver

- context에 Prisma를 넣어두었으므로 prisma만 import 하면 autocomplete 된다.
- 사실 isAuthenticated를 context에 추가할 수도 있다.
- server.js에 아래와 같이 해두면
~~~javascript
import { isAuthenticated } from "./middlewares";

context: ({ request }) => ({ request, isAuthenticated })
~~~
edutUser.js에서 더 이상 아래의 것을 import하지 않아도 된다.
~~~javascript
import { isAuthenticated } from "./middlewares";
~~~

### editUser 예시

- Command
~~~json
mutation {
  editUser(bio:"I love to drink hot americano in the morning"){
    firstName
    lastName
    bio
  }
}
~~~

- HTTP header
~~~json
{"Authorization": "Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpZCI6ImNrNzd0dzQyOTFycXIwOTA1eXk1c2hpcnAiLCJpYXQiOjE1ODMyMDg4NTJ9.qAojVTOQnWH_z1Y-DvWUlZDsoJ41FfPA-UrIl-ggWNQ"}
~~~

- Result
~~~json
{
  "data": {
    "editUser": {
      "firstName": "Gianluigi",
      "lastName": "Buffon",
      "bio": "I love to drink hot americano in the morning"
    }
  }
}
~~~

- 만약 내가 firstname이나 lastname등 아무것도 보내지 않는다면 내 firstname이랑 lastname은 빈 string으로 올까?
  혹은 prisma가 이전의 것을 보존해 줄까?
  -> 이전의 것을 보존해 준다. 위와 같이...

- editUser.js에서 return 할 때 await로 리턴하지 않아도 된다.
  -> 왜냐면 return이 마지막 statement이기 때문이다.
  -> 서버가 자동으로 이 promise가 resolve 되서 브라우저에게 결과를 전달하길 기다려주기 때문.

### seeUser 예시

- Command
~~~json
{
  seeUser(id:"ck77tw4291rqr0905yy5shirp"){
    firstName
  }
}
~~~

- HTTP header
~~~json
{"Authorization": "Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpZCI6ImNrNzd0dzQyOTFycXIwOTA1eXk1c2hpcnAiLCJpYXQiOjE1ODMyMDg4NTJ9.qAojVTOQnWH_z1Y-DvWUlZDsoJ41FfPA-UrIl-ggWNQ"}
~~~

- Result
~~~json
{
  "data": {
    "seeUser": {
      "firstName": "Gianluigi"
    }
  }
}
~~~

- seeUser는 인증 필요하지 않아서 제거해버림

## 3.12. me Resolver + Prisma's Limitations
- 내 정보를 확인하고 싶으면 이론적으로 playGround에서 me를 하면 됨.
  하지만 실제로는 못함 => Prisma Client는 강한 관계를 제공하지 않기 때문.
  내가 원하는 query에 직접 관계를 맺어줘야 함
  => 웹 해킹 (공격 벡터)를 최소화 시키기 위해서 이렇게 만들어진 것.
  
### me 조회하기
- Command
~~~json
{
  me {
    posts {
      id
    }
  }
}
~~~

- HTTP header
~~~json
{"Authorization": "Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpZCI6ImNrNzd0dzQyOTFycXIwOTA1eXk1c2hpcnAiLCJpYXQiOjE1ODMyMDg4NTJ9.qAojVTOQnWH_z1Y-DvWUlZDsoJ41FfPA-UrIl-ggWNQ"}
~~~

- Result
~~~json
{
  "data": {
    "me": {
      "posts": []
    }
  }
}
~~~

- fragment.js에 있는 것만 가지고 올 수 있을까? 아니면 더 많은 정보를 가지고 오는게 가능할까?
  => 있는 것만 가져올 수 있음.
  => 엄청 깊은 관계를 가지고 있는 Query를 작성한다면, 아니면 relationship을 맺고 싶으면
    => fragment 사용하기 싫다면, $fragment가 아니라 다른 구문을 사용해야 함.

- 가끔은 ORM 쓸떄 우리가 게을러져서, user, posts, 팔로워 권한을 모두 원하게 됨.
  => me.graphql처럼 하면 됨.

### me 사용 예시 2

- Command
~~~json
{
  me {
    user {
      firstName
      lastName
      email
    }
    posts {
      id
    }
  }
}
~~~

- HTTP header
~~~json
{"Authorization": "Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpZCI6ImNrNzd0dzQyOTFycXIwOTA1eXk1c2hpcnAiLCJpYXQiOjE1ODMyMDg4NTJ9.qAojVTOQnWH_z1Y-DvWUlZDsoJ41FfPA-UrIl-ggWNQ"}
~~~

- Result
~~~json
{
  "data": {
    "me": {
      "user": {
        "firstName": "Gianluigi",
        "lastName": "Buffon",
        "email": "ggamini7@gmail.com"
      },
      "posts": []
    }
  }
}
~~~

## 3.13. See Full Posts. 

- like 수 올리기
~~~json
mutation {
  toggleLike(postId:"ck77phhmq3cdt0961jtbsz2yg")
}
~~~

- HTTP header
~~~json
{"Authorization": "Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpZCI6ImNrNzd0dzQyOTFycXIwOTA1eXk1c2hpcnAiLCJpYXQiOjE1ODMyMDg4NTJ9.qAojVTOQnWH_z1Y-DvWUlZDsoJ41FfPA-UrIl-ggWNQ"}
~~~

- Result
~~~json
{
  "data": {
    "toggleLike": true
  }
}
~~~

- seeFullPost 테스트
~~~json
{
  seeFullPost(id: "ck77phhmq3cdt0961jtbsz2yg") {
    post {
      location
    }
    comments {
      id
    }
    likeCount
  }
}

~~~HTTP header
{"Authorization": "Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpZCI6ImNrNzd0dzQyOTFycXIwOTA1eXk1c2hpcnAiLCJpYXQiOjE1ODMyMDg4NTJ9.qAojVTOQnWH_z1Y-DvWUlZDsoJ41FfPA-UrIl-ggWNQ"}
~~~

- Result
~~~json
{
  "data": {
    "seeFullPost": {
      "post": {
        "location": "New york"
      },
      "comments": [
        {
          "id": "ck7bgja9p1fgd0984bcayfn7b"
        }
      ],
      "likeCount": 1
    }
  }
}
~~~

### seeFullPost fragment로 comments의 text, user, username 넣는 예시

- Command
~~~json
{
  seeFullPost(id: "ck77phhmq3cdt0961jtbsz2yg") {
    post {
      location
    }
    comments {
      id
      text
      user {
        username
      }
    }
    likeCount
  }
}
~~~

- HTTP header
~~~json
{"Authorization": "Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpZCI6ImNrNzd0dzQyOTFycXIwOTA1eXk1c2hpcnAiLCJpYXQiOjE1ODMyMDg4NTJ9.qAojVTOQnWH_z1Y-DvWUlZDsoJ41FfPA-UrIl-ggWNQ"}
~~~

- Result
~~~json
{
  "data": {
    "seeFullPost": {
      "post": {
        "location": "New york"
      },
      "comments": [
        {
          "id": "ck7bgja9p1fgd0984bcayfn7b",
          "text": "I'm hungry",
          "user": {
            "username": "gigi"
          }
        }
      ],
      "likeCount": 1
    }
  }
}
~~~

## 3.14. Computed Fields in Prisma

### Computed || custom filed
  - me.js를 보면 UserProfile을 받게 되는데, 내가 내 User에 더하고 싶은게 있는 경우에, 예를 들면 fullName을 더해서 구하고 싶다고 해 보자. fullName은 firstName과 lastName에 의해 결정된다. 이건 database가 아니고, 사실은 User의 일부들이다. 예를들어, seeFullPost나 seeUser에서는 isLiked:Boolean 같은것을 더해주어야 한다. isLiked는 내가 사진에 좋아요를 눌렀는지 아닌지에 관한 것이다. 그리고 User에다가 예를 들어 amIFollowing도 더해줘야 한다.
  이것이 무엇이냐면 우리가 userProfile을 갈 때마다 내가 이 사람을 팔로잉하는지 알고 싶을 것이다. 그래서 amIFollowing도 있고 isLiked도 있다. 
  
  그리하여 me.js에 user resolver를, custom resolver를 더하여 이건 하나의 field만을 위한 것이다. 이것은 다른 field들에 영향을 끼치지 않을 것이다. 이렇게 되면 먼저 Prisma로 가서 찾으려고 할 것이고, 만약 Prisma에서 찾을 수가 없다면, 자신의 서버에서 찾으려고 할 것이다. 

  ### me를 이용하여 테스트 해 보기

  - Command
  ~~~json
    {
    me {
      user {
        fullName
      }
    }
  }
  ~~~

  - HTTP header
  ~~~json
  {"Authorization": "Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpZCI6ImNrNzd0dzQyOTFycXIwOTA1eXk1c2hpcnAiLCJpYXQiOjE1ODMyMDg4NTJ9.qAojVTOQnWH_z1Y-DvWUlZDsoJ41FfPA-UrIl-ggWNQ"}
  ~~~

  - Result
  ~~~json
  {
    "data": {
      "me": {
        "user": {
          "fullName": "Gianluigi Buffon"
        }
      }
    }
  }
  ~~~

  request에는 user가 있고, 기술적으로 내가 접근하고 싶은곳은 어디든 접근할 수 있다. Prisma에도 접근하고, request도 할 수 있다. 여기서 parent라는 것이 있다. 보통 parent가 하는것은 위에 있는 resolver를 준다. 
  ~~~json
    {
    me {
      user {
        fullName
      }
    }
  }
  ~~~
  예를 들어 fullName의 parent는 user이다. 만약 user의 parent를 console.log한다면 undefined가 나올 것이다. 왜냐하면 그것은 그냥 me로 query이기 때문이다. 즉 나를 call 한 resolver의 parent를 갖게 된다. 어느 resolver가 나를 call 했는지 알 수 있으니 유용하다. (참고로 _ 가 parent를 뜻한다, parent나 root라고 한다.)

  아무튼 우리는 parent로 부터 return을 할 수 있다. 그 예는 아래와 같다.
  ~~~json
    User: {
    fullName: parent => {
      return `${parent.firstName} ${parent.lastName}`;
    }
  }
  ~~~

  - 이것을 me말고 seeUser에 해도 작동할까?
  => 그렇다, schema에다가 모든 resolver를 하나로 merging하고 있기 때문이다.

## 3.15. itsMe and amIFollowing Fields part One
- computed.js 파일 생성.
- amIFollowing -> 이 유저가 그를 following 하고 있는지 확인 위해 만듬.

### amIFollowing 예시
- Command
~~~json
{
  me {
    user {
      amIFollowing
      itsMe
    }
  }
}
~~~

- HTTP request
~~~json
{"Authorization": "Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpZCI6ImNrNzd0dzQyOTFycXIwOTA1eXk1c2hpcnAiLCJpYXQiOjE1ODMyMDg4NTJ9.qAojVTOQnWH_z1Y-DvWUlZDsoJ41FfPA-UrIl-ggWNQ"}
~~~

- Result
~~~json
{
  "data": {
    "me": {
      "user": {
        "amIFollowing": false,
        "itsMe": true
      }
    }
  }
}
~~~

### seeUser로 테스트 (다른 사람)

- Command
~~~json
{
  seeUser(id:"ck77oedm81kue0905wt78x0kx"){
    user {
      itsMe
    }
  }
}
~~~

- HTTP header
~~~json
{"Authorization": "Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpZCI6ImNrNzd0dzQyOTFycXIwOTA1eXk1c2hpcnAiLCJpYXQiOjE1ODMyMDg4NTJ9.qAojVTOQnWH_z1Y-DvWUlZDsoJ41FfPA-UrIl-ggWNQ"}
~~~

- Result
~~~json
{
  "data": {
    "seeUser": {
      "user": {
        "itsMe": false
      }
    }
  }
}
~~~

### 내가 나를 following 하는 지 알아보기. (amIFollowing 이용)

- Command
~~~json
{
  me {
    user {
      amIFollowing
    }
  }
}
~~~

- HTTP header
~~~json
{"Authorization": "Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpZCI6ImNrNzd0dzQyOTFycXIwOTA1eXk1c2hpcnAiLCJpYXQiOjE1ODMyMDg4NTJ9.qAojVTOQnWH_z1Y-DvWUlZDsoJ41FfPA-UrIl-ggWNQ"}
~~~

- Result
~~~json
{
  "data": {
    "me": {
      "user": {
        "amIFollowing": false
      }
    }
  }
}
~~~

### 3.16. itsMe and amIFollowing Fields part Two.
- exits는 users를 위한 필터이므로 이것을 사용해보자.

- Command
~~~json
{
  users(
    where: {
      AND: [
        { id: "ck77tw4291rqr0905yy5shirp" }
        { followers_some: { id: "ck77oedm81kue0905wt78x0kx" } }
      ]
    }
  ){
    id
  }
}
~~~

- Result
~~~json
{
  "data": {
    "users": []
  }
}
~~~

- Prisma로 디버깅하는 방법은 (무언가 실수했는데 Prisma Client가 이를 삼켜버린다면) 코드를 복사해서 Prisma Endpoint에서 테스트 해보면 됨.
- itsMe => itSelf, amIFollowing => isFollowing

## 3.17. isLiked Computed File

- Post에 있는 "isLiked" => computed.js의 isFollowing이나 itSelf와 비슷.

### isLiked 테스트

- Command
~~~json
{
  seeFullPost(id:"ck77phhmq3cdt0961jtbsz2yg"){
    post{
      isLiked
    }
  }
}
~~~

- Result
~~~json
{
  "data": {
    "seeFullPost": {
      "post": {
        "isLiked": true
      }
    }
  }
}
~~~

## 3.18. upload Resolver

### upload post 예시
- command
~~~json
mutation {
  upload(
    caption: "brewedCoffee"
    files: [
      "https://www.google.com/url?sa=i&url=https%3A%2F%2Fblog.bluebottlecoffee.com%2Fposts%2Fpro-tips-nel-drip-coffee&psig=AOvVaw1gV7V1HDocPQG3ZkNs5RaD&ust=1583590844673000&source=images&cd=vfe&ved=0CAIQjRxqFwoTCKDs6eKFhugCFQAAAAAdAAAAABAQ"
      "https://www.google.com/url?sa=i&url=https%3A%2F%2Fwww.123rf.com%2Fphoto_82666652_thai-people-use-drip-coffee-maker-or-dripper-made-hot-coffee-for-sale-for-people-at-shop-in-organic-.html&psig=AOvVaw1gV7V1HDocPQG3ZkNs5RaD&ust=1583590844673000&source=images&cd=vfe&ved=0CAIQjRxqFwoTCKDs6eKFhugCFQAAAAAdAAAAABAc"
    ]
  ) {
    id
    caption
  }
}
~~~

- HTTP header
~~~json
{"Authorization": "Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpZCI6ImNrNzd0dzQyOTFycXIwOTA1eXk1c2hpcnAiLCJpYXQiOjE1ODMyMDg4NTJ9.qAojVTOQnWH_z1Y-DvWUlZDsoJ41FfPA-UrIl-ggWNQ"}
~~~

- Result
~~~json
{
  "data": {
    "upload": {
      "id": "ck7g9xkwb8r980984i4isheai",
      "caption": "brewedCoffee"
    }
  }
}
~~~

- seeFullPost에서 파일들을 받도록 seeFullPost.graphql와 seeFullPost.js에 추가.

### upload한 포스트 seeFullPost로 보기

- command
~~~json
{
  seeFullPost(id: "ck7g9xkwb8r980984i4isheai") {
    files {
      id
      url
    }
    post {
      caption
      location
    }
  }
}
~~~

- HTTP header
~~~json
{"Authorization": "Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpZCI6ImNrNzd0dzQyOTFycXIwOTA1eXk1c2hpcnAiLCJpYXQiOjE1ODMyMDg4NTJ9.qAojVTOQnWH_z1Y-DvWUlZDsoJ41FfPA-UrIl-ggWNQ"}
~~~

- Result
~~~json
{
  "data": {
    "seeFullPost": {
      "files": [
        {
          "id": "ck7g9xleof22u0a48s737b629",
          "url": "https://www.google.com/url?sa=i&url=https%3A%2F%2Fblog.bluebottlecoffee.com%2Fposts%2Fpro-tips-nel-drip-coffee&psig=AOvVaw1gV7V1HDocPQG3ZkNs5RaD&ust=1583590844673000&source=images&cd=vfe&ved=0CAIQjRxqFwoTCKDs6eKFhugCFQAAAAAdAAAAABAQ"
        },
        {
          "id": "ck7g9xleof22v0a48av4n1z81",
          "url": "https://www.google.com/url?sa=i&url=https%3A%2F%2Fwww.123rf.com%2Fphoto_82666652_thai-people-use-drip-coffee-maker-or-dripper-made-hot-coffee-for-sale-for-people-at-shop-in-organic-.html&psig=AOvVaw1gV7V1HDocPQG3ZkNs5RaD&ust=1583590844673000&source=images&cd=vfe&ved=0CAIQjRxqFwoTCKDs6eKFhugCFQAAAAAdAAAAABAc"
        }
      ],
      "post": {
        "caption": "brewedCoffee",
        "location": null
      }
    }
  }
}
~~~

## 3.19. seeFullPost Refactoring.
- computed.js를 밖으로 옮김. (computed file이 공유 되야 하기 때문에)
- files, user, comments 이렇게 점점 커지기 때문에 fullPost를 위한 아주 큰 fragment를 만들었다.
  fragment.js를 정리하여 관계들을 분명하게 만들었다.
  그렇지 않으면, 엄청나게 많은 쿼리들 때문에 힘들어질 것이기 때문이다.

### 수정 후 seeFullPost로 예시

- Command
~~~json
{
  seeFullPost(id: "ck7g9xkwb8r980984i4isheai") {
      caption
      location
    likeCount
    files {
      id
    }
    user {
      id
    }
    comments {
      id
    }
  }
}
~~~

- HTTP header
~~~json
{"Authorization": "Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpZCI6ImNrNzd0dzQyOTFycXIwOTA1eXk1c2hpcnAiLCJpYXQiOjE1ODMyMDg4NTJ9.qAojVTOQnWH_z1Y-DvWUlZDsoJ41FfPA-UrIl-ggWNQ"}
~~~

- Result
~~~json
{
  "data": {
    "seeFullPost": {
      "caption": "brewedCoffee",
      "location": null,
      "likeCount": 1,
      "files": [
        {
          "id": "ck7g9xleof22u0a48s737b629"
        },
        {
          "id": "ck7g9xleof22v0a48av4n1z81"
        }
      ],
      "user": {
        "id": "ck77tw4291rqr0905yy5shirp"
      },
      "comments": []
    }
  }
}
~~~

## 3.20. editPost deletePost Resolver

### editPost 예시

- Command
~~~json
mutation {
  editPost(id: "ck7g9xkwb8r980984i4isheai", location: "San Francisco", action: EDIT) {
    id
    location
  }
}
~~~

- HTTP header
~~~json
{"Authorization": "Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpZCI6ImNrNzd0dzQyOTFycXIwOTA1eXk1c2hpcnAiLCJpYXQiOjE1ODMyMDg4NTJ9.qAojVTOQnWH_z1Y-DvWUlZDsoJ41FfPA-UrIl-ggWNQ"}
~~~

- Result
~~~json
{
  "data": {
    "editPost": {
      "id": "ck7g9xkwb8r980984i4isheai",
      "location": "San Francisco"
    }
  }
}
~~~