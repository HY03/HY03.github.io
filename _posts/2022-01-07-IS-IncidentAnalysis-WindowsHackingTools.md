---
title: 정보보안 - 윈도우 해킹프로그램 분석
date: '2022-01-07 09:00:00'
tags:
- 정보보안기사
- 윈도우 침해사고
- 해킹 프로그램
related: true
categories:
- IS_Certification
toc: true
---

# 윈도우 해킹프로그램 분석
- 해킹프로그램 분석
    + 분석환경 구성
        * 최소 2개 이상의 가상서버를 구동 (물리적 서버 필요)
            - Virtual Box
            - VMWare Workstation Pro
        * 가상서버 
            - 가상네트워크를 일반네트워크와 똑같이 구축 (Samba, HTTP, FTP 등 제공)
            - 가상 서버 OS 를 리눅스로 구축
                + 악성프로그램에 취약하지 않음
            - 가상 서버 OS 를 윈도우로 구축 (피해시스템 - 해킹프로그램을 실행하는 서버)
        * 해킹프로그램이 실행될 때 시스템 파일에서 발생되는 내용과 레지스트리에서 발생하는 내용을 모니터링하는 프로그램 필요
        * 테스트 후 이전 상태(초기상태)로 복구되어야 함
            - VMWare 의 Snapshot 기능을 이용
        * 물리적 서버 (VMWare 서버)
            - DHCP
            - 외부관리프로그램
        * 리눅스 서버 (가상서버) - 서비스제공
            - Samba
            - DNS Server
            - Routing S/W
            - HTTP Server
            - SMTP Server
            - POP3 Server
            - IRC Server
            - Network Monitoring S/W
        * 윈도우 서버 (가상서버) - 피해대상
            - 악성코드 실행서버
            - 파일시스템 모니터링
            - 사용자정보 모니터링
            - 레지스트리 모니터링
            - 서비스항목 모니터링
            - 공유폴더 모니터링
            - 시스템항목 모니터링
            - 네트워크항목 모니터링
    + sysAnalyzer 도구
        * `http://sandsprite.com/iDef/SysAnalyzer`
        * System에서 악성코드가 구동되는 동안 변경된 정보 수집, 비교, 분석하는 자동화된 툴
        * 스냅샷을 비교하여 어떤 것이 바뀌었는지 비교
            - Delay
            - Sniffit Hit : IRC/HTTP 접속정보
            - API Logger : 바이너리에 Injection 되는 DLL 이 어떤 것이 호출되는지 확인
            - Directory Watcher : 모니터링 되는 시점에서 생성되는 모든 파일 확인
            - 시점별로 실행된 프로세스 확인
            - 악성코드에 의해 오픈된 포트
            - explorer.exe 로드된 DLL 파일
            - 커널에 로드된 모듈
            - 변경 / 생성된 레지스트리 키
    + Malcode Analysis Pack
        * 악성코드 조사
        * Shell Ext : 문자열 확인
        * SocketTool : TCP 클라이언트 모니터링
        * MailPot : 메일서버 캡처 프로그램
        * fakeDNS : 스푸핑 미니 DNS 서버 프로그램. 악성프로그램이 접속하는 도메인을 다른 곳으로 유도하는 프로그램
        * Sniff_Hit : HTTP, IRC, DNS 스니퍼
        * sclog : 악성코드에서 사용하는 쉘코드 분석
        * GdiProcs : 숨겨진 프로세스 탐지
        * Shellcode2Exe : 인코드된 쉘코드로 변환하는 php 스크립트 프로그램 (3:27)
    + 모니터링 프로그램 활용
        * 파일 모니터링 : `Filemon`
        * 레지스터 모니터링 : `Regmon`
        * CPU성능 모니터링 : `Cpumon`
        * 네트워크 모니터링 : `TCPView`, `TDImon`
        * 프로세스 모니터링 : `procexp`
        * 스냅샷 모니터링 : `Winanalysys`
        * API 함수 추적 : `API SPY`
        * 네트워크 트래픽 분석 : `ethereal`