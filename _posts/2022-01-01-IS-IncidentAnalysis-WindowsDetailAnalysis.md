---
title: 정보보안 - 윈도우 침해사고 상세분석
date: '2022-01-01 09:00:00'
tags:
- 정보보안기사
- 윈도우 침해사고
- 상세분석
related: true
categories:
- IS_Certification
toc: true
---

# 윈도우 침해사고 상세분석
- 분류
    + 레지스트리 분석
        * 시스템이 운영되는데 필요한 정보를 담고 있는 파일
            - 설치된 S/W 정보, 환경설정, 임시저장값
            - 시스템의 모든 정보 포함
        * 공격자의 흔적을 알아낼 수 있음
        * 시작프로그램 등록 관련 레지스트리
            - `HKLM\Software\Microsoft\Windows\CurrentVersion\Run`
            - `HKLM\Software\Microsoft\Windows\CurrentVersion\RunOnce`
            - `HKLM\Software\Microsoft\Windows\CurrentVersion\RunOnceEx`
            - `HKLM\Software\Microsoft\Windows\CurrentVersion\RunServices`
            - `HKLM\Software\Microsoft\Windows\CurrentVersion\RunServicesOnce`
            - `HKLM\Software\Microsoft\Windows\CurrentVersion\Windows\Load`
            - `HKLM\Software\Microsoft\Windows\CurrentVersion\Windows\Run`
            - `HKLM\Software\Microsoft\Windows\CurrentVersion\Winlogon\UserInit`
            - `HKCU\Software\Microsoft\Windows\CurrentVersion\Run`
            - `HKCU\Software\Microsoft\Windows\CurrentVersion\RunOnce`
            - `HKCU\Software\Microsoft\Windows\CurrentVersion\RunOnceEx`
            - `HKCU\Software\Microsoft\Windows\CurrentVersion\RunServicesOnce`
        * `Autoruns` : 시작 레지스트리 Check (Sysinternals.com)
        * 공격자가 남긴 레지스트리 정보 수집
            - 최근 사용한 문서 목록
                + `HKCU\Software\Microsoft\Windows\CurrentVersion\Explorer\Recentdocs`
            - 터미널 서비스 접속 목록
                + `HKCU\Software\Microsoft\Terminal Server Client\Default`
            - 설치된 S/W 목록
            - 열어본 파일 목록
    + 자동실행 점검 - `Autoruns` 를 통해 점검 가능
        * 서비스 점검
            - 공격자가 윈도우 서비스에 악성 프로그램을 등록, 시스템 재부팅 시 악성 프로그램 실행
            - 생소한 이름의 서비스
            - Description 영역의 내용이 비어있는 경우 / 영문으로 되어있는 경우
            - 정상적 서비스 이름을 위장하는 경우
                + Backup System
                + Remote Administrator Service
                + System spooler Host
                + Windows Management Drivers
                + Universal Serial Bus Control Component
        * 스케줄된 작업 확인
            - 시스템이 재부팅 되더라도 악성 프로그램이 실행될 수 있음
        * 자동시작 폴더 점검
            - 윈도우 재시작 시 자동시작 폴더 내 각종 프로그램들을 실행함
            - `c:\Documents and Settings\Administrator\시작메뉴\프로그램\시작프로그램`
            - `HKCU\Software\Microsoft\Windows\CurrentVersion\Explorer\ShellFolders`
        * Winlogon Notification DLL
            - 적은 코드로 구현이 가능, 안전모드에서 원하는 코드를 실행 가능
            - WinLogon.exe 파일에서 발생하는 이벤트 핸들러를 이용하여 원하는 코드를 실행 가능
                + Logon, Logoff, Startup, Shutdown, ScreenSaver 이벤트 등
            - 악성코드 활용 소지가 있음
            - `HKLM\Software\Microsoft\Windows NT\CurrentVersion\Winlogon\Notify\`
                + DllName = arprmdg0.dll (악성코드 예시)
                + Startup = arprmdg0
                + Impersonate = 1 (인격화. 보안 환경변수 설정시 주로 설정, 인증된 사용자 계정으로 자원 실행할 수 있게 함)
    + 이벤트 로그 분석
        * 공격자가 남긴 흔적, 활동 등의 정보를 찾아내기 위함
        * H/W, S/W, 시스템 문제 시 이벤트로그에 저장함 -> 이벤트뷰어를 통해 확인
            - 관리도구 -> 이벤트뷰어 (eventvwr.msc)
                + 보안로그
                + 시스템로그
                + 응용프로그램로그
        * 공격자들은 자신의 흔적을 최소화 하기 위해 `ClearEvent` 프로그램을 사용 (이벤트로그를 삭제)
        * 공격자가 남기는 로그 유형
            - 로컬 로그온 시도 실패
            - 계정의 잘못된 사용 (없는 계정 로그인 시도)
            - 계정 잠김
            - 터미널서비스 공격
            - 사용자계정 생성
    + MAC time 분석
        * 파일, 디렉터리 등 파일시스템은 시간속성을 가지고 있음
        * 공격자가 파일시스템을 통해서 어떠한 행동을 했는지 판단 (어떤 파일이 생성, 수정, 실행되었는지?)
        * mtime : 파일 수정, 생성
        * atime : 최근 파일을 읽은 시간, 실행한 시간
        * ctime : 파일 속성 변경 시간
    + 침입방법 분석
        * 공격자가 어떻게 침입했는지 분석 (사고 조치 이외에 원인분석을 하지 않으면 사고가 재발할 수 있음)
        * 윈도우에서 해킹발생 취약점 분류 (대부분 해킹사고는 시스템 보안 업데이트 미실시, 웹서비스 취약점이 원인)
            - 윈도우 취약점
                + 시스템 취약점 (보안업데이트 미실시)
                    * 최종 보안 업데이트 날짜 파악이 중요
                        - 원격 공격 가능한 취약점이 있는지 파악
                    * MS 에서 제공하는 Microsoft Baseline Security Analyzer 로 확인
                        - 보안패치 상태
                        - IIS 상태
                        - SQL 상태
                + 패스워드 취약점
                + 잘못된 공유설정
            - 웹 어플리케이션 취약점
                + 웹서비스 취약점
                    * 해킹 발생 시점에 발생한 로그를 분석하여 공격여부, 공격방법등을 파악
                    * IIS 로그 : `C:\WINNT\system32\LogFiles\W3SVC1`
                + SQL Injection
                + 파일업로드
            - MS-SQL 취약점
                + Default password 사용
                + 패치 미실시
    + 인터넷 임시파일 분석
        * 브라우저와 연관
            - 접속한 사이트의 페이지가 임시파일에 저장됨
            - 접속 흔적이 히스토리에 남음
            - 사용된 쿠키가 디스크에 저장됨
        * 파일 위치 : `indexView` 도구를 다운받아 쉽게 분석 가능
            - 임시 인터넷 객체 : `Download Program Files` - 인터넷에서 다운로드 받은 파일들
            - 임시 인터넷 파일 : `Local Settings\Temporary Internet Files` - 방문한 사이트 페이지
            - 열어본 페이지 : `Local Settings\History` - 방문한 사이트 이름
            - 임시쿠키 파일 : `Local Settings\COOKIES` 