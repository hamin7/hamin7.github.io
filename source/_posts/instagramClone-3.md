---
title: instagram Clone하기 (Backend) 02-Prisma Setting하기 (3) 
author: Hamin
date: 2020-03-31 22:18:06
tags: [node.js, prisma, graphql, typescript]
categories: 
- [Projects]
- [Backend, Prisma]
thumbnail: /gallery/prisma.png
---

저번 포스팅에 이어서 본격적으로 backend 기능을 구현해 보도록 하겠습니다.
이 프로젝트 내용은 {% link NomadCoders https://academy.nomadcoders.co/ [external] [title] %} 를 참고하여 진행할 예정입니다.

<!-- more -->

## 3.0. Planning the API

- [x] Create account
- [x] Request Secret
- [x] Confirm Secret (Login)
- [x] Like / Unlike a photo
- [x] Comment on a photo
- [x] Search by user
- [x] Search by location
- [x] See user profile
- [x] Follow / Unfollow User
- [x] See the full photo
- [x] Edit my profile
- [x] Upload photo
- [x] Edit the photo (Delete)
- [x] See the feed

- 사용자들이 전송 받은 비밀값을 페이지에 붙여넣기 하면 로그인하게 되고, 그들이 이메일에 접근할 수 있다는 것을 확인!

## 3.1. Create Account Resolver

- graphql console에서 post 추가.
- Image/toggleLike 폴더 생성 => 이미 좋아요가 되어 있으면 좋아요 취소로 아이콘이 바뀔 것.
- createAccount 폴더 및 createAccount.graphql 파일 생성.
- 참고로 멀티 커서 키는 option + click.
- 그래서 resolver가 무엇인가???

### createAccount로 user 추가
  - command
  ~~~json
    mutation {
        createAccount (username:"gigi", email:"gianluigi@juventus.com", firstName:"Gianluigi", lastName:"Buffon"){
            id
        }
    }
  ~~~

  - 결과
  ~~~json
  {
    "data": {
      "createAccount": {
        "id": "ck77tw4291rqr0905yy5shirp"
      }
    }
  }
  ~~~

  ## 궁금? 이거 왜 로컬 playground에서는 되는데 서버 playground에서는 에러남??? 근데 또 console db에는 추가 됨.
  ~~~json
  {
    "data": null,
    "errors": [
        {
        "message": "Cannot query field 'createAccount' on type 'Mutation'. Did you mean 'createPost', 'createComment' or 'createRoom'? (line 2, column 3):\n  createAccount(username: \"gigi\", email: \"gianluigi@juventus.com\", firstName: \"Gianluigi\", lastName: \"Buffon\") {\n  ^",
        "locations": [
            {
            "line": 2,
            "column": 3
            }
        ]
        }
    ]
    }
  ~~~

### 참고 : 삭제하기

~~~json
mutation {
  deleteManyObjects(where: {
    id_not: 0
  }){
    count
  }
}
~~~

## 3.2. requestSecret Resolver

- requestSecret.graphql과 requestSecret.js 파일 생성.
- utils.js 생성.
- 로그인하면 토큰 부여.
- 사용자가 페이지에 접속해서 이메일 입력하면, 비밀값이 전송 되고, 사용자들은 그 비밀값을 복사-붙여넣기 해서 접속.
- 보통은 비밀번호를 쓰지만 비밀번호는 진부해~~~!
- Math.random을 이용하여 랜덤으로 adjectives와 nouns를 조합하여 단어를 생성해 주는 기능 만들기.
- Math.floor는 소숫점 아랫자리 숫자 버림, Math.ceil은 반대로 올림.
- user의 email이 인자로 입력된 email과 같은 사용자를 where로 찾을 것.
- id, username, email로만 사용자를 찾을 수 있음.
- bio로 사용자를 찾아서 갱신할 수 는 없음. 유니크한 특성들로만 사용자를 찾아서 갱신요청 할 수 있기 때문.
- lastName이나 bio같은 @unique가 없는 것들로는 사용자를 검색할 수 없고 많은 사용자들을 필터는 할 수 있음.
- 유니크한 특성이 아니라면 삭제나 수정 요청을 할 수 없음.

## 3.3. sendMail Function with Nodemailer

- secretRequest를 실제로 메일로 보내주기 위해 nodemailer를 사용.
- yarn add nodemailer
- utils에 sendMail을 만들자.
- nodemailer는 몇몇 기본값들로 transport라는 것을 만들고, 그것으로 sendMail을 요청하면 끝.
- sendgrid를 사용하겠다.
- yarn add nodemailer-sendgrid-transport
- api_user와 api_key는 .env에 저장.
- server.js에 utils를 import. process.env를 가져오려고 함.
- sendMail은 외부에서 사용하지 않으니 export 하지 않아도 됨. sendSecretMail을 사용할 것.
- 참고로 보내는 메일 주소 도메인을 가짜로 설정 하는 것은 정말 쉬워 보인다.

## 3.4. Passport JWT part One

- requestSecret에 이메일 보내는 기능을 추가.
- utils에서 sendSecretMail은 sendMail을 리턴하고, sendMail은 Promise 함수를 리턴.
- command
  ~~~json
  mutation {
    requestSecret(email:"ggamini7@gmail.com")
  }
  ~~~

- result
  ~~~json
  {
    "data": {
      "requestSecret": true
    }
  }
  ~~~

  - confirmSecret을 만들어 보자.
  - confirmSecret.js 및 confirmSecret.graphql파일 생성
  - confirmSecret.graphql의 함수는 jwt 토큰을 리턴할 것.

### secretConfirm 실행

~~~json
mutation {
  confirmSecret(email:"ggamini7@gmail.com", secret:"great hate")
}
~~~

- result
~~~json
{
  "data": {
    "confirmSecret": "TOKEN"
  }
}
~~~

- 이제 토큰을 만들어 보자
- passportjs에서 jwt를 사용할 것.
- yarn add passport-jwt passport
- passport는 인증 관련한 모든 일을 함.
- jwt 토큰이나 쿠키에서 정보를 가져와서 사용자 정보에 serialize(저장) 함.
- 토큰에서 정보를 가져와서 (express의) request에 붙여주는 것.
- 토큰을 가져와서 해독한 후에 사용자 객체를 request에 추가 해 줌.
- secret은 passport 정보를 암호화하는데 필요한 비밀 값.
- 확인용 callback 함수도 추가하여 옵션이 잘 맞게 적용되었을 때 JwtStrategy 함수가 토큰을 해석하도록 해야 함.
- 사용자 정보가 암호화 되어 토큰에 담기면 
- -> JWT는 토큰을 입력 받아서 정보를 해석함
- -> 해석된 정보를 콜백 함수로 전달해 줌.

## 3.5. Passport JWT part Two

- JWT를 가져와서 해석하고 확인하는 작업들을 함.
- JWT 생성도 하도록 해야 함.
- jsonwebtoken 모듈이 필요함. => yarn add jsonwebtoken
- 암호화할 때와 해독할 때 같은 private key를 사용.
- jwt가 id를 암호화 해서 토큰을 만들어 줌.

### token 만들기
- command
~~~json
mutation{
  confirmSecret(email:"ggamini7@gmail.com", secret:"great hate")
}
~~~

- result
~~~json
{
  "data": {
    "confirmSecret": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpZCI6ImNrNzd0dzQyOTFycXIwOTA1eXk1c2hpcnAiLCJpYXQiOjE1ODMwODA3OTl9.cZiSHChUpIxMnr21hP6H0_1W2KayZo3hViBzmOWlXbQ"
  }
}
~~~