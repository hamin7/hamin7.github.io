---
title: ARM 및 그 응용 프로그램에서 하드웨어 지원 명령 레벨 분리 (1)
author: Hamin
date: 2020-03-25 00:09:42
tags:
thumbnail: /gallery/computer-security.jpg
---

이 포스트는 한양대학교 CSE 융합세미나의 조영필 교수님의 강연을 바탕으로 작성되었습니다.

ARM architecture는 최근 스마트폰, IoT 디바이스 등에서 매우 폭발적으로 많이 사용되고 있는 아키텍쳐이다. 그리고 이 ARM 아키텍쳐에는 다양한 하드웨어 feature들이 있다. 이러한 feature들을 이용한 security system을 만들어 이를 이용해 어떻게 우리가 보안성을 향상시킬 수 있는가가 이 포스트의 포인트이다.

<!-- more -->

우리는 다양한 보안 위협에 처해있다. 그렇기 때문에 Security Mechanism을 만들어 보안 위협을 대비해야 한다.

{% asset_img image1.png %}

이러한 Security Mechanism들은 software only로 개발되어 왔는데 가장 큰 이유는 적용이 유리하기 때문이다.

하지만 이러한 software mechanisma들은 performance overhead가 상당하다.

{% asset_img image2.png %}

우리가 시스템을 구현함에 있어서 5%만 느려져도 느려진것이라고 느끼는데 50%~300%의 상당히 overhead가 많다고 볼 수 있다.
이 overhead를 줄이기 위해서는 security guarantee를 줄여야 하는데 그렇게 되면 보안성이 낮아진다.

그래서 이를 해결하기 위해서 Security Mechanism을 구현함에 있어서 hardware의 도움을 받아야 한다.

Isolation
- Security Mechanism에서 효과적인 "code size"를 줄임으로써 공격 가능성을 줄인다.
- 왜 Code Size를 줄이는가?
    시스템을 개발하는 과정에서 코드가 길어질 수록 code의 vulnerability가 발생할 가능성이 높기 때문.

{% asset_img image3.png %}

small SW entity의 경우 본인을 직접 공격하는 경우도 있겠지만 Large SW entity를 장악하고 small SW entity공격도 가능.

이 문제를 해결하기 위해 isolate 해야 한다.

{% asset_img image4.png %}

이 isolate의 적용 예는 다음과 같다

{% asset_img image5.png %}

intel SGX의 경우 Enclave가 완전히 격리 되어있다.

(% asset_img image6.png %)

이러한 isolation을 Domain-level Isolation이라고 한다.
un-authorized code와 Authorized code 두 가지로 구분해 둘 수 있다.
두 코드는 서로 다른 access permision을 가진다.
un-authorized로 접근하려면 Crossing isolation boundaty를 뛰어 넘어야 하는데 이는 매우 무거운 연산이다.
그렇기 때문에 드물게 로그인 되는 경우에만 유용하다. 

Domain-level Isolation의 한계.
- 빈번하게 접속되는 데이터에 대해서는 민감하다
- 예) 
    - 스택의 리턴주소
    - heap objects들의 fuction pointer들
    - 로그인 여부 flag variable 같은 환경 변수들

이렇게 빈번하게 access되는 return address attack을 막기 위한 방법에는 Shadow Stack이라는 방법이 있다.

{% asset_img image7.png %}

Shadow stack은 앞에서 나온 isolation을 이용한 security mechanism이다.
원래 프로그램 내부에는 stack이 하나만 있지만, Shadow stack에서는 별도의 stack을 하나 더 둔다.
요약하자면 shadow stack만 isolation 되어있으면 return address가 잘 보호된다.
하지만 매번 함수호출마다 위처럼 네번의 crossing이 있다면 overhead가 커지게 될 것이다.

{% asset_img image8.png %}

Instruction-level Isolation은 위의 Domain-level Isolation과는 다르게 instruction 자체에 access permission을 부여.
이 경우 instruction 사이는 특별히 isolation boundary를 넘어갈 필요가 없다.
그렇기 때문에 Instructuon-level Isolation은 frequently 하게 access 되는 data 보호에 유리하다.

{% asset_img image9.png %}

그렇다면 이러한 instruction-level isolation을 어떻게 구현할까?
결국 키는 instruction별로 서로 다른 access permision을 줄 수 있느냐가 핵심이다.
그래서 이것을 구현할 수 있는 한가지는 segment라는 것이다.
이것은 x86-32bit에서만 지원이 된다.
각각의 segment register 별로 access permission과 bit수와 limit을 설정할 수 있는 Segment Descriptor가 존재한다.
그리고 instruction에 어느 segment register에 접근하는지 지정할 수 있다.
instruction 간에는 특별히 isolation boundary를 넣지 않아도 자연스럽게 segment descriptor에 따라 access permission 차이가 날 것이다.

{% asset_img image10.png %}

SFI는 instruction 앞에 체크하는 코드를 넣는 것이다.
이 방법의 단점은 하나만 보면 overhead가 크지 않지만 sensitive data 접속 빈도보다 normal data 접속 빈도가 더 많을 텐데 대부분의 instruction에 chk 코드를 넣어야 한다는 것이다.
결과적으로 전체적인 performance overhead가 수십퍼센트 증가할 것이다.

조금 더 효율적인 방법이 Information hiding이다.
Sensitive data를 임의의 장소에 위치를 시키고 그 위치를 특정 instruction에만 저장하는 것이다.
그래서 그 위치를 알고있는 instruction은 접근이 가능하지만 그렇지 않은 경우는 접근 불가능하다.
단점은 확률적인 보안이라는 것이다. 즉, normal instruction이 sensitive data의 위치를 모를것이라고 확신할 수 없다.

{% asset_img image11.png %}

그래서 이 논문은 어떻게 ARM에서 instruction-level Isolation을 구현 할 것인가를 다루었다.
메모리에 대해서 access를 하는데 일반적인 access permission과는 다른방법으로 access permission이 주어지는 그런 special한 memory instruction이 있을까에 대한 것이다.

LSU를 이용해 보자는 것이 이 논문의 주장이다.
왼쪽의 표가 ARM architecture에서 사용하는 memory instruction이다.
일반적인 load, store와 unprivileged가 붙은것들의 차이점은 우리가 원래 memory access를 한다고 했을 때, 그 load store instruction이 access 할 수 있는 memory는 결과적으로 load store instruction이 수행 될 때 프로세스가 어떤 mode에 있었는가에 따라서 결정된다.
가령 우리가 load store instruction을 application에서 실행했다면, 그러면 이 load, store instruction은 보통 프로세서가 unprivileged 모드로 작동한다. 쉽게 말하면 usermode이다. 그러니까 이 때는 user memory로 설정되어있는 memory만 access 할 수 있고, kernal의 memory는 access 불가능하다.
반대로 이 instruction을 kernal에서 실행했다면 보통 privilleged 모드로 실행된다. 그러니까 이 때 load store instruction은 kernal 모드로 chk되어 있는 메모리에 access 가능하다. 뿐만 아니라 user space로 설정되어있는 메모리에도 접근 가능하다.

오른쪽 표는 instruction에 설정해줄 수 있는 memory access permission 여부이다. 이 access permission은 page table에 설정 할 수 있다.

여기서 알 수 있는 점은 
- privileged 모드와 unprivileged 모드 간에는 access permission에 차이가 나는 경우가 있다
- load, store instruction은 어떤 mode에서 실행되느냐에 따라서 access permission이 결정.

