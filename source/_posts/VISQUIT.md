---
title: (프로젝트) 인공지능 스피커를 이용한 음성인식 키오스크 플랫폼
author: Hamin
date: 2020-06-08 23:47:10
tags: [AI, Backend, Node.js, AWS, javascript, React.js]
categories:
- [AI]
- [Backend, Node.js]
- [Frontend, React.js]
- [Projects, SKT]
- [AWS, EC2]
- [Toy Project]
thumbnail: /gallery/kiosk.jpg
---

SKT의 음성인식 스피커와 AWS EC2, S3, Node.js, React.js 를 이용, 음성인식 키오스크 플랫폼을 개발하였습니다. 
기술에 소외된 분들이 키오스크를 이용하는데 어려움을 겪는 것에 문제의식을 느껴 개발하게 되었습니다.
또한 기존의 키오스크 플랫폼이 구축 비용이 큰 것에 비해 음성인식 키오스크는 구축 비용이 적습니다.

<!--more-->
 
{% link Front-end repository https://github.com/FallenMapoBridge/visquit-frontend [external] [title] %}
<br>
 
{% link Back-end repository https://github.com/FallenMapoBridge/visquit-backend [external] [title] %}

## 최종 발표 자료

{% link PDF 파일 https://github.com/FallenMapoBridge/project_documentation/blob/master/final/se_ppt_v3.pdf [external] [title] %}

## Abstract

VISQUIT는 인공지능 기반의 음성인식 기술로 구동되는 주문 서비스입니다. 요즘에는 많은 상점들이 KIOSK를 사용하고 있는데, 이를 통해 점원을 고용하는 비용과 개별 주문을 하는 데 드는 시간이 줄어들게 되었습니다. 하지만 문제는 키오스크 설치가 여전히 비싸다는 점입니다.
이 비용 문제를 해결하기 위해, 우리는 음성 인식 기술을 이용한 NUGU 스피커를 사용할 것을 제안합니다. NUGU 스피커를 사용하면 고객이 음성으로 주문할 수 있습니다. 이 시스템이 한 사람의 발화를 이해하고 서로 소통 할 수 있다면, 이것은 이전의 "사람에 의해서 이루어지던" 주문 시스템을 대체할 수 있습니다. 그리고 더 나아가 KIOSK의 비용 부담 문제도 해결될 것입니다.
SK텔레콤의 NUGU 플랫폼에 크게 의존해 음성인식 기능을 구현하고자 합니다. React.js 라이브러리로 UI를 개발하고, Node.js로 백엔드 구조를 구성하고, Docker와 Jenkins로 CI/CD 인프라를 구축할 것입니다.

## Intoduction
### 1.1 Motivation
최근 프랜차이즈 음식점들을 중심으로 키오스크의 설치 및 운영이 증가하는 추세입니다. 이를 통하여 주문 수납에 필요한 인력에 대한 인건비를 절약할 수 있고, 단위 시간에 보다 많은 주문을 처리할 수 있습니다.

하지만 키오스크의 등장으로 인하여 불편을 겪는 사람들도 있습니다. 바로 이러한 디지털 기술이 익숙하지 않은 계층입니다. 특히, 노년층에게 디지털 기술은 그야말로 낯선 괴물에 가깝습니다. 한국정보화진흥원(NIA)의 ‘2018 디지털정보격차 실태조사’에 따르면 만 55세 이상의 장노년층의 종합적인 ‘디지털정보화 수준’은 일반 국민의 63.1%에 불과합니다. 사람을 편리하게 만들고자 등장한 기술로 인하여 소외 받는 계층이 존재하는 모순적인 상황이 발생하는 것입니다.

또한 키오스크는 가격이 비싸기 때문에 자영업자들에게도 큰 비용적인 부담을 안겨준다는 문제 또한 가지고 있습니다.

### 1.2 Problem Analysis
키오스크의 문제점 중에서 키오스크 내부에 탑재되는 주문/결제 소프트웨어의 개선에 초점을 두고자 합니다. 기존 방식은 주문시 모든 메뉴가 화면에 표시됨에 따라 정보량이 많아지고, UI에 익숙하지 않은 사람에게 불편을 초래합니다. 또한 표시되는 정보량을 최대화하기 위하여 글자와 이미지의 크기가 작고, 화면 해상도에 한계가 있어 선명하게 표시되지 않습니다. 또한, 터치 스크린의 감도가 물리 버튼에 비하여 정확하지 않아 사용자에게 혼란스러운 UX를 제공하게 됩니다.

### 1.3 Solution
SK Nugu Platform의 인공지능 음성 인식 기술을 활용하여, 사용자의 발화를 인식한 뒤 이를 기반으로 주문 과정을 진행합니다. 이를 통하여 디지털 기술에 익숙하지 않은 사용자에게 실제 사람과 대화하는 듯한 인터페이스를 제공하여 친숙하게 접근할 수 있습니다. 또한, 기존 방식에 비하여 복잡한 UI가 없이도 여러 가지 주문을 처리할 수 있습니다.

또한, 전반적으로 큰 이미지와 텍스트를 사용하여 컨텐츠의 선명도를 극대화하고, 시력이 낮은 사용자가 컨텐츠를 보다 잘 파악할 수 있도록 UI를 구성합니다. 컨텐츠의 크기가 증가함에 따라 한 화면에 담을 수 있는 정보량이 줄어들기 때문에, 줄어든 정보량에도 불구하고 기존과 동일한 비즈니스 로직을 구현할 수 있도록 UI를 설계합니다.

또한 기존의 키오스크와 다르게 NUGU 스피커만 있으면 되기 때문에 비용적인 부담도 줄여줄 수 있습니다.

## Structure
전체 구조는 다음과 같습니다.  
{% asset_img structure.png %}

## User Interface for Client
우리는 고객에게 메뉴 관리와 주문 확인 기능을 제공하고자 합니다.


{% asset_img Client_UI_1.png %}

{% asset_img Client_UI_2.png %}


## Contributors

- {% link 김정모 (정보시스템/14) https://github.com/cadenzah [external] [title] %}
- {% link 이하민 (정보시스템/17) https://github.com/hamin7 [external] [title] %}
- {% link 이효식 (경영학부/14) https://github.com/hy06ix [external] [title] %}
- {% link 황성우 (정보시스템/16) https://github.com/king10tech [external] [title] %}
