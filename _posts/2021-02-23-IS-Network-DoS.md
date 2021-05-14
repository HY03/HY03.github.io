---
title: 정보보안 - 네트워크 DoS
date: '2021-02-23 17:00:00'
tags:
- 정보보안기사
- 네트워크
related: true
categories:
- IS_Certification
toc: true
---

# DoS (Denial of Service) 공격
- 개요
    + 대상 시스템이 정상적인 서비스를 할 수 없도록 만드는 공격 (서비스 거부 공격)
    + 가용성(Availability)을 떨어뜨리는 공격
    + 공격 유형
        * 파괴공격 : 디스크, Data, System
        * 시스템 자원 소진 공격 : CPU, 메모리, 디스크
        * 네트워크 자원 소진 공격 : 네트워크 대역폭 (BandWidth)
    + DoS 와 DDoS
        * DoS : 단일 컴퓨터를 통해 공격
        * DDoS : 다수의 컴퓨터를 통해 공격 (Distributed)
    + 종류
        * Ping Of Death Attack
            - 개요
                + IP 패킷은 단편화 (Fragment) 를 수행
                + MTU 1500 bytes (Ethernet)
                + 단편화 후 전송, 수신측은 Reassembly 진행
                + Ping : ICMP 프로토콜
            - 원리 (이더넷 기준)
                + ICMP 패킷(ping) 을 아주 크게 만듦
                + MTU => fragment 발생
                + IP 헤더 20bytes 제외 1480 bytes
                + 수신 측 재조합 과정에서 재조합 버퍼의 overflow 발생
            - 과정
                + Attacker : ping 패킷 전송
                    * 용량이 매우 큰 ICMP 패킷
                    * 단편화 발생
                + Target : 대량의 IP fragment 수신
                    * 재조합 buffer 가 과부하 발생
            - 대응책
                + 일반적으로 ICMP 패킷은 분할이 될 정도로 크지 않음
                + 분할이 일어난 패킷 탐지, 공격 의심
                + 반복적으로 들어오는 경우 ICMP 패킷 무시하도록 설정
            - 예시
                + Ping of Death 공격
                    * Fragmented proto=ICMP offset=0 ID=1
                    * Fragmented proto=ICMP offset=1480 ID=1
                    * Fragmented proto=ICMP offset=2960 ID=1
                    * Fragmented proto=ICMP offset=4440 ID=1
        * Land Attack
            - 개요
                + SRC IP 와 DST IP 를 동일하게 해서 패킷 전송
                + 수신자는 자신에게 응답을 보내게 되어 시스템 가용성 침해
                + 방화벽이나 시스템에서 출발지와 목적지가 같으면 패킷 폐기
                + 이론상으로만 존재 (현재는 차단)
            - 과정
                + Attacker (192.168.150.1)
                    * SRC IP : ~~192.168.150.1~~ 192.168.100.10(위조)
                    * DST IP : 192.168.100.10
                + Target (192.168.100.10)
            - 대응책
                + 방화벽에서 Drop
                + 동일 SRC IP, DST IP 는 Drop
            - 예시
                + ICMP 패킷 => Echo Request
        * Smurf Attack
            - 개요
                + Broadcast
                    * 해당 라우터 아래 내부 네트워크 호스트에게 한꺼번에 패킷을 보냄
                    * 목적지 주소 : 네트워크 주소를 제외한 Client IP의 주소를 255로 설정 (다이렉트 브로드캐스트)
                    * 라우터 범위 밖을 넘어가지 않음
                + 공격자가 다이렉트 브로드캐스트를 이용해 공격
                    * 증폭 네트워크의 라우터의 브로드캐스트 기능이 활성화되어 있어야 함
            - 원리
                + 공격자가 SRC IP를 희생자 IP 롤 위조
                + ICMP Echo Request 를 브로드캐스팅으로 전송
                + 라우터 아래 내부 네트워크 호스트들이 Echo Reply 를 희생자 IP로 전송
                    * 증폭 네트워크 (Bounce Network)
            - 과정
                + Attacker (192.168.100.200)
                + Target (192.168.100.10)
                + Amplifier(Bounce) Network (192.168.200.255) : 여러 호스트들
                + Attacker : ICMP Echo Request 전송 (ping)
                    * SRC IP : 192.168.100.10 (Victim)
                    * DST IP : 192.168.200.255
                + Amplifier(Bounce) Network : ICMP Echo Reply 전송 (ping)
                    * DST IP : 192.168.100.10 (Victim)
            - 대응책
                + 단시간에 다량의 ICMP Echo Reply 패킷이 생성이 되면 침입차단시스템을 통해서 Drop을 시킴
                + Bounce(Amplifier) Network 차단
                    * 라우터 : 다른 네트워크로부터 자신의 네트워크로 들어오는 Direct Broadcast 패킷 차단
                        - 라우터 명령어 : `(config-if)#no ip directed-broadcast`
                + ICMP Echo Request 에 대해 응답하지 않도록 설정
        * Teardrop Attack
            - 개요
                + fragment offset 정보를 이용한 DoS
                + IP 패킷의 reassembly 과정에서의 문제를 이용
                + fragment offset 값이 중첩되도록 조작
                + 공격종류 : Bonk, Boink
        * IP Fragmentation  취약점을 이용한 침입차단시스템 우회공격
            -  Tiny Fragment (DoS 아닌 우회공격)
                +  원래 Fragment 보다 작게 IP Header 를 조작, 공격
            -  Fragment Overlap (DoS)
                +  Offset 값 조작
                +  서비스 포트의 필드를 중첩시켜 방화벽이 허용하지 않는 서비스에 접근하는 공격기법