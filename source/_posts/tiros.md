---
title: AWS의 Zelkova와 tiros란? AWS의 S3 Configuration Errors를 줄이려는 노력
author: Hamin
date: 2020-04-21 00:15:37
tags: [AWS, Security, Network]
categories:
- [papers]
- [Network]
- [Security]
- [papers]

thumbnail: /gallery/aws.png
---

Tiros는 네트워크 연결 가능성 추론 도구입니다. 이 도구는 상용 자동 논리 검증 도구를 사용하여 보안 취약성의 원인이 되는 네트워크 구성 오류를 찾아냅니다. Tiros는 최근 Amazon inspector 서비스에 도입되어 현재 고객이 클라우드에서 애플리케이션을 빌드하는 데 사용 중인 네트워크 보안 분석 기능의 기초가 됩니다. 또한 Tiros는 AWS 내에서 기존 AWS 네트워킹 기능을 기반으로 구축 된 다양한 AWS 서비스의 규정 준수 검증과 보안 불변성 준수를 자동화하는 데에도 사용됩니다.

<!-- more -->

{% asset_img tiros0.jpg %}

# Configuration Error로 인한 피해들.

Amazon은 AWS S3의 Configuration errors의 가능성을 줄이기 위해 두 가지 새로운 도구인 Zelkova와 Tiros를 개발하여 누가 데이터와 리소스에 액세스 할 수 있고 이를 사용하여 무엇을 할 수 있는지를 명확하게 파악하고 있습니다. 이 툴은 액세스 제어를 분석 및 평가하고 클라우드 환경의 개방성을 매핑합니다.

Aws는 고객이 클라우드 환경을 안전하게 보호하는하는 데 어려움을 겪고 있습니다. Misconfiguration Errors는 여전히 흔한 일이며, 엄청난 양의 민감한 데이터가 노출되도록 하였습니다. Verizon, BoossAllenHamiltion, WWE 재단, Alteryx, USNationalCredtiFederation, AustraliaBroadcastionCorporation, Accenture를 포함한 기관들은 모두 configuration Error로 인해 정보를 노출시켰습니다.

AWS는 이전에 서비스 보안을 향상시키고 configuration Errors 발생 가능성을 줄이기 위해 노력하였습니다. 작년에 AWS에 저장된 중요한 데이터를 자동으로 검색하고 보호하기 위한 머신러닝 툴인 Macie와 기본 암호화, 상세 재고 보고서, 허가 확인 등 S3 보안을 개선하기 위한 일련의 새로운 기능을 출시하였습니다.

2018년 2월, FedEx가 여권, 운전 면허증, 고객 기록 등 10만 건 이상의 문서를 유출당했다는 뉴스가 전해진 지 불과 며칠만에 AWS는  S3 Bucket Permissions Check서비스를 모든 사용자에게 무료로 제공했습니다.

AWS S3(SimpleStorageService)는 이 회사의 객체 스토리지 제품입니다. Skihigh Networks에 따르면 모든 S3 버킷의 7%는 제한 없이 공개 액세스 할 수 있으며 35%는 암호화되지 않은 상태로 남아 있습니다. 최근 노출되거나 보호되지 않은 데이터의 예로는 50만대 이상의 차량 추적 장치의 로그인 자격 증명, 2억명의 미국 유권자 기록, 미 육군 정보국에 속하는 민감한 데이터 등이 있습니다. 정보를 훔치는 것뿐만 아니라, 해커들은 랜섬 웨어로 데이터를 차단하고 암호를 알아내기 위해 컴퓨팅 자원을 사용하는 것이 발견되었습니다.

# 아마존 Zelkova와 Tiros가 하는 것.

Zelkova와 Tiros는 Amazon 제품에 대한 검증 도구와 기술을 개발하는 AWS의 ARG(Automated Describe Group)에 의해 만들어졌습니다. 자동화된 추론은 공식적인 검증 방법으로서, semantic한 추론을 취하고 수학 공식을 특정 질문에 대한 대답과 정책이 예상대로 작동하는지 검증하는 것입니다. ARG는 AWS보안 팀에 속해 있으며 2년 이상 내부적으로 툴을 개발해 왔습니다.

2018년 6월에 처음 발표된 Zelkova는 자동화 된 추론을 사용하여 정책과 정책의 향후 결과를 분석합니다. 회사의 IAM(Identity and Access Management), S3 및 기타 리소스 정책과 호환되는 이 솔루션을 사용하면 조직에서 벤치 마크를 생성하고 현재 정책 설정의 결과를 사용자에게 알릴 수 있습니다. 예를 들어 S3정책에 대해 사용되는 경우 권한이 없는 사용자가 버킷을 읽거나 쓸 수 있는지 여부를 알려 줍니다.

Tiros는 네트워크 간의 연결을 매핑 합니다. 예를 들어 인터넷에서 EC2 인스턴스에 연결 할 수 있는지 여부와 관련된 질문에 대답 할 수 있습니다.

Zelkova와 Tiros는 Internal tools로 시작했습니다. Zelkova는 S3 대시 보드의 일부로 내부적으로 사용되며 AWS Macie 내에서 사용됩니다. 투자 관리 회사인 Bridgewater Associates는 초기에 이들을 테스트할 수 있는 권한을 부여 받았습니다.

Bridgewater Associates의 수석 클라우드 보안 설계자인 DanPeebles는 "브리지 워터는 Zelkova를 사용하여 데이터 유출, Misconfiguration  및 기타 악의적이고 우발적인 바람직하지 않은 행동을 허용하지 않음을 확인하고 보장합니다."라고 Zelkova announcement에서 말했습니다. "Zelkova를 통해 보안 전문가들은 이해한 내용을 한번에 인코딩한 다음 이를 관련 정책에 기계적으로 적용하여 오류가 발생하기 쉽고 느린 인적 검토를 방지하는 동시에 IAM 정책의 정확성과 보안에 대한 높은 신뢰도를 제공할 수 있습니다."

현재 두 tool 모두 공개용으로 사용할 수 없습니다. Bridgewater는 그들이 특히 사용자 친화적이지 않은 "raw state"에 있다고 말한다. 아마존은 더 넓은 이용 가능성이나 가격에 관한 어떠한 정보도 공개하기를 거부했다.

# Amazon의 클라우드 Configuration challenges.

Amazon 및 Microsoft Azure와 같은 클라우드 공급 업체는 서비스와 관련하여 일정 수준의 보안을 제공하고 권장 모범 사례를 제공하지만, 여전히 많은 책임이 고객에게 있는 공유 보안 모델 아래에서 운영되며, 이러한 모델에서는 문제가 자주 발생합니다. 영국에 본사를 둔 MSP Claranet의 선임 사이트 안정성 엔지니어인 SteveSmith는 "AWS S3버킷 주변에서 보고되고 있는 보안 문제는 플랫폼 자체와 거의 관련이 없다"며 "그러나 가장 큰 약점인 플랫폼을 사용하는 사람들과 관련된 모든 것"이라고 말했다.

"AWS는 configuration을 support 하도록 설계 된 많은 합리적인 기본 값을 설정합니다. S3버킷은 기본적으로 private이지만, 안타깝게도 플랫폼을 사용하는 방법을 모르면 문제를 쉽게 야기할 수 있습니다."라고 Smith는 덧붙였습니다.

교육이 부족하고 구축 및 관리 툴의 복잡성이 증가하고 관리자가 대처해야 하는 서비스의 수가 계속 증가하고 있으며, 클라우드 환경은 보안 팀의 시야에서 벗어나 쉽게 전환될 수 있으며, 데이터 유출이 계속해서 일상적인 문제가 되고 있습니다.

로그 관리 및 분석 공급 업체 (및 AWS고객) Sumo Logic의 George Gerchow는 "소비자는 shared responsibility model을 이해하고 데이터를 보호하기 위해 best practice를 적용하여 역할을 수행해야 합니다."라고 말합니다. "AWS는 이러한 교육을 제공하기 위해 많은 것을 하지 않기 때문에 소비자들은 모든 것이 잘 이루어졌다고 믿기 쉽습니다."

AWS는 이러한 새로운 툴을 사용하여 사람이 실수할 가능성을 줄이고 데이터 누출 가능성을 줄이는 방안을 모색하고 있습니다. 하지만 그들이 도와 줄까요? "우리의 보안 목표는 AWS에서 데이터가 유출되는 것을 막는 것입니다."라고 Bridgewater Associates의 보안 설계자 Greg Frascadore는 AWS New York Summit에서 Zelkova와 Toris에 관한 프레젠테이션에서 말했습니다. "우리가 찾고자 하는 것은 공식적인 분석과 우리가 설치한 보안 통제 장치가 우리가 생각하는 대로 작동하고 있는지 확인하는 체계적인 방법입니다."

Frascadore는 이러한 툴의 사용 사례에는 개별 보안 제어 검증, 보안 제어 관련 벤치 마크 생성, 다수의 고객에게 관련 제어 검색, 검증 자동화, 설계 단계에서 검증 등이 포함된다고 말했습니다. " 이러한 도구의 가장 중요한 점은 설계 단계에서 확인할 수 있다는 것입니다. 실제로 AWS 인프라를 변경하기 전에 보안 검증을 통해 계정에 취약점을 적용할 수 있었으면 합니다."라고 그는 말했습니다.

다른 이들은 이러한 새로운 도구가 장점과 잠재적인 단점을 모두 가지고 있다고 경고한다. Sumo Logic의 Gerchow는 시작은 좋지만 비용이 많이 들고 올바르게 구성해야 하므로 복잡성이 가중될 수 있으며, 멀티 클라우드 또는 하이브리드 구축에 도움이 되지 않는다고 말합니다.

BTB Security의 수석 정보 보안 advisor인 MattWilson은 "Zelkova와 Tiros에게 엄청난 잠재적 이익이 있지만 이러한 팀은 데이터를 운영할 수 있어야 합니다. 그렇지 않으면 그들은 단지 도구를 사용하고 있을 뿐입니다."라고 말합니다. "그들은 여전히 조직의 누군가가 그들을 execute하고, 그들의 output을 분석하고, 제공된 정보에 따라 행동할 것을 요구하고 있습니다. 고급 알고리즘과 매끄러운 인터페이스도 좋지만, 이것들 만으로는 충분하지 않습니다."

출처 : {% link CSO online US  https://www.csoonline.com/article/3298166/what-are-amazon-zelkova-and-tiros-aws-looks-to-reduce-s3-configuration-errors.html [external] [title] %}

Paper : {% link Reachability Analysis for AWS-based Networks https://d1.awsstatic.com/whitepapers/Security/Reachability_Analysis_for_AWS-based_Networks.pdf [external] [title] %}