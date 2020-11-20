---
title: Why Hexo?
date: 2020-03-17 23:00:00
tags: [Hexo, Blog, Node.js]
categories: [Javascript, Node.js]
thumbnail: /gallery/hexo.jpg
---

Hexo framework 와 Git을 이용 간단한 블로그설정에 대한
포스팅입니다.

<!-- more -->

## Why select hexo?

### 다른 프레임워크간의 비교
정적페이지 블로그 프레임워크 중에서 유명한것 중에 대표적으로 Hexo, Hugo, Jekyll 이 있다.

Hexo
ㅡ Node js 기반
ㅡ 한글 레퍼런스 + 사용자수 꽤 많음
ㅡ 테마 여러개 많음
ㅡ 플러그인과 확장성 좋다함
ㅡ 근데 죄다 중국개발자님들…

Hugo
ㅡ Go 기반
ㅡ 빌드 빠르다고 함
ㅡ 문서화 깔끔함
ㅡ 한글 레퍼런스 거의 없음
ㅡ 사실 뭐가 좋은지 모르겠다…

Jekyll
ㅡ 루비 기반
ㅡ 사용자수 제일 많음
ㅡ 한글 레퍼런스도 제일 많음
ㅡ 근데 느림… ( 10개 포스팅시 5분이상 걸렸음)
ㅡ 윈도우 지원 이슈 많음…

라고 비교를 해봤는데
결국 Jekyll 는 루비기반인데 포스팅속도나 윈도우 지원 이슈 때문에 포기
Hugo 랑 Hexo 둘중 고민중 차라리 Hugo 의 커스텀을 위해선 Go Lang 으로 다루어야 하는데
레퍼런스도 많이 없고 깃스타수는 많은데 보통 수정배포자가 아닌 이용자들만 많은 현황
그래서 Hexo로 결정하였다.

{% codeblock lang:javascript %}
    const var = "Hi";
    console.log(var);
{% endcodeblock %}
