---
title: 정보보안 - 접근통제와 사용자 인증
date: '2021-12-01 09:00:00'
tags:
- 정보보안기사
- 접근통제
- 사용자 인증
related: true
categories:
- IS_Certification
toc: true
---

# 접근통제
- 접근통제 개요
    + 접근통제(접근제어, Access Control) 기본 개념
        * 컴퓨터 보안의 주된 목적
            - 허가되지 않은 사용자 (비인가자)에 의한 정보자원 접근 제한
            - 정당한 사용자 (인가자)가 허가되지 않은 방법으로(비인가된 방식) 정보자원에 접근하는 것을 제한
            - 정당한 사용자가 허가된 방식으로만 자원에 접근하도록 하는 것
        * 주체(외부에서 접근하는 사람)가 객체(시스템 등 접근 대상)에 접근(Access) 할 때 보안상의 위협, 변조 등과 같은 위험으로부터 객체와 제반 환경을 보호하기 위한 보안 대책
- 접근통제 절차
    + **식별**(Identification) : 인증 시스템에 본인이 누구인지 스스로를 밝히기 위해 정보를 제공 (ID, 계정번호 등) 하는 주체활동
    + **인증**(Authentication) : 주체의 신원을 검증하기 위한 사용 증명 활동 (패스워드, PIN, 토큰 등)
    + **인가**(Authorization) : 인증된 주체에게 권한을 부여하는 것
    + 책임추적성(Accountability) : 시스템에 접근한 주체가 시스템에 어떤 행위를 하고 있는지 기록
        * 해명책임성으로도 해석됨
        * 사용자의 이용을 추적하고 그의 행동에 대해 기록하고 추적하는 활동
        * 사전 침입 의도를 감소시킴
        * 관여하지 않은 사람에게 책임을 물어 불이익을 당하지 않도록 함
        * 식별, 인증, 권한부여, 접근통제, 감사 개념을 기반으로 수립됨
        * 책임추적이 가장 어려운 경우 : 하나의 계정을 여러 명이 공유할 경우
- 접근통제 기본 원칙
    + **직무 분리**
        * 한 개인이 전체 업무에 접근 -> 전체 업무를 파괴할 가능성이 있음
        * 시스템 단계별로 나누어서 직무를 부여 -> 전체 업무를 파괴하는 사고를 방지
    + **최소 권한 부여**
        * 사용자가 작업을 하는데 있어서 필요한 최소한의 접근권한 / 자원을 부여
        * 사용자의 실수나 의도적인 방법으로 인한 손상을 최소화
    + 입력의 신뢰성
        * 충분한 사용자 인증 기능이 요구됨
    + 개방적 정책과 폐쇄적 정책
    + 정책 결합과 충돌해결
    + 관리정책
- 사용자 인증과 메시지 인증
    + 사용자 인증
        * 사용자 A가 C에 접속을 시도할 때 C와 협조하여 자신이 A임을 증명할 수 있어야 하나, B가 A를 가장할 수 없어야 함
        * 실시간 처리
    + 메시지 인증 (MAC)
        * 실시간 처리 안됨
- 사용자 인증 유형
    + 지식 (Something you know) - 패스워드, PIN
    + 소유 (Something you have) - 토큰, 스마트카드
    + 존재 (Something you are) - 지문
    + 행위 (Something you do) - 서명, 움직임
    + Two Factor - 위 타입 중 2가지 매커니즘을 결합한 것 (예:토큰 + PIN)
        * ATM : PIN + 카드
        * 신용카드 : 카드 + 서명
    + Multi Factor - 위 타입 중 3가지 이상을 결합한 것 (예:토큰 + PIN + 지문)
- 인증 기법
    + 지식에 기반을 둔 인증 (What you know)
        * 장점
            - 다양한 분야에서 사용 가능
            - 검증 방법이 확실 (모호성이 없음)
            - 관리비용 저렴
        * 단점
            - 소유자가 지식을 망각했을 경우
            - 공격자에 의해서 추측이 가능
            - 사회공학적 공격에 매우 취약
    + 소유에 기반을 둔 인증 (What you have)
        * 장점
            - 다양한 매체를 통해 인증 가능 (학생증, 신분증, 열쇠, OTP)
            - 경제적
        * 단점
            - 소유물이 반드시 있어야 함
            - 복제가 가능
            - 손실이나 도난 시 관리기능이 필요
            - 타인이 쉽게 도용 가능 : 단독으로 사용하지 않음
    + 개체 특성에 기반을 둔 인증 (What you are)
        * 장점
            - 지문, 홍채, 음성, 얼굴인식 등 무언가를 소유할 필요가 없음
            - 도난의 염려가 없음
            - 위조가 어려움
        * 단점
            - 판단에 대한 모호성이 존재
            - 관리의 어려움
            - 관리비용 발생