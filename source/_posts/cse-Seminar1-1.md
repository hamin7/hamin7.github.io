---
title: ARM 및 그 응용 프로그램에서 하드웨어 지원 명령 레벨 분리 (2)
author: Hamin
date: 2020-03-27 01:45:04
tags:
thumbnail: /gallery/computer-security.jpg
---
이 포스트는 한양대학교 CSE 융합세미나의 조영필 교수님의 강연을 바탕으로 작성되었습니다.

ARM architecture는 최근 스마트폰, IoT 디바이스 등에서 매우 폭발적으로 많이 사용되고 있는 아키텍쳐이다. 그리고 이 ARM 아키텍쳐에는 다양한 하드웨어 feature들이 있다. 이러한 feature들을 이용한 security system을 만들어 이를 이용해 어떻게 우리가 보안성을 향상시킬 수 있는가가 이 포스트의 포인트이다.

<!-- more -->

{% asset_img image1.png %}
앞에서 본 바와 같이 