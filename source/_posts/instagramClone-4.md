---
title: instagram Clone하기 (Backend) 03-Backend Resolvers (1) 
author: Hamin
date: 2020-04-06 11:22:41
tags: [node.js, prisma, graphql, typescript]
categories: 
- [Projects]
- [Backend, Prisma]
thumbnail: /gallery/prisma.png
---

이번 포스팅에서는 로그인 시 인증, Like, follow, 유저 검색, 포스트 검색 그리고 댓글 달기 Resolver를 만들어 보겠습니다
이 프로젝트 내용은 {% link NomadCoders https://academy.nomadcoders.co/ [external] [title] %} 를 참고하여 진행할 예정입니다.

<!-- more -->

## 3.6. Passport JWT part Three

- env.js 파일 생성. => passport와 utils에 dotenv를 불러오지 않고 process.env를 사용할 수 있음.
- passport는 쿠키와 세션 작업을 하기에 좋다. 쿠키를 가져오고 만들어주고 모든 일을 한다.
- express에서는 미들웨어를 지나서 라우트가 실행 됨.
- 토큰을 받아서 해석하고, 사용자를 찾고, 사용자가 존재한다면 req 객체에 사용자를 추가하고 나면 graphql 함수를 실행.
- 로그인 되어 있다면 모든 graphql 요청에 사용자 정보가 추가되어서 요청되는 것.

  ### context는 resolver 사이에서 정보를 공유할 때 사용.
  - 예를 들어, prisma를 server.js에서 한 번만 import 한 후에 prisma를 context에 추가할 수 있음.
  - 하지만 vscode가 이 방식을 이해하지 못한다. => 자동완성 사용 불가능.

- 많은 사람들이 prisma를 context에 db라는 이름으로 담아 사용한다.
  context로 담은 후에, context.db.user 처럼 호출.
- 서버에 전달되는 모든 요청은 authenticateJwt 함수를 통과함.

### confirmSecret으로 토큰 받기
- command
~~~json
mutation {
  confirmSecret(email:"ggamini7@gmail.com", secret:"enchanting plantation")
}
~~~

- result
~~~json
{
  "data": {
    "confirmSecret": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpZCI6ImNrNzd0dzQyOTFycXIwOTA1eXk1c2hpcnAiLCJpYXQiOjE1ODMyMDg4NTJ9.qAojVTOQnWH_z1Y-DvWUlZDsoJ41FfPA-UrIl-ggWNQ"
  }
}
~~~

### requestSecret으로 request 안에 user가 있는지 확인하기

- command
~~~json
mutation{
  requestSecret(email:"ggamini7@gmail.com")
}
~~~

- HTTP Header
~~~json
{"Authorization": "Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpZCI6ImNrNzd0dzQyOTFycXIwOTA1eXk1c2hpcnAiLCJpYXQiOjE1ODMyMDg4NTJ9.qAojVTOQnWH_z1Y-DvWUlZDsoJ41FfPA-UrIl-ggWNQ"}
~~~

- result
~~~json
{
  "data": {
    "requestSecret": false
  }
}
~~~

- console.log(request.user)
~~~json
{ email: 'ggamini7@gmail.com',
  username: 'gigi',
  loginSecret: 'enchanting plantation',
  bio: '',
  lastName: 'Buffon',
  firstName: 'Gianluigi',
  id: 'ck77tw4291rqr0905yy5shirp' }
~~~

### passport workflow
 - authenticateJwt 함수에서는 passport.authenticate("jwt") 함수를 실행
 - 이 함수는 Stretegy를 활용해서 jwt 토큰을 추출
 - 토큰이 추출되면 verifyUser를 payloa와 함께 실행.
 - payload는 토큰에서 해석된 id를 받아서, user를 찾아서 리턴.
 - 콜백 함수 authenticate("jwt", (error, user))가 실행되어, 사용자가 있으면 그 사용자를 req에 추가.
 - sever.js에서 context에 request를 담아줌.


## 3.7. toggleLike Resolver

- toggleLike.js 및 toggleLike.graphql 파일 생성
- middlewares.js 파일 생성  => graphql middleware 같은 것.
- 인증을 필요로 하는 Resolver들이 많은데 그 때마다 로그인 하라는 함수 만들 수 없으니 middleware 만들어서 한번에 처리.
- confirmSecret으로 로그인 하고 그 토큰을 받아 toggleLike 할 때 사용.
- toggleLike.js에서 만약 좋아요가 존재 하면, 이걸 지워야 하고, 좋아요가 존재하지 않는다면 이 사용자가 갖고 있는 그리고 이 포스트가 갖고 있는 좋아요를 만들 것.


## 3.8. addComment Resolver
- Like를 얻는 것과 지우는 것이 같은 방식이므로 toggleLike.js에 filterOptions라는 변수 만들어 줌.
  
  ### post에 like 하기
  - command
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

  - 저 command를 한 번 더 하면 like가 사라진다.

  ### Comment 달기
  
  - command
  ~~~json
  mutation {
    addComment(postId:"ck77phhmq3cdt0961jtbsz2yg", text:"I'm hungry"){
      id
      text
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
      "addComment": {
        "id": "ck7bgja9p1fgd0984bcayfn7b",
        "text": "I'm hungry"
      }
    }
  }
  ~~~

## 3.9. searchUser and searchPost Resolver
- search는 인증과정이 불필요하다.
- Prisma의 장점 중 하나는 검색이 매우 쉽다.

### searchUser 예시

- command
  ~~~json
  {
    searchUser(term:"gerrit"){
      id
      username
      firstName
      lastName
    }
  }
  ~~~

- Result
  ~~~json
  {
    "data": {
      "searchUser": [
        {
          "id": "ck77oedm81kue0905wt78x0kx",
          "username": "gerrit",
          "firstName": "gerrit",
          "lastName": "Cole"
        }
      ]
    }
  }
  ~~~

### searchPost 예시

- command
~~~json
{
  searchUser(term:"gigi"){
    id
    username
    firstName
    lastName
  }
  searchPost(term:"americano"){
    id
    caption
    location
  }
}
~~~

- Result
~~~json
{
  "data": {
    "searchUser": [
      {
        "id": "ck77tw4291rqr0905yy5shirp",
        "username": "gigi",
        "firstName": "Gianluigi",
        "lastName": "Buffon"
      }
    ],
    "searchPost": [
      {
        "id": "ck77phhmq3cdt0961jtbsz2yg",
        "caption": "Americano nyam",
        "location": "New york"
      }
    ]
  }
}
~~~

### searchPost 예시2
- Command
~~~json
{
  searchUser(term:"gigi"){
    id
    username
    firstName
    lastName
  }
  searchPost(term:"New"){
    id
    caption
    location
  }
}
~~~

- Result
~~~json
{
  "data": {
    "searchUser": [
      {
        "id": "ck77tw4291rqr0905yy5shirp",
        "username": "gigi",
        "firstName": "Gianluigi",
        "lastName": "Buffon"
      }
    ],
    "searchPost": [
      {
        "id": "ck77phhmq3cdt0961jtbsz2yg",
        "caption": "Americano nyam",
        "location": "New york"
      }
    ]
  }
}
~~~

- searchPost.js에서 caption_starts_with이므로 시작 부분을 비교하여 검색결과를 보여줌.

## 3.10. follow unfollow Resolver

### follow 예시

- Command
~~~json
mutation {
  follow(id:"ck77oedm81kue0905wt78x0kx")
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
    "follow": true
  }
}
~~~

### unfollow 예시

- Command
~~~json
mutation {
  unfollow(id:"ck77oedm81kue0905wt78x0kx")
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
    "unfollow": true
  }
}
~~~

- Secret이 확인 되면 로그인 시크릿을 지우도록 confirmSecret.js에 코드 추가.
~~~javascript
await prisma.updateUser({
  where: { id: user.id },
  data: {
    loginSecret: ""
  }
});
~~~