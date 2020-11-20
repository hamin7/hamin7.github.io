---
title: 서버 대역과 포트 스캔하기
author: Hamin
date: 2020-05-04 23:14:11
tags: [nmap, Network, Security]
categories:
- [nmap]
- [Network]
- [Security]
thumbnail: /gallery/nmap2.jpg
---

오늘은 한양대학교 서버와 포트를 스캔해 보도록 하겠습니다.

우선 한양대의 서버 Ip를 알아봅시다.

<!--more-->

hanyang.ac.kr에 Ping을 날려봅시다

{% asset_img pingHanyang.png %}

안타깝게도 패킷이 100% loss되는 군요...

왜 ping이 안 먹히는지는 다음에 알아보도록 하겠습니다.

hanyang.ac.kr의 IP주소는 166.104.177.108이라는 군요

사실 166.104.177.108은 한양메일의 IP이고 한양대학교 홈페이지의 Ip주소는 166.104.177.24이긴 합니다.

왜 이렇게 나오는지도 다음에 알아보도록 하겠습니다.

그러면 이 두 IP주소에 nmap을 이용하여 스캔을 해보겠습니다.

{% asset_img -f_denied.png %}

어처구니 없게도 두 호스트가 다운되어 있다고 나오는군요...

-Pn으로 해보라고 하니 해보겠습니다

{% asset_img -pn108.png %}

166.104.177.108(한양 메일)의 경우 1000개의 포트를 스캔했고 모두 filtered 되어있다고 나오는군요

왜 filtered 되어있는걸까요?

하지만 몇시간 후에 해보니

{% asset_img -pn108again.png %}

이번엔 filtered가 안나오는군요...

왜그럴까요...?

{% asset_img -pn24.png %}

166.104.177.24(한양 홈페이지)의 경우 80번과 443번 포트가 열려 있다고 나오는 군요.

참고로 다시 -F를 이용하여 해본결과 아래와 같이 이전과 다르게 스캔이 되었습니다

{% asset_img -f24.png %}

심지어 166.104.177.108은 어느 포트가 무엇을 하는지까지 보여줍니다. 94개만 filtered 되었다고 나오는 군요

{% asset_img -f108.png %}

왜 이전과 다른 결과가 나오는 걸까요..?

아무튼 이제 nmap을 이용하여 원하는 호스트의 원하는 포트를 스캔하는 파이썬 코드를 작성해보겠습니다.

그 코드는 아래와 같습니다

~~~python
import optparse
import socket
import nmap

port = '21,22,23,25,42,53,70,79,80,88,110,118,143,156,161,220,443,993,8080'

def nmapScan(tgtHost, tgtPort):
        nmScan = nmap.PortScanner()
        nmScan.scan(tgtHost, tgtPort)
        state = nmScan[tgtHost]['tcp'][int(tgtPort)]['state']
        print(" [*] " + tgtHost + " tcp/" + tgtPort + " " + state)

def main():
        parser = optparse.OptionParser('usage %prog <options> \n-H <target host> : input target host name \n-p <target port> : input target port name \n-A <all port> : target is all port')
        parser.add_option('-H', dest='tgtHost', type='string', help='specify target host')
        parser.add_option('-p', dest='tgtPort', type='string', help='specify target port[s] separated by comma')
        parser.add_option('-A', dest='allPort', action='store_true', help='target port is all')
        (options, args) = parser.parse_args()
        tgtHost = options.tgtHost
        tgtPorts = str(options.tgtPort).split(',')
        allPort = options.allPort

        if ((tgtPorts[0] == 'None') & (allPort == None)) | (tgtHost == None) :
                print(parser.usage)
                exit(0)

        if (allPort == True):
                tgtPorts = str(port).split(',')
                for tgtPort in tgtPorts:
                        nmapScan(tgtHost, tgtPort)

        else:
                for tgtPort in tgtPorts:
                        nmapScan(tgtHost, tgtPort)


if __name__ == '__main__':
        main()   
~~~

그리고 이 코드를 실행 시켜보면

166.104.177.24(한양대 홈페이지)의 경우 아래와 같고
~~~json
(py36)  ~/Documents/GitHub/Today_I_Learned/Network   master ●  python portScan11.py -H 166.104.177.24 -A 
 [*] 166.104.177.24 tcp/21 filtered
 [*] 166.104.177.24 tcp/22 filtered
 [*] 166.104.177.24 tcp/23 filtered
 [*] 166.104.177.24 tcp/25 filtered
 [*] 166.104.177.24 tcp/42 filtered
 [*] 166.104.177.24 tcp/53 filtered
 [*] 166.104.177.24 tcp/70 filtered
 [*] 166.104.177.24 tcp/79 filtered
 [*] 166.104.177.24 tcp/80 open
 [*] 166.104.177.24 tcp/88 filtered
 [*] 166.104.177.24 tcp/110 filtered
 [*] 166.104.177.24 tcp/118 filtered
 [*] 166.104.177.24 tcp/143 filtered
 [*] 166.104.177.24 tcp/156 filtered
 [*] 166.104.177.24 tcp/161 filtered
 [*] 166.104.177.24 tcp/220 filtered
 [*] 166.104.177.24 tcp/443 open
 [*] 166.104.177.24 tcp/993 filtered
 [*] 166.104.177.24 tcp/8080 filtered
~~~

166.104.177.108(한양 메일)의 경우 아래와 같습니다

~~~json
(py36)  ~/Documents/GitHub/Today_I_Learned/Network   master ●  python portScan11.py -H 166.104.177.108 -A
 [*] 166.104.177.108 tcp/21 filtered
 [*] 166.104.177.108 tcp/22 filtered
 [*] 166.104.177.108 tcp/23 filtered
 [*] 166.104.177.108 tcp/25 open
 [*] 166.104.177.108 tcp/42 filtered
 [*] 166.104.177.108 tcp/53 filtered
 [*] 166.104.177.108 tcp/70 filtered
 [*] 166.104.177.108 tcp/79 filtered
 [*] 166.104.177.108 tcp/80 open
 [*] 166.104.177.108 tcp/88 filtered
 [*] 166.104.177.108 tcp/110 open
 [*] 166.104.177.108 tcp/118 filtered
 [*] 166.104.177.108 tcp/143 open
 [*] 166.104.177.108 tcp/156 filtered
 [*] 166.104.177.108 tcp/161 filtered
 [*] 166.104.177.108 tcp/220 filtered
 [*] 166.104.177.108 tcp/443 open
 [*] 166.104.177.108 tcp/993 open
 [*] 166.104.177.108 tcp/8080 filtered
 ~~~

 두 곳 모두 터미널에서 nmap을 사용했을 때와 같은 결과가 나오는군요!

 그렇다면 이번에는 다른 웹 서버가 있는지 알아보겠습니다.

 ~~~python
 # 서버 스캔
import optparse
from socket import *
from threading import *
import ipaddress
import time

cnt = 0

# port-scan function, takes arguments tgtHost, tgtPorts 
def portScan(tgtHost, tgtPorts):
    global cnt
    try:
        tgtHost = str(tgtHost)
        # tries to get target IP address
        tgtIP = gethostbyname(tgtHost)
    except:
        # if unsuccesful, prints out following result
        print ('[-] cannot resolve ' + tgtHost + ': unknown host')
        return

    try:
        # tries to get target address
        tgtName = gethostbyaddr(tgtIP)
        cnt += 1
        print ('\n[+] scan results for: ' + tgtIP + " Domain name is : " + tgtName[0])
        
    except:
        return

def main():
       parser = optparse.OptionParser('usage %prog -t <target-host> -p <target-port(s)>')
       parser.add_option('-t', dest='tgtHost', type='string', help='specify target host')
       parser.add_option('-p', dest='tgtPort', type='string', help='specify target port(s), seperated by a comma, seperate ranges with a -')
       (options, args) = parser.parse_args()

       if (options.tgtHost == None) | (options.tgtPort == None):
           print (parser.usage)
           exit(0)
       else:
            tgtHost = options.tgtHost
            if tgtHost.endswith('.0'):
                hosts = ipaddress.ip_network(tgtHost+'/24')
            else:
                hosts = [tgtHost]

            if '-' in str(options.tgtPort):
                tgtPorts = options.tgtPort.split('-')
                tgtPorts = range(int(tgtPorts[0]),int(tgtPorts[1]))
            else:
                tgtPorts = str(options.tgtPort).split(',')
            
            for tgtHost in hosts:
                # print(tgtHost)
                portScan(tgtHost, tgtPorts)
            
            print('\n Total number of web servers: ' + str(cnt))
            print('\n Scan duration : ', time.time() - start, 'sec')

if __name__ == '__main__':
    start = time.time() # 시작 시간 설정
    main()
~~~

위와 같이 nmap을 사용하여 측정하였습니다.

80포트와 8080포트를 기준으로 측정하여보면 결과는 아래와 같습니다.

우선 80포트를 기준으로 스캔하면

 {% asset_img scanAll80.png %}

~~~json
(py36)  ~/Documents/GitHub/Today_I_Learned/Network   master ●  python portScan13.py -t 166.104.177.0 -p 80
[+] scan results for: 166.104.177.24 Domain name is : www.hanyang.ac.kr

[+] scan results for: 166.104.177.62 Domain name is : nmail.hanyang.ac.kr

[+] scan results for: 166.104.177.103 Domain name is : antispam1.hanyang.ac.kr

[+] scan results for: 166.104.177.104 Domain name is : antispam2.hanyang.ac.kr

[+] scan results for: 166.104.177.105 Domain name is : mail.hanyang.ac.kr

[+] scan results for: 166.104.177.106 Domain name is : mail.hanyang.ac.kr

[+] scan results for: 166.104.177.108 Domain name is : hanyang.ac.kr

[+] scan results for: 166.104.177.109 Domain name is : antispam.hanyang.ac.kr

[+] scan results for: 166.104.177.170 Domain name is : portal.hanyang.ac.kr

[+] scan results for: 166.104.177.200 Domain name is : nf.hanyang.ac.kr

 Total number of web servers: 10

 Scan duration :  0.28237009048461914 sec
~~~

8080포트를 스캔하면

{% asset_img scanAll8080.png %}

~~~json
(py36)  ~/Documents/GitHub/Today_I_Learned/Network   master ●  python portScan13.py -t 166.104.177.0 -p 8080

[+] scan results for: 166.104.177.24 Domain name is : www.hanyang.ac.kr

[+] scan results for: 166.104.177.62 Domain name is : nmail.hanyang.ac.kr

[+] scan results for: 166.104.177.103 Domain name is : antispam1.hanyang.ac.kr

[+] scan results for: 166.104.177.104 Domain name is : antispam2.hanyang.ac.kr

[+] scan results for: 166.104.177.105 Domain name is : mail.hanyang.ac.kr

[+] scan results for: 166.104.177.106 Domain name is : mail.hanyang.ac.kr

[+] scan results for: 166.104.177.108 Domain name is : hanyang.ac.kr

[+] scan results for: 166.104.177.109 Domain name is : antispam.hanyang.ac.kr

[+] scan results for: 166.104.177.170 Domain name is : portal.hanyang.ac.kr

[+] scan results for: 166.104.177.200 Domain name is : nf.hanyang.ac.kr

 Total number of web servers: 10

 Scan duration :  0.15810728073120117 sec
 ~~~

 80포트와 8080포트 모두 스캔하면

 {% asset_img scanAll80and8080.png %}
 ~~~json
 (py36)  ~/Documents/GitHub/Today_I_Learned/Network   master ●  python portScan13.py -t 166.104.177.0 -p 80,8080

[+] scan results for: 166.104.177.24 Domain name is : www.hanyang.ac.kr

[+] scan results for: 166.104.177.62 Domain name is : nmail.hanyang.ac.kr

[+] scan results for: 166.104.177.103 Domain name is : antispam1.hanyang.ac.kr

[+] scan results for: 166.104.177.104 Domain name is : antispam2.hanyang.ac.kr

[+] scan results for: 166.104.177.105 Domain name is : mail.hanyang.ac.kr

[+] scan results for: 166.104.177.106 Domain name is : mail.hanyang.ac.kr

[+] scan results for: 166.104.177.108 Domain name is : hanyang.ac.kr

[+] scan results for: 166.104.177.109 Domain name is : antispam.hanyang.ac.kr

[+] scan results for: 166.104.177.170 Domain name is : portal.hanyang.ac.kr

[+] scan results for: 166.104.177.200 Domain name is : nf.hanyang.ac.kr

 Total number of web servers: 10

 Scan duration :  0.16145086288452148 sec
 ~~~

 이러한 결과들이 나옵니다.

 이제 스레드를 사용하여 해보겠습니다.

~~~python
import optparse
from threading import *
from socket import *
import ipaddress
import time

screenLock = Semaphore(value=1)
cnt = 0

def printResult():
    print('\n Total number of web servers: ' + str(cnt))
    print('\n Scan duration : ', time.time() - start, 'sec')


def connScan(tgtHost, tgtPort):
    try:
        connSkt = socket(AF_INET, SOCK_STREAM)
        connSkt.connect((tgtHost, tgtPort))
        # connect 까진 됨
        #connSkt.send('Hi Hanyang\r\n')
        # send부터 안됨.
        #results = connSkt.recv(100)
        # print('[+] %d/tcp open' % tgtPort)
        result = str('[+] %d/tcp open' % tgtPort)
        return result
    except:
        result = str('[-] %d/tcp closed' % tgtPort)
        return result
    finally:
        connSkt.close()

def portScan(tgtHost, tgtPorts):
    global cnt
    try:
        tgtHost = str(tgtHost)
        tgtIP = gethostbyname(tgtHost)
    except:
        print("[-] Cannot resolve '%s' : Unknown host" %tgtHost)
        return
    
    try:    
        tgtName = gethostbyaddr(tgtIP)
        cnt += 1
        screenLock.acquire()
        print('\n[+] Scan Results for: ' + tgtIP + " Domain name is : " + tgtName[0])
        # for tgtPort in tgtPorts:
        #     result = connScan(tgtHost, int(tgtPort))
        #     print(result)
    except:
        return
    finally:
        screenLock.release()
    
def main():
    parser = optparse.OptionParser('usage %prog -H <target host> -p <target port>')
    parser.add_option('-H', dest = 'tgtHost', type = 'string', help = 'specify target host')
    parser.add_option('-p', dest = 'tgtPort', type = 'string', help = 'specify target port[s] separated by comma')

    (options, args) = parser.parse_args()

    if (options.tgtHost == None) | (options.tgtPort == None):
        print (parser.usage)
        exit(0)
    else:
        tgtHost = options.tgtHost
        if tgtHost.endswith('.0'):
            hosts = ipaddress.ip_network(tgtHost+'/24')
        else:
            hosts = [tgtHost]

        if '-' in str(options.tgtPort):
            tgtPorts = options.tgtPort.split('-')
            tgtPorts = range(int(tgtPorts[0]),int(tgtPorts[1]))
        else:
            tgtPorts = str(options.tgtPort).split(',')

        for tgtHost in hosts:
            setdefaulttimeout(1)
            # 스레드 생성
            t = Thread(target=portScan, args=(tgtHost, tgtPorts))
            t.start()
        
        t.join()    # 스레드가 종료될 때까지 기다림
        printResult()
        
if __name__ == '__main__':
    start = time.time() # 시작 시간 설정
    main()
~~~

이를 실행하여 166.104.177.0/24의 모든 80포트를 스캔하면 아래와 같이 나옵니다.

{% asset_img threadOne.png %}

또한 이를 실행하여 166.104.177.0/24의 모든 80과 8080포트를 스캔하면 아래와 같이 나옵니다.

{% asset_img threadTwo.png %}