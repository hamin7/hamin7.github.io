---
title: Nmap으로 IP 스캔하기
author: Hamin
date: 2020-05-02 22:14:11
tags: [nmap, Network, Security, ]
categories:
- [nmap]
- [Network]
- [Security]
- 
thumbnail: /gallery/nmap2.jpg
---

nmap을 사용하여 타겟 서버의 포트 개방 여부를 확인하고, 어떤 프로세서가 러닝되고 있는지 확인하려고 합니다. 
그러기 위해서는 'NMAP' 이 설치가 되어 있어야 합니다. 
맥을 사용하시는 분들이라면 homebrew 를 설치하신 이후에 brew install nmap 을 하시면 설치 할 수 있습니다. 

<!--more-->

아래와 같이 인스톨 합니다.

{% asset_img install.png %}

NMAP 으로 확인하려고 하는 결과를 먼저보여 드리면 아래 그림과 같습니다.

{% asset_img nmap-F.png %}

한양대학교의 아이피 주소인 166.104.177.24 을 넣어보니 http 프로토콜의 포트번호인 80번과, https의 포트번호인 443만 개방되어 있는것을 확인하실 수 있습니다.

~~~python
import os

# options인자는 CLI의 옵션을 받기 위해. 
def get_nmap(options, ip):
    command = "nmap" + options + " " + ip
    process = os.popen(command)
    results = str(process.read())
    return results

print(get_nmap(' -F', '166.104.177.24'))
~~~

위의 CLI로 실행한 기능을 실행하는 python 코드 입니다.

이를 실행하면

{% asset_img python.png %}

위와 같이 실행됩니다.