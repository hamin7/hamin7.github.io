---
title: Mac OsX에서 LaTeX 사용하기
author: Hamin
date: 2020-04-21 14:45:15
tags: [LaTeX]
categories:
- [papers]
- [LaTeX]

thumbnail: /gallery/latex.png
---

LaTeX (ˈleɪtɛk 레이텍 또는 ˈlɑːtɛk 라텍)은 문서 조판에 사용되는 프로그램입니다. 도널드 커누스가 만든 TeX을 쉽게 사용하기 위하여 1984년에 레슬리 램포트가 만든 매크로입니다. TeX을 직접 사용하기는 어렵기 때문에, 오늘날에는 LaTeX을 이용하여 문서를 만드는 경우가 많습니다.
이 포스팅에서는 MacOSX에서 MacTex를 이용해 LaTeX를 사용하는 방법을 알아보겠습니다.

<!--more-->

우선 {% link MacTex 사이트 http://tug.org/mactex/mactex-download.html [external] [title] %}에서 pkg를 다운로드 받습니다.

{% asset_img install.png %}

다운로드가 완료되면 pkg를 실행시켜 installation을 진행해 줍니다.

설치가 완료 되면 dock에 아래와 같은 아이콘이 생성됩니다.

{% asset_img icon.png %}

저 icon을 클릭하면 TeXShop이 실행 됩니다.

TeXShop editor에 다음과 같이 간단하게 작성하여 보았습니다.

{% asset_img editor.png %}

이를 저장해 줍니다.

{% asset_img save.png %}

상단 메뉴 바에서 조판하기 (Typeset)에서 LaTeX를 선택해주면 아래와 같은 콘솔창이 뜹니다.

{% asset_img console.png %}

아래와 같은 pdf output 또한 나타납니다.

{% asset_img pdf.png %}

<br>
<br>
<br>

{% link TeXstudio https://www.texstudio.org/ [external] [title] %} 또한 사용 가능합니다.

<br>

{% asset_img TeXstudio.png %}