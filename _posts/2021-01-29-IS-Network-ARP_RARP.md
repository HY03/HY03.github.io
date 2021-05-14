---
title: 정보보안 - 네트워크 ARP / RARP
date: '2021-01-29 12:00:00'
tags:
- 정보보안기사
- 네트워크
related: true
categories:
- IS_Certification
toc: true
---

- ARP / RARP 프로토콜
    + ARP
        * IP (논리주소) -> MAC (물리주소) 로 변환
        * 과정
            - 질의자가 IP 를 브로드캐스트 하여 MAC 주소를 질의
            - 해당하는 IP를 가진 응답자가 자신의 MAC 주소를 유니캐스트
            - 해당하지 않는 Host 는 해당 프레임 폐기
        * 각 Host 는 ARP Cache 를 가지고 있음
            - 일시적으로 보관
        * ARP Cache 테이블
            - 각각의 MAC/IP 정보를 일시적으로 보관
            - IP에 대한 MAC 질의 결과를 보관
            - 윈도우
                + `arp -a` 로 확인
                + Type
                    * dynamic : 동적 생성 (1~2분 유지)
                    * static : 관리자 할당 (재부팅시 삭제)
                + `arp -d [ip]` 로 삭제
                    * incomplete : 삭제된 직후 MAC 주소란에 표기됨
                + `arp -s [ip] [mac]` 로 static 할당
            - 리눅스
                + `arp -a` 로 확인
                    * PERM : 관리자 할당 (static)
                + `arp -d [ip]` 로 삭제
                + `arp -s [ip] [mac]` 로 static 할당
    + RARP
        * MAC (물리주소) -> IP (논리주소) 로 변환
        * 과정
            - Host 는 본인의 MAC 주소만 알고 IP를 모름 (하드디스크 없음)
            - 질의자가 MAC 주소를 브로드캐스트 하여 IP를 질의
            - MAC / IP 정보를 가진 RARP 서버가 IP 주소를 유니캐스트
            - 해당하지 않는 Host 는 해당 프레임 폐기

- ARP Spoofing 공격 = ARP Cache poisoning
    + 과정 (MAC 주소 변조, 트래픽 스니핑, 2계층 공격)
        * 2계층은 서로 다른 네트워크끼리 라우팅하지 않음
            - 공격 대상은 같은 네트워크 대역
            - 라우터 바깥으로 나갈 때는 물리적 주소가 아닌 IP 주소로 사용
            - Router
                + 라우터의 논리적인 주소를 r.ip, 물리적 주소를 r.mac 으로 가정
                + Alice : a.ip, a.mac, a.cache
                    * H : b.ip h.mac (공격결과)
                    * H : h.ip h.mac (정상결과)
                + Bob : b.ip, b.mac, b.cache
                    * H : a.ip h.mac (공격결과)
                    * H : h.ip h.mac (정상결과)
                + Hacker : h.ip, h.mac, h.cache
                    * A : a.ip a.mac (정상결과)
                    * B : b.ip b.mac (정상결과)
                + Hacker는 가운데에서 스니핑하며 정상 중계
        * Hacker, Alice (Victim), Bob (Victim), Router
        * Hacker는 Alice 와 Bob 의 MAC 주소를 공격자의 MAC 주소로 위조한 Reply 를 만들어서 지속적으로 전송
        * Alice 와 Bob 의 Cache Table 에는 Hacker 의 MAC 주소가 유지됨
        * Alice 와 Bob 간의 통신이 Hacker 에게 넘어감 (스니핑)
        * Hacker 는 Alice 와 Bob 이 스니핑을 눈치채지 못하도록 IP 포워딩 (전송기능 활성화)
            - Arp Cache 가 저장된 Router 주소를 자신의 주소로 변조시킴
            - 외부로 나가는 데이터를 전부 스니핑
            - Arp Redirect 공격
    + ARP 의 취약점을 완전히 없애는 방법은 없음. 정적인 설정으로 캐시정보 갱신 X
        * 시스템이 종료되면 전부 삭제
        * 재가동 될 때마다 ARP 캐시를 정적으로 구성
        * ARP Watch : ARP 트래픽을 실시간으로 모니터링
    + GARP (Gratuitous - 불필요한)
        * Sender IP 와 Target IP 를 동일하게 함
        * 목적
            - IP 주소 충돌 감지
                + 자신 이외의 Reply 를 받아서는 안됨
                + Reply 가 있을 경우 자신과 같은 IP를 가진 Host 가 존재함을 인지
            - ARP Table 갱신
                + MAC 주소 변경 시 자신의 MAC 주소가 바뀌었음을 알려줌
                + ARP 문제점 : 인증을 하지 않고 수정함 (취약점 존재)