---
title: Deep Learning for Self-Driving Cars
author: Hamin
date: 2020-04-08 17:34:12
tags: [Deep Learning, Machine Learning, javascript, AI]
categories: 
- [AI]
- [Toy Project]
thumbnail: /gallery/deepTraffic.png
---

자율주행차 MIT 딥러닝 코스의 딥러닝(Deep Learning for Self-Driving Cars) 과정 중 {% link DeepTraffic https://selfdrivingcars.mit.edu/deeptraffic-documentation/ [external] [title] %}을 참고하여 포스팅을 작성하였습니다. 
고속도로 교통량이 밀집한 곳을 통해 차량(또는 복수의 차량)을 최대한 빨리 주행할 수 있는 신경망을 만드는 것이 목표이다.

<!-- more -->

신경망의 한 예가 차들 중 하나를 제어(빨간색으로 표시됨) 
최대한 빨리 가기 위해 효율적으로 탐색하는 방법을 배워야 한다.
차는 이미 안전장치가 달려있기 때문에 당신은 운전의 기본적인 과제에 대해 걱정할 필요가 없다
뉴럴넷은 가속/감속 또는 차선을 변경해야 하는지 자동차에게 알려주면 됨
그리고 그것이 다른 차들과 충돌하지 않고 가능하다면 그렇게 할 것이다. 
아래 것들을 참고하자

~~~javascript
@inproceedings{fridman2018deeptraffic,
author = {Lex Fridman and Jack Terwilliger and Benedikt Jenik},
title = {DeepTraffic: Crowdsourced Hyperparameter Tuning of Deep Reinforcement Learning
         Systems for Multi-Agent Dense Traffic Navigation},
booktitle = {Neural Information Processing Systems (NIPS 2018)
             Deep Reinforcement Learning Workshop}
year = {2018},
url = {http://arxiv.org/abs/1801.02805},
doi = {10.5281/zenodo.2530457}
archivePrefix = {arXiv},
}
~~~

## 개요

{% asset_img Overview.png %}

게임 페이지는 4개의 다른 영역으로 구성되어 있다.

- 왼쪽에는 다양한 디스플레이 옵션으로 도로의 실시간 시뮬레이션을 볼 수 있다.
- 페이지 상단에서 
    (1) 작용제를 제어하는 신경망의 설계를 변경할 수 있는 코딩 영역과 
    (2) 변경사항 적용, 저장/로드 및 제출을 위한 일부 버튼을 찾을 수 있다.
- 코딩 영역 아래에서 
    (1) 중앙 적색 자동차의 보상의 이동 평균을 보여주는 그래프, 
    (2) 신경망 활성화의 시각화, 
    (3) 네트워크 훈련 및 테스트를 위한 버튼을 찾을 수 있다.

시뮬레이션된 도로와 그래프 사이에서 차량의 현재 이미지와 사용자 정의하기 위한 몇 가지 옵션을 찾을 수 있으며 가장 적합한 하위 항목의 시각화를 만들 수 있다.
시뮬레이션 영역에는 현재 차의 속도나 사이트를 연 이후 통과된 차량의 수와 같은 기본적인 정보가 표시된다. 또한 시뮬레이션 표시 방법을 변경할 수 있다.

{% asset_img simulation.gif %}

시뮬레이션은 내부 시간 척도로 프레임을 사용하기 때문에 느린 컴퓨터도 느린 뉴럴 넷도 결과에 영향을 미치지 않는다. 
Simulation Speed 설정을 사용하면 시뮬레이션이 실시간으로 일치하는 프레임을 그리려고 하기 때문에 실제 계산이 더 빨리 진행되는지 대기하는 지 등 시뮬레이션이 완료되는 즉시 프레임을 표시하므로 시뮬레이션 속도 설정을 통해 시뮬레이션이 표시되는 방법을 제어할 수 있다.

내부적으로는 게임 전체가 그리드 시스템으로 운영된다. Road Overlay를 전체 맵으로 변경하면 볼 수 있다.

{% asset_img roadOverlay.png %}

각각의 자동차에 대해 그 아래의 그리드 셀은 자동차의 속도로 채워지고, 빈 셀은 속도의 잠재력을 상징하기 위해 높은 값으로 채워진다.

당신의 차는 신경망의 입력으로 사용하기 위해 그 지도에서 자동차 중심의 컷아웃을 얻는다. 도로 오버레이를 학습 입력으로 변경하여 살펴보십시오.

{% asset_img roadOverlay2.png %}

다음 변수는 네트가 얻는 입력의 크기를 조절한다. 
입력 영역이 클수록 트래픽 상황에 대한 더 많은 정보를 제공하지만, 관련 부분을 배우는 것이 더 어려워지고, 학습 시간이 더 길어질 수 있다. 
(그러나 당신은 시작 샘플에 우리가 가지고 있는 입력 크기를 확실히 변경해야 한다. 그것은 자동차를 본질적으로 블라인드(?)로 만든다)

~~~javascript
lanesSide = 1;
patchesAhead = 10;
patchesBehind = 0;
~~~

다른 모든 자동차에 전원을 공급하는 기본 알고리즘, 그리고 당신의 기본은 안전 시스템이라고 불리는데, 당신은 도로 오버레이를 바꾸면 그것을 볼 수 있다.

{% asset_img roadOverlay3.png %}

강조 표시된 셀은 현재 빨간색인 경우 안전 시스템이 해당 방향으로 이동하는 것을 보여 준다. 
안전장치 전면부는 장애물에 부딪히지 않기 위해 차의 속도를 늦추게 한다. 
강조 표시된 부분에 다른 차량이 있는 동안 그리고 이미 차선을 바꾸는 과정에 있는 경우 차선 전환이 비활성화된다. 
얼마나 빨리 가려고 하느냐에 따라 점검 구역이 늘어나기 때문에 flooring은 항상 좋은 것은 아니다.

에이전트는 현재 상태(정의된 학습 입력 컷아웃의 평평한 배열로 제공됨)를 수신하는 학습이라는 함수에 의해 제어되며, 
마지막 단계에 대한 보상(이 경우 평균 속도(mph))을 받고 다음 동작 중 하나를 반환해야 한다.

~~~javascript
var noAction = 0;
var accelerateAction = 1;
var decelerateAction = 2;
var goLeftAction = 3;
var goRightAction = 4;
~~~

에이전트에 속도와 차선을 유지하라고 지시하는 가장 기본적인 학습 기능은 다음과 같다.

~~~javascript
learn = function (state, lastReward) {
    return 0;
}
~~~

경기를 위해 신경망을 사용하여 자동차를 제어해야 하는 – 학습 기능

~~~javascript
learn = function (state, lastReward) {
    brain.backward(lastReward);
    var action = brain.forward(state);

    draw_net();
    draw_stats();

    return action;
}
~~~

이 일이 일어나도록 하는 것은 이미 초기 코드 샘플에 제공되어 있고 그대로 유지될 수 있다 
당신은 물론 그 상태를 인터넷에 공급하기 전에 당신 자신의 데이터 사전 처리를 할 수 있지만 시간을 너무 많이 쓰지는 마라.
대부분의 (전부는 아니더라도) 개선은 net 적응에서 이루어져야 한다
(그리고 당신은 코스를 통과하는 데 필요한 최소값을 훨씬 초과하여 사전 처리를 전혀 하지 않고 상당히 적절한 속도를 얻을 수 있다).

## 교육 및 평가
신경망을 훈련하려면 교육 실행 버튼을 눌러야 한다.

{% asset_img training.png %}

이렇게 하면 실시간 속도가 약 30배인 별도의 스레드에서 시뮬레이션을 실행하여 신경망 훈련을 시작하고 훈련된 그물을 가시적 시뮬레이션에 수시로 다시 적용하여 즉각적인 개선 효과를 볼 수 있어야 한다.(당신의 순 레이아웃이 물론 좋은 경우에만).

이 사이트는 또한 우리가 경기에 사용하는 것과 동일한 평가를 실행할 평가 버튼을 제공한다.

{% asset_img evaluation.png %}

Evaluation Run은 또한 각각 약 30초 동안 500회 주행 시뮬레이션하는 별도의 스레드에서도 이루어진다. 
각 주행에 대해 주행당 평균 속도를 계산하며, 최종 점수는 500회 주행의 중간 속도가 된다.

다른 차들이 어떻게 움직이는지에 관련된 임의의 요소들이 있기 때문에, 당신의 지역 평가는 실제 점수에 대한 추정치만을 준다는 것을 명심해야 한다. 
관련 점수는 우리가 계산한 것과 같다. (그리고 우리는 또한 부정행위와 관련된 어떤 종류의 것이 있는지 알아보기 위해 당신의 코드를 볼 것이다. 그것은 금지시킬 것이다 – 그러니 시도조차 하지 말라.

당신은 당신의 프로필 페이지에서 최고의 속도를 찾을 수 있다. 당신이 정말로 10위 안에 든다면.

## 여러 차량 제어

최대 10대의 차량을 제어할 수 있다. 다음 코드 행 변경을 통해서

~~~json
otherAgents = 9; // plus the tracked agent
~~~

각 에이전트, 집합적으로 안 할 수 있는 알고리즘의 인스턴스를 운영하고 있다. 빨리 운전하려면 네트워크 교통 체증이 유발되고 것을 피하는 방법을 배워야 할 것이다. 여러 agent들을 이용하면, 너의 점수는 올라갈 것이다.

## 신경망 설계

우리가 제공하는 기본 신경망 레이아웃을 변경하려면(잘못 수행되도록 의도적으로 변경됨) 웹사이트의 코드 박스에서 코드를 변경해야 한다.

{% asset_img apply_and_reset.png %}

apply code 버튼은 코드를 실행하여 새로 정의된 신경망을 생성한다 (조심: 이전에 사용했던 훈련 상태를 잃게 된다).

{% asset_img save_and_load.png %}

그리고 저장 및 로드 버튼을 사용하면 코드와 훈련된 네트 상태를 기계에 저장한 후 다시 로드할 수 있다. 규칙적으로 저장하라!

## 코드

~~~javascript
lanesSide = 0;
patchesAhead = 1;
patchesBehind = 0;
trainIterations = 10000;
~~~

가장 기본적인 설정을 정의 
더 큰 입력의 경우 train iterations 숫자를 늘려야 한다. 
사실 몇 개의 패치를 앞에 두고, 그리고 적어도 옆으로 가는 한 개의 차선도 아마 좋은 생각일 것이다.

~~~javascript
var num_inputs = (lanesSide * 2 + 1) * (patchesAhead + patchesBehind);
var num_actions = 5;
var temporal_window = 3;
var network_size = num_inputs * temporal_window + num_actions * temporal_window + num_inputs;
~~~

입력에 대한 자세한 내용을 지정 
임시 창은 제외하고 해당 부품을 만질 필요가 없음.

넷은 변경할 필요가 없는 입력부터 시작하는 계층의 배열로 정의된다.

~~~javascript
var layer_defs = [];
layer_defs.push({
    type: 'input',
    out_sx: 1,
    out_sy: 1,
    out_depth: network_size
});
~~~

하나의 뉴런으로 이루어진 히든 기본 레이어를 추가해 놓았다 어떻게 하는지를 보여주기 위해서-이 부분도 너가 바꿔줘야 한다.

~~~javascript
layer_defs.push({
    type: 'fc',
    num_neurons: 1,
    activation: 'relu'
});
~~~

그리고 결국 작용에 대해 결정하는 최종 회귀층이 있는데, 그것은 아마도 다음과 같다.
~~~javascript
layer_defs.push({
    type: 'regression',
    num_neurons: num_actions
});
~~~
Q-Learning 파트에는 훨씬 더 많은 옵션이 있다. 자세한 내용은 다음 링크의 코드에 있는 코드에 설명되어 있다. 
https://github.com/karpathy/convnetjs/blob/master/build/deepqlearn.js 
이것들은 당신의 네트의 보다 진보된 최적화에 대부분 흥미롭다.

~~~javascript
var tdtrainer_options = {
    learning_rate: 0.001,
    momentum: 0.0,
    batch_size: 64,
    l2_decay: 0.01
};

var opt = {};
opt.temporal_window = temporal_window;
opt.experience_size = 3000;
opt.start_learn_threshold = 500;
opt.gamma = 0.7;
opt.learning_steps_total = 10000;
opt.learning_steps_burnin = 1000;
opt.epsilon_min = 0.0;
opt.epsilon_test_time = 0.0;
opt.layer_defs = layer_defs;
opt.tdtrainer_options = tdtrainer_options;
~~~

그리고 마지막 단계는 뇌를 만드는 것이다.

~~~javascript
brain = new deepqlearn.Brain(num_inputs, num_actions, opt);
~~~

## Submission
평가를 위해 신경망을 제출하려면 제출 버튼을 누르십시오.

{% asset_img submit.png %}

교육 및 현지 평가를 먼저 실행하고, 성과에 만족하는 경우에만 제출하십시오. 
당신은 여러 번 제출할 수 있고 우리는 최선의 결과를 얻을 수 있지만, 너무 자주 하는 것은 좋지 않다: 
제출은 우리의 평가 대기열 뒤에 당신의 Net을 더하지만, 당신은 거기에 한 자리만 있을 수 있기 때문에 평가가 끝나기 전에 다시 제출하면, 당신은 다시 뒤에 부딪히게 된다. 평가 결과를 보려면 프로필 페이지로 이동하십시오.

이 클래스에 공식적으로 등록한 경우 이 할당에 대한 학점을 취득하려면 65mph 이상의 성능을 발휘해야 한다.

## Customization & Visualization

시뮬레이션된 고속도로와 그래프 사이에는 차량의 이미지, DeepTraffic의 모양을 사용자 지정하는 옵션, 최상의 제출물 시각화를 요청하는 버튼이 있다.

{% asset_img visualizaion.png %}

사용자 지정 차량 이미지(png 파일만 해당)를 업로드하려면 LOAD CUSTOM IMAGE 버튼을 클릭하십시오. 
png 파일을 선택한 후 268×586 이미지로 잘라야 한다. 
드롭다운 색상 선택기를 사용하여 중앙 에이전트의 추적과 같은 색상표를 사용자 정의할 수도 있다. 
제출을 완료한 후 최상의 성능을 시각화하도록 요청할 수 있다. 이 시각화는 .mp4 파일이다. 
준비가 되면 프로필 페이지에서 다운로드 링크를 찾을 수 있다.

~~~javascript

//<![CDATA[

// a few things don't have var in front of them - they update already existing variables the game needs
lanesSide = 3;  // 옆 패치???
patchesAhead = 50;   // 앞 패치
patchesBehind = 10;  // 뒷 패치
trainIterations = 500000;    // 더 큰 input의 경우 늘려줘야

// the number of other autonomous vehicles controlled by your network
otherAgents = 10; // max of 10 // 몇대의 차를 제어할 것인가...

var num_inputs = (lanesSide * 2 + 1) * (patchesAhead + patchesBehind);
var num_actions = 5;
var temporal_window = 0;
var network_size = num_inputs * temporal_window + num_actions * temporal_window + num_inputs;

var layer_defs = [];
    layer_defs.push({
    type: 'input',
    out_sx: 1,
    out_sy: 1,
    out_depth: network_size
});
layer_defs.push({
    type: 'fc',
    num_neurons: 36,
    activation: 'tahn'
});
layer_defs.push({
    type: 'fc',
    num_neurons: 24,
    activation: 'tanh'
});
layer_defs.push({
    type: 'fc',
    num_neurons: 24,
    activation: 'tahn'
});
layer_defs.push({
    type: 'fc',
    num_neurons: 24,
    activation: 'tanh'
});
layer_defs.push({
    type: 'regression',
    num_neurons: num_actions
});

var tdtrainer_options = {
    learning_rate: 0.001,
    momentum: 0.0,
    batch_size: 128,
    l2_decay: 0.01
};

var opt = {};
opt.temporal_window = temporal_window;
opt.experience_size = 100000;
opt.start_learn_threshold = 5000;
opt.gamma = 0.98;
opt.learning_steps_total = 500000;
opt.learning_steps_burnin = 1000;
opt.epsilon_min = 0.0;
opt.epsilon_test_time = 0.0;
opt.layer_defs = layer_defs;
opt.tdtrainer_options = tdtrainer_options;

brain = new deepqlearn.Brain(num_inputs, num_actions, opt);

learn = function (state, lastReward) {
    brain.backward(lastReward);
    var action = brain.forward(state);

    draw_net();
    draw_stats();

    return action;
}

//]]>

~~~