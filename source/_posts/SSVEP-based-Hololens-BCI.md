---
title: (프로젝트)SSVEP 기반 홀로렌즈 BCI
author: Hamin
date: 2020-03-22 23:09:33
tags: [unity, c#, Hololens, vuforia]
categories: 
- [Projects, Conelab]
- [Unity]
thumbnail: /gallery/hololens.png
---
이 프로젝트는 한양대학교 Conelab에서 학부생 프로그래머로써 개발한 프로젝트입니다.
개발 기간은 2018년 12월 ~ 2020년 2월 입니다.
홀로렌즈 어플 인터페이스와 EMG신호(SSVEP), vuforia 영상 인식 기술을 통해 사용자의 의향을 파악하여 로봇청소기, 가습기, 공기청정기, 에어컨, 전구의 전원, 충전, 바람 세기 등 여러 기능들을 조정하는 BCI 시스템입니다.
홀로렌즈 인터페이스는 Unity를 기반으로 C#과 vuforia를 이용하여 만들었습니다.

<!-- more -->

## 소스코드

{% link Hololens App source code repository https://github.com/hamin7/SSVEP_based_BCI [external] [title] %} 에 가시면 Unity와 C#, Vuforia를 이용해 개발한 홀로렌즈 어플의 소스코드를 볼 수 있습니다.


## 온라인 실험의 전체 절차
(a) 제안된 가전 제어 시스템의 계층 구조. 

반복되는 눈 깜박임으로 제어 시스템이 작동한다. 선택된 가전제품에 지정된 명령을 전달하고 나면 사용자의 반복적인 눈 깜빡임으로 제어 시스템이 꺼진다. 

(b) "기기 선택" 단계에 제시된 시각적 자극. (c), (d), (e)는 로봇 진공, 공기청정기, 가습기를 각각 제어하기 위해 "명령 선택" 단계에서 제시한 시각적 자극을 각각 보여준다.

{% asset_img onlineExperimentProcedure.png %} <br>

## 제어 대상 기기 선택 방법 - 3종

이름|방식|선택 가능 기기 갯수|사용 시나리오|참고 그림
-|-|-|-|-
QR 코드|선택하고자 하는 기기에 부착된 QR코드 응시|제한 없음|눈앞에 있는 기기를 선택할 때 사용|{% asset_img qr.png %}
SSVEP|기기에 해당하는 SSVEP 자극 응시|3개|눈앞에 없는 기기 중 자주 사용하는 기기를 등록해두고 사용|{% asset_img ssvep.png %}
눈 움직임 패턴|눈으로 숫자 0~9에 해당하는 패턴 중 하나를 그려 선택|10개|눈앞에 없고 가끔 사용하는 기기를 선택할 때 사용|{% asset_img eye.png %}

## 프로세스 절차

{% asset_img process1.png %}

## 통신값 (BCI -> Middleware)

{% asset_img communication.png %}

## 기기별 명령 정리

<table>
       <thead>
           <tr>
               <th>기기 번호</th>
               <th>기기명</th>
               <th>명령 번호</th>
               <th>제어 명령</th>
               <th>기타</th>
           </tr>
       </thead>
       <tbody>
            <tr>
               <td rowspan=4>1</td>
               <td rowspan=4>로봇청소기</td>
               <td>1</td>
               <td>전원 on/off</td>
               <td rowspan = 9>기존과 동일</td>
            </tr>
            <tr>
                <td>2</td>
                <td>터보모드</td>
            </tr>
            <tr>
                <td>3</td>
                <td>충전복귀</td>
            </tr>
            <tr>
                <td>4</td>
                <td>Back</td>
            </tr>
            <tr>
               <td rowspan=4>2</td>
               <td rowspan=4>공기청정기</td>
               <td>1</td>
               <td>전원 on/off</td>
            </tr>
            <tr>
                <td>2</td>
                <td>바람-자동</td>
            </tr>
            <tr>
                <td>3</td>
                <td>모드-취침</td>
            </tr>
            <tr>
                <td>4</td>
                <td>Back</td>
            </tr>
            <tr>
               <td>3</td>
               <td>가습기</td>
               <td colspan=2>공기청정기와 동일</td>
            </tr>
            <tr>
               <td rowspan=4>4</td>
               <td rowspan=4>에어컨</td>
               <td>1</td>
               <td>전원 on/off</td>
               <td rowspan = 8>신규 추가</td>
            </tr>
            <tr>
                <td>2</td>
                <td>온도조절(순환식(18도 -> 21 -> 24 -> 27 -> 18 -> ...))</td>
            </tr>
            <tr>
                <td>3</td>
                <td>운전모드(순환식(auto -> cool -> dry -> auto -> ...))</td>
            </tr>
            <tr>
                <td>4</td>
                <td>Back</td>
            </tr>
            <tr>
               <td rowspan=4>5</td>
               <td rowspan=4>전등</td>
               <td>1</td>
               <td>전원 on/off</td>
            </tr>
            <tr>
                <td>2</td>
                <td>색상(순환식(r -> g -> b -> r -> ...</td>
            </tr>
            <tr>
                <td>3</td>
                <td>밝기(순환식(33% -> 66 -> 99 -> 33 -> ...</td>
            </tr>
            <tr>
                <td>4</td>
                <td>Back</td>
            </tr>
            <tr>
                <td colspan=4></td>
            </tr>
       </tbody>
</table>


<br>
<br>
<br>
<br>

{% asset_img capture0.png %} <br>

먼저 전구를 조종해보겠습니다 <br>

{% asset_img capture1.png %} <br>



{% asset_img capture2.png %} <br>

전구를 쳐다보면 vuforia library를 통해 전구를 인식할 수 있습니다. <br>

{% asset_img capture3.png %} <br>

전구가 선택 되었고, 전구의 기능중에 어떤 것을 수행할 지 고르는 화면입니다 <br>

{% asset_img capture4.png %} <br>

왼쪽 위의 아이콘을 쳐다보면 EOG 신호를 통해 전구 ON/Off기능을 선택할 수 있습니다. <br>

{% asset_img capture5.png %} <br>

불이 켜졌고 이제 다시 다른 명령어를 고를 수 있습니다. <br>

{% asset_img capture6.png %} <br>

오른쪽 위 아이콘을 쳐다보면 전구 색상을 바꿀 수 있습니다. <br>

{% asset_img capture7.png %} <br>

전구가 빨간색으로 바뀌었습니다. 전구 색상은 흰색 - 빨간색 - 파란색 순으로 바뀝니다. <br>

{% asset_img capture8.png %} <br>

왼쪽 아래 아이콘을 쳐다보면 전구의 밝기를 조절할 수 있습니다 <br>

{% asset_img capture9.png %} <br>

전구가 어두워졌고 이제 왼쪽 위 아이콘을 바라보아 전구를 끄고 메인화면으로 나가겠습니다. 오른쪽 아래 아이콘을 쳐다보면 메인으로 나갈 수 있습니다. <br>

이런식으로 로봇청소기와 공기청정기, 에어컨, 가습기도 조종할 수 있습니다.

{% asset_img capture10.png %} <br>

로봇 청소기를 컨트롤 해보겠습니다. <br>

{% asset_img capture11.png %} <br>

참고로 눈을 깜박이면 시스템을 시작할 수 있습니다. <br>

{% asset_img capture12.png %} <br>

vuforia 이미지 처리가 아닌 SSVEP 자극을 통한 시선 처리로도 기기를 고를 수 있습니다. <br>
원하는 기기를 쳐다보면 선택할 수 있습니다 <br>

{% asset_img capture13.png %} <br>

로봇 청소기를 골랐으니 이전 처럼 똑같이 원하는 기능을 SSVEP 자극이 깜박거릴 때 쳐다봐 골라주면 됩니다. <br>

{% asset_img capture14.png %} <br>

로봇 청소기가 켜졌습니다. <br>

{% asset_img capture15.png %} <br>

눈을 깜박여 SSVEP 모드 변경 화면을 켜 모드를 전환해주겠습니다. <br>

{% asset_img capture16.png %} <br>

터보모드를 선택했습니다. <br>

나머지 기능들도 똑같이 사용할 수 있습니다. <br>

{% asset_img capture17.png %} <br>

이제 가습기를 제어해보겠습니다. <br>

{% asset_img capture18.png %} <br>

다른 기기들과 비슷하게 SSVEP 자극이 깜박일 때 시선으로 선택해 주면됩니다. <br>

{% asset_img capture19.png %} <br>

이런식으로 Sleep 모드로 바꿔줄 수도 있습니다. <br>

{% asset_img capture20.png %} <br>

이제 에어컨을 조종해보겠습니다. <br>

{% asset_img capture21.png %} <br>

이번엔 눈글씨를 통해 기기를 골라보겠습니다. <br>

{% asset_img capture22.png %} <br>

시선을 통해 9개 점에 원하는 기기의 번호를 그리면 됩니다. <br>

{% asset_img capture23.png %} <br>

에어컨이 선택되었습니다. <br>

{% asset_img capture24.png %} <br>

마찬가지로 모드, 온도조절 등 기능을 선택하여 조종하면 됩니다. <br> 

{% asset_img capture25.png %} <br>

오른쪽 위는 온도, 왼쪽 아래는 운전모드 변경입니다. <br>

{% asset_img capture26.png %} <br>

마지막으로 공기청정기를 조종해보겠습니다. <br>

{% asset_img capture27.png %} <br>

이번에도 눈글을 통해 기기를 선택합니다. <br>

{% asset_img capture28.png %} <br>

공기청정기가 선택되었습니다. <br>

{% asset_img capture29.png %} <br>

다른 기기와 비슷하게 기능을 선택하여 조종해 줍니다.  <br>


