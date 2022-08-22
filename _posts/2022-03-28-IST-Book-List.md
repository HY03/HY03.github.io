---
title: 정보보안기사 실기 - 00.시험대비 공부할 목차 정리
date: '2022-03-17 12:00:00'
tags:
- 정보보안기사 실기
- 알기사 정보보안기사 실기
related: true
categories:
- IS_Certification
toc: true
---

# -이론편-

# PART 01 시스템

## SECTION 01 시스템 기본 학습
01. 윈도우 기본 학습
- 루트 키 (registry)
02. UNIX/Linux 기본 학습
03. UNIX/Linux 시스템 관리
04. UNIX/Linux 서버 보안
05. 시스템 해킹
- 교착상태
- RAID
- Run Level
- utmp
- wtmp
- btmp
- crontab
- at

## SECTION 02 UNIX/Linux 서버 취약점
01. 계정 관리
- 패스워드 파일
02. 파일 및 디렉터리 관리
- umask
- chmod
- setuid
- setgid
- sticky bit
- chown

## SECTION 03 윈도우 서버 취약점
01. 계정 관리
- winlogon
- lsa
- sam
- srm
02. 서비스 관리
- lsass
- net share
03. 로그 관리
- 이벤트 로그
- DDE 취약점

# PART 02 네트워크

## SECTION 04 네트워크 기본 학습
01. 프로토콜(Protocol)
02. OSI 7Layer
- OSI 7Layer
03. OSI모델 데이터 교환 방식
04. TCP/IP 프로토콜
05. ARP/RARP 프로토콜(TCP/IP 인터넷 계층)
- ARP Spoofing
06. IP(IPv4) 프로토콜
- MTU
- Subnet Mask
- IP Spoofing
07. ICMP 프로토콜
- ICMP 메시지 종류
08. TCP 프로토콜
09. UDP 프로토콜
10. 네트워크 관리 명령어
11. 포트 스캐닝(Port Scanning)
- NMAP Port Scan
    + Stealth Scan
        * TCP FIN Scan
        * TCP Null Scan
        * TCP X-MAS Tree Scan
    + TCP Half Open Scan
        * TCP SYN Scan
    + UDP Scan
- TCP Fragmentation
- 스니핑?
12. 서비스 거부(DoS:Denial of Service) 공격
13. 분산 서비스 거부(DDoS:Distributed Denial of Service) 공격
- TCP SYN Flooding
- ICMP Flooding / Smurfing Attack
- Tear Drop
    + Tiny Fragment
    + Fragment Overlap
    + IP Fragmentation
        * Ping of Death
        * Tear Drop
- Land Attack
- HTTP GET Flooding
- Slow HTTP Get/Post Attack
- Slow HTTP Read DoS
- Slow HTTP Header DoS(Slowloris)
- Hash DoS
- Hulk DoS
14. DRDoS(Distributed Reflection DoS)
15. 무선랜(Wireless LAN) 보안
- WEP / WPA / WPA2
- 네트워크 토폴로지 (트리형, 버스형, 성형, 링형, 망형)


## SECTION 05 네트워크 보안 프로토콜
01. 가상 사설망(VPN:Virtual Private Network)
- 가상사설망
- SSL VPN
- IPSEC VPN
- PPTP VPN
- L2TP VPN
- L2F
- 망분리
02. IP 보안 - IPsec(IP Security)
- IPSEC Header
- IPSEC 키관리
03. 전송 계층 보안 - SSL/TLS
- SET
- 이중서명
- SSL Handshake
- SSL Header
- OTP

## SECTION 06 라우터 보안
01. 라우터 자체 보안
02. 라우터와 접근제어(ACL)
03. 라우터를 통한 네트워크 보안
- OSPF
- RIP

# PART 03 어플리케이션

## SECTION 07 어플리케이션 기본 학습
01. DNS(Domain Name System)
- DNS Query
- DNS 증폭공격
- DNSSEC
- DNS캐시포이즈닝
02. HTTP(Hyper Text Transfer Protocol)
- Request Header
- Response Header
03. FTP(File Transfer Protocol)
- FTP 전송모드(Active/Passive)
- FTP / tFTP / sFTP
- ftp 접근 통제 파일
- FTP 취약점
- FTP 로그기록
04. SNMP(Simple Network Management Protocol)
05. DHCP(Dynamic Host Configuration Protocol)

## SECTION 08 웹 어플리케이션 취약점
01. SQL Injection 취약점
02. 크로스 사이트 스크립트(XSS:Cross Site Script) 취약점
03. 크로스 사이트 요청 위조(CSRF:Cross Site Request Forgery) 취약점
04. 서버 사이트 요청 위조(SSRF:Server Side Request Forgery) 취약점
05. 운영체제 명령 실행(Command Execution) 취약점
06. 파일 업로드(File Upload) 취약점
07. 파일 다운로드(File Download) 취약점
08. 경로 추적(Path Traversal) 취약점
09. 파일 삽입(File Inclusion) 취약점
10. URL/파라미터 변조 취약점
11. 불충분한 세션 관리 취약점
- Session Hijacking
12. 정보누출 취약점
13. 기타 웹 어플리케이션 취약점
14. 개발 보안 관리

## SECTION 09 웹 서버 취약점
01. 디렉터리 리스팅(Directory Listing) 취약점
02. 웹 서비스 메소드 설정 취약점
03. 관리자 페이지 노출 취약점
04. 위치공개 취약점
05. 검색엔진 정보 노출 취약점
06. 기타 웹서버 보안 대책(아파치 기준)
07. 웹 로그 분석
08. 보안서버(Security Server) 구축
- APT 공격
- httpd.conf
- FollowSymLinks
- ServerSignature
- Server Tokens
- 접근제어 (httpd.conf)
- 세션관리

## SECTION 10 이메일(E-Mail) 보안
01. 이메일 시스템 구조
02. SMTP 메일 형식
- POP3, IMAP
- PGP / PEM / S-MIME
03. 메일서버(sendmail) 보안 설정
04. 이메일 인증 기술(스팸 메일 방지 기술)
- RBL
- SPF
- Spam Assassin

## SECTION 11 데이터베이스 보안
01. 데이터베이스 보안 위협과 통제
- 집합성
- 추론
02. DBMS 보안 통제
03. 데이터베이스 암호화 기술
- DB 암호화 솔루션
- DB 감사 솔루션
- DB 백업 (전체, 차등, 증분)
04. 데이터베이스(MySQL) 취약점 점검

## SECTION 12 클라우드 컴퓨팅 보안
01. 클라우드 컴퓨팅 개요 및 특징
02. 클라우드 컴퓨팅 분류
03. 클라우드 기반 보안 서비스 : SecaaS

# PART 04 침해사고 분석 및 대응

## SECTION 13 보안장비 운영
01. 침입탐지 시스템(snort)
02. 침입차단 시스템(iptables)
- iptables
03. 보안 솔루션 종류 및 특징
- 침입차단 시스템
    + 패킷 필터링
    + 어플리케이션 게이트웨이
    + 회선 게이트웨이
    + 상태 기반 패킷 검사
- 침입차단 시스템 구축 유형
    + 스크리닝 라우터
    + 베스천 호스트
    + 듀얼 홈드 호스트
    + 스크린드 호스트
    + 스크린드 서브넷
- 침입탐지 시스템
    + 오용탐지
    + 이상탐지
    + NIDS / HIDS
    + 지식기반 탐지 / 행위기반 탐지
- 침입대응 시스템
- UTM
- 허니팟
- NAC
- ESM
- SIEM
- SSO
- DRM
- 워터마킹
- 디지털 포렌식
04. 네트워크 보안장비 운영
05. 보안 솔루션(장비) 취약점

## SECTION 14 시스템 점검 도구
01. 취약점(Vulnerability) 점검 도구(nessus/nikto)
02. 무결성(Integrity) 점검 도구(tripwire)
03. 루트킷(Rootkit) 점검 도구(chkrookit)
- tripwire

## SECTION 15 침해사고 유형별 시나리오
01. 리버스 쉘(Reverse Shell) 침해 사고 시나리오
02. 루트킷(Rootkit) 침해 사고 시나리오
03. DBD(Drive By Download) 침해 사고 시나리오
04. 악성코드 행위 분석 시나리오
05. 워터링 홀(Watering Hole) 침해 사고 시나리오

## SECTION 16 주요 취약점(Vulnerability)
01. GNU Bash 취약점(ShellShock)
- ShellShock
02. SSL/TLS 관련 취약점
- OpenSSL Heartbleed
03. NTP 분산 서비스 거부(DDoS) 취약점
04. SSDP 분산 서비스 거부(DDos) 취약점

# PART 05 정보보안 일반/관리

## SECTION 17 정보보안 일반/관리
01. 정보보호개요
- 예방 통제, 탐지 통제, 교정 통제
- 생체인증 (보편성, 유일성, 지속성, 성능, 수용성, 저항성)
- 논리폭탄
- 커버로스 인증
02. 암호학
- 대칭키 암호화
- 공개키 암호화
- 전자서명
- PKI
- 스트림 암호화 / 블록 암호화
- 암호분석 방법
- Diffie-Hellman 키 교환 알고리즘
- RSA
- Hash 함수
03. 접근통제
- MAC / DAC / RBAC
- CL / ACL
- Bell-Lapadula / Biba / Clark and Wilson / Chinese Wall
04. 정보보호관리
05. 위험관리(Risk Management)
- 위험관리 체계
- 접근방식에 따른 위험분석 기법
- 정량적 위험분석 / 정성적 위험분석
- 위험에 따른 손실액 분석

# PART 06 정보보안 법규

## SECTION 18 정보보안 법규
01. 법규관련 개요
02. 개인정보보호법(시행:2020.08.05.)
03. 개인정보의 안전성 확보조치 기준(시행:2021.09.15.)
04. 개인정보의 기술적·관리적 보호조치 기준(시행:2021.09.15.)
05. 정보통신망 이용촉진 및 정보보호 등에 관한 법률(시행:2021.12.09.)
06. 정보통신기반보호법(시행:2020.12.10.)
07. 전자서명법(시행:2021.06.10.)
08. 위치정보의 보호 및 이용 등에 관한 법률(시행:2021.02.05.)
- ISMS-P
- ISMS 의무인증 대상
- ISMS-P 관리체계 인증심사
- 정보보호 거버넌스 체계 수립
- 정보보호 위험 평가 (자산/위험/위협/취약점)
- CC 인증
- 개인정보 영향평가 제도
- 개인정보보호법
- 정보통신기반 보호법

# -문제편-

## Section 01 시스템 보안
01. 시스템 보안 단답형
02. 시스템 보안 서술형
03. 시스템 보안 실무형

## Section 02 네트워크 보안
01. 네트워크 보안 단답형
02. 네트워크 보안 서술형
03. 네트워크 보안 실무형

## Section 03 어플리케이션 보안
01. 어플리케이션 보안 단답형
02. 어플리케이션 보안 서술형
03. 어플리케이션 보안 실무형

## Section 04 침해사고 분석 및 대응
01. 침해사고 분석 및 대응 단답형
02. 침해사고 분석 및 대응 서술형
03. 침해사고 분석 및 대응 실무형

## Section 05 정보보안 일반
01. 정보보안 일반 단답형
02. 정보보안 일반 서술형
03. 정보보안 일반 실무형

## Section 06 정보보안관리/법규
01. 정보보안관리/법규 단답형
02. 정보보안관리/법규 서술형
03. 정보보안관리/법규 실무형
