---
title: instagram Clone하기 (Backend) 01-Prisma Setting하기 
author: Hamin
date: 2020-03-24 21:20:14
tags: [node.js, prisma, graphql, typescript]
categories: 
- [Projects]
- [Backend, Prisma]
thumbnail: /gallery/prisma.png
---

instagram을 클론하는 프로젝트를 진행해보겠습니다.
Backend는 node.js와 prisma, typescript 그리고 Frontend는 node.js, React-native, apollo를 이용하여 개발할 예정입니다.
이 프로젝트 내용은 {% link NomadCoders https://academy.nomadcoders.co/ [external] [title] %} 를 참고하여 진행할 예정입니다.

<!-- more -->

## 0.0.  What are we building (사용 기술 스택)
- node.js, react.js, react-native, prisma, graphql, apollo, typescript.
- node.js (서버 기술)
- prisma (데이터베이스 ORM)
- react.js (프론트엔드 기술), react-native를 앱을 만드는 데에 사용.
- ios, 안드로이드, 프론트엔드, 백엔드 모두 javascript로 할 것.

## 0.1. Requirements.
- 터미널에서 yarn이나 npm을 실행 해야 함.
- yarn 설치 할 것 (Homebrew 통해 설치).
- node version은  v10.15.2.
- visual studio를 에디터로 사용.
- Git 사용
- prisma.io 사용.
- Google chrome.
- lot of coffee.

## 1.0. Setting up the project
- yarn init.
- yarn add graphql-yoga
- yarn add nodemon-D => src/server.js에 있는 코드를 실행하는 script를 작성하는데에 nodemon이 필요.
- yarn add babel-node => babel-node 설치.
- yarn add babel-cli -D
- nodemon --exec babel-node src/server.js => dev 명령어 추가.
- yarn dev를 실행하면, 'nodemon --exec babel-node src/server.js'를 실행하게 되는 것.
- nodemon.json의 ext는 nodemon이 감시해야 할 파일의 확장자들을 지정할 수 있음.
- babel은 멋진 코드를 못생긴 코드로 바꿔주는 도구.
- 멋진 코드는 아직 지원되지 않는 곳들이 있어서, 못생긴 코드로 바꿔줘야 함.
- nodemon은 파일을 저장할 때마다 실행을 새로 해주는 도구, 서버를 껐다가 켤 필요가 없음.

## 1.1. Creating GraphQL Server
- 서버를 먼저 세우고, 그 다음에 prisma를 서버 코드에 추가할 것.
- dotenv 모듈은 .env 파일을 읽음.
- dotenv config에서 포트를 읽어오도록 할 수 있음. => .env 파일에 Port를 추가하면 됨.
- PORT 넘버 등 모든 설정값들을 env에 추가하는건 좋은 습관이다.
- node가 import를 인식하지 못한다면 .babelrc 파일 추가해야 함.
- .babelrc 파일에 presets를 입력. @babel/preset-env가 가장 최신의 프리셋.
- 같은 패키지에서 여러 모듈을 설치하고 싶으면 yarn add @babel/{node, preset-env} 이런식으로 할 수 있음.

## 1.2. Setting up the Server.
- morgan은 미들웨어이자 logger(로깅 전용 모듈)이다.
- GraphQLServer에는 express 서버가 내장되어 있다.
  ### typeDef와 resolvers를 추가하지 않고, 더 프로페셔널한 방식으로 추가하는 방법.
  - src/api라는 폴더를 만들어 schema.js를 만들고 이 파일에 모든 파일들을 합침.
  - api 폴더 안에 아주 많은 graphql 파일들이 추가될 것이고, 같은 위치에 resolvers파일들이 추가될 것.
  - 그 파일들을 이 schema.js 파일로 밀어 넣을 것.
  - 서버에는 schema.js 파일 하나만 입력해주면 됨.
  - 서버에는 typeDefs와resolvers가 모두 입력 되어야 함.
  - 아니면 그것을 schema 파일에 합쳐서 입력해도 됨. schema 파일이 typeDefs와 resolvers를 모두 가지고 있으면 됨.
  - 폴더를 만들고 graphql 파일들과 resolver 파일들을 만들고, 그것들을 모아서 import해주는 방식!
  - api 폴더 밑에 images, comments, likes, users 이렇게 폴더를 나눠서 만드는 것이 좋다.
  - **는 모든 폴더고, *.graphql은 모든 .graphql 파일.
  - api 폴더의 모든 파일들을 schema 파일에서 한번에 받음.
  - server에는 typeDef와 resolvers를 따로 입력하거나, 하나로 합쳐진 schema를 입력하면 됨.