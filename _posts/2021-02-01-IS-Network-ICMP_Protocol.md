---
title: 정보보안 - 네트워크 ICMP 프로토콜
date: '2021-02-01 16:00:00'
tags:
- 정보보안기사
- 네트워크
related: true
categories:
- IS_Certification
toc: true
---

- ICMP 프로토콜
    + 개요
        * 3계층 프로토콜
            - IP : 신뢰할 수 없는 프로토콜
            - ICMP : 에러발생 원인, 네트워크 상태 등 리포트
        * 기능
            - Error Message 기능
            - Query Message (네트워크 상태 조회)
        * Type 필드 (Meesage 종류)
        * Code 필드 (Type 에 속하는 세부 내용)
    + 헤더 구조
        * Type : 8bit : ICMP 메시지 유형/용도
            - Type 3 : Destination Unreachable
        * Code : 8bit : ICMP 메시지의 세부 내용
            - Code 3 : Port Unreachable (상대방 UDP 포트가 열려있지 않음 등)
        * checksum : 16bit : ICMP 메시지 자체 오류에 대한 검사를 위함
        * Rest of header : 타입, 코드에 따라 추가되는 헤더가 있을 경우
        * Data Section : 데이터 영역
    + 메시지 유형
        * Error Message
            - Type 3 : Destination Unreachable
                + Code 1 (Host Unreachable) : 목적지 호스트로 패킷 전송 실패
                + Code 2 (Protocol Unreachable) : 목적지에서 특정 프로토콜을 사용할 수 없을 때
                + Code 3 (Port Unreachable) : UDP 포트가 열려있지 않을 때
                + Code 4 (Fragmentation needed) : DF Flag bit 이 1로 설정되어 있을 때 단편화를 해야만 하는 경우 리턴 메시지
            - Type 5 : Redirection : 라우팅 경로가 잘못되어 새 경로로 보내야 할 경우
                + ICMP Redirect 공격 시 이용할 수 있는 Msg
            - Type 11 : Time exceeded : 시간초과
                + 시간 초과 시 패킷 전부 폐기
                + Code 0 : TTL = 0
                + Code 1 : Fragment Reassembly time exceeded
        * Query Message
            - Type 8 : Echo (ping) Request
                + Identifier : 식별자
                + Sequence number : 메시지 순번
            - Type 0 : Echo (ping) Reply
                + Identifier : 식별자
                + Sequence number : 메시지 순번
                + Request 와 쌍이 맞아야 함

- ICMP Redirect 공격
    + 개요
        * ICMP Redirection Message (Type5)를 이용
        * 패킷의 경로를 재설정
        * 해당 메시지를 수신한 호스트는 자신의 라우팅 테이블을 변경
        * 이러한 특성을 이용하여 ICMP Redirect 메시지를 공격자가 만듦
        * 패킷이 자신에게 오도록 유도하여 스니핑을 함
    + ARP Redirect 공격과의 비교
        * ARP Cache Table을 변경, 스니핑
    + 예시
        * 공격자 IP : 192.168.120.110
        * 수신자 
            - IP : 192.168.120.100
            - Default Gateway : 192.168.120.1
        * ICMP Redirection Message : 라우팅 경로가 잘못 되었을 때 Host 에게 알려주는 메시지
        * IP Packet을 Router / Gateway 가 전달 받고 경로가 잘못되었을 때 ICMP Redirection Message 를 송신
        * 공격자는 Src IP를 게이트웨이 IP (192.168.120.1) 로 위조하고 수신자 (192.168.120.100) 에게 ICMP Redirection 메시지를 전송
            - 메시지의 목적지 주소 (예: 10.10.10.10) 의 Gateway 주소를 자신의 주소 (192.168.120.110) 으로 설정하여 전송
        * 수신자는 공격을 받은 이후 Routing table 에 잘못된 정보를 생성
            - Dest: 10.10.10.10
            - Netmask : 255.255.255.255
            - Gateway : 192.168.120.110
            - Interface : 192.168.120.100
    + 대처방법
        * ICMP Redirect (Type 5) 메시지 자체에 취약점을 가지고 있음
        * ICMP Redirect Message 에 라우팅 테이블이 변경되지 않도록 설정 (옵션해제)
            - 리눅스
                + `sysctl` (커널 매개변수 변경 등) (런타임 적용)
                + vi 편집기로 `/etc/sysctl.conf` 변경 후 `sysctl -p` 로 적용
                + `#echo "net.ipv4.conf.[all|eth0|lo].accept_redirects=0" >> /etc/sysctl.conf`
                    * 송신 차단 : `send_redirects`
        * sysctl
            - 커널의 매개변수 변경 등에 사용
            - `sysctl [옵션] [커널매개변수]`
                + `-a` : 모든 변수 표시
                + `-w` : write -> 변수값 설정
                + `-p` : 수정된 파일의 적용
            - 예 : ping 신호 차단
                + `sysctl -w net.ipv4.icmp_echo_ignore_all=1`
                + vi 편집기로 `/etc/sysctl.conf` 의 `net.ipv4.icmp_echo_ignore_all=1` 설정
                + `#echo "net.ipv4.icmp_echo_ignore_all=1" >> /etc/sysctl.conf`