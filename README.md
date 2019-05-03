# 페이플 - 카드 일반결제, 정기결제  
페이플은 계좌 뿐 아니라 카드결제도 제공합니다.<br> 
앱카드 결제고객을 위한 일반결제와 함께 정기배송, 정기구독 서비스를 위한 정기결제를 함께 제공합니다.

<br><br><br>
## 결제서비스별 흐름도 
각 결제서비스의 흐름도입니다. 동일한 조건으로 모든 결제서비스를 이용할 수 있습니다. <br>
![Alt text](/img/process_card.jpeg)
<br><br><br>
## 공통 정의사항 
* 인코딩 : UTF-8 <br>
* SSL 보안통신 필수 <br>
* 메세지 포맷 : JSON <br>
<br><br><br>
## 가맹점 인증 
* 가맹점 인증방식은 [계좌](../../../manual#가맹점-인증)와 동일합니다. 
* Payple 서비스를 이용하기 위해서는 가맹점 계약을 통한 인증이 필요하지만 계약 전 **_테스트 계정을 통해 개발진행이 가능합니다._** 계약 완료 후 Payple 에서 가맹점에 아래의 키를 발급합니다.<br><br> 
  * cst_id (가맹점 식별을 위한 가맹점 ID)
  * custKey (API 통신전문 변조를 방지하기 위한 비밀키) 
<br><br><br>
#### 호출정보
구분 | 테스트 | 운영
---- | ---- | ----
URL | https://testcpay.payple.kr/php/auth.php | https://cpay.payple.kr/php/auth.php
ID | cst_id : test | cst_id : 가맹점 운영 ID 
KEY | custKey : abcd1234567890 | custKey : ID 매칭 Key
비고 | - 테스트 승인이후 일괄 자동취소가 진행됩니다. | - 실제 승인이 진행됩니다.<br>**- AWS(아마존웹서비스)에서 AUTH0004 오류 발생 시 가맹점 서버도메인의 REFERER 추가가 필요할 수 있습니다.**<br>**- 카페24, 가비아 등 서버호스팅 이용 시 호스팅사에 페이플 URL(테스트, 운영) 방화벽 오픈을 요청하셔야 할 수 있습니다.** 

<br><br><br>
#### 호출예시 
* 승인 - Request 
```html
POST /php/auth.php HTTP/1.1
Host: testcpay.payple.kr
Content-Type: application/json
<!-- AWS 이용 가맹점인 경우 REFERER 추가 -->
Referer: http://가맹점 도메인 
<!-- End : AWS 이용 가맹점인 경우 REFERER 추가 -->
Cache-Control: no-cache
{
  "cst_id": "test",
  "custKey": "abcd1234567890"
}
```
* 승인 - Response
```html
{
  "result": "success",
  "result_msg": "사용자 인증완료",
  "cst_id": "UFVNNVZpZk4reWo5UFRualUwcGV4dz09",
  "custKey": "T3JzRkp5L1FTcEFDa1FQdHo5Um1UZz09",
  "AuthKey": "a688ccb3555c25cd722483f03e23065c3d0251701ad6da895eb2d830bc06e34d",
  "return_url": "https://cpay.payple.kr/index.php?ACT_=PAYM",
  "cPayHost": "https://cpay.payple.kr",
  "cPayUrl": "/index.php?ACT_=PAYM"
}
```

* 승인취소 - Request 
```html
POST /php/auth.php HTTP/1.1
Host: testcpay.payple.kr
Content-Type: application/json
<!-- AWS 이용 가맹점인 경우 REFERER 추가 -->
Referer: http://가맹점 도메인 
<!-- End : AWS 이용 가맹점인 경우 REFERER 추가 -->
Cache-Control: no-cache
{
  "cst_id": "test",
  "custKey": "abcd1234567890",
  "PCD_PAYCANCEL_FLAG": "Y"
}
```
* 승인취소 - Response
```html
{
  "result": "success",
  "result_msg": "사용자 인증완료",
  "cst_id": "UFVNNVZpZk4reWo5UFRualUwcGV4dz09",
  "custKey": "T3JzRkp5L1FTcEFDa1FQdHo5Um1UZz09",
  "AuthKey": "a688ccb3555c25cd722483f03e23065c3d0251701ad6da895eb2d830bc06e34d",
  "PCD_PAY_HOST": "https://testcpay.payple.kr",	  // payple Server Host
  "PCD_PAY_URL": "/php/account/api/cPayCAct.php", // payple Server url
  "return_url": "https://testcpay.payple.kr/php/account/api/cPayCAct.php"  // return_url 은 변경되어 수신될 수 있습니다. URL을 고정하지 마세요.
}
```

* 등록카드 해지(정기결제) - Request 
```html
POST /php/auth.php HTTP/1.1
Host: testcpay.payple.kr
Content-Type: application/json
<!-- AWS 이용 가맹점인 경우 REFERER 추가 -->
Referer: http://가맹점 도메인 
<!-- End : AWS 이용 가맹점인 경우 REFERER 추가 -->
Cache-Control: no-cache
{
  "cst_id": "test",
  "custKey": "abcd1234567890",
  "PCD_PAY_WORK": "PUSERDEL"
}
```
* 등록카드 해지(정기결제) - Response
```html
{
  "result": "success",
  "result_msg": "사용자 인증완료",
  "cst_id": "UFVNNVZpZk4reWo5UFRualUwcGV4dz09",
  "custKey": "T3JzRkp5L1FTcEFDa1FQdHo5Um1UZz09",
  "AuthKey": "a688ccb3555c25cd722483f03e23065c3d0251701ad6da895eb2d830bc06e34d",
  "PCD_PAY_HOST": "https://testcpay.payple.kr",	  // payple Server Host
  "PCD_PAY_URL": "/php/cPayUser/api/cPayUserAct.php?ACT_=PUSERDEL", // payple Server url
  "return_url": "https://testcpay.payple.kr/php/cPayUser/api/cPayUserAct.php?ACT_=PUSERDEL"  // return_url 은 변경되어 수신될 수 있습니다. URL을 고정하지 마세요.
}
```

<br><br><br>
## 결제요청 
### 1. 최초결제 - 공통  
* 페이플은 자바스크립트만을 이용해 모든 결제절차를 진행합니다. <br><br>
* 앱카드 사용자를 위한 **일반결제** 화면입니다.<br><br>
![Alt text](/img/card_genaral.png) <br><br>
* **정기결제** 화면입니다. 이용을 위해서는 별도 심사가 필요하며 카드번호, 유효기간, 생년월일, 비밀번호 입력으로 결제가 진행됩니다.<br><br>
![Alt text](/img/card_bill.png) <br><br>
* 정기결제에서 최초결제없이 **카드등록만 하기 위해서는 obj.PCD_PAY_WORK = 'AUTH'** 로 세팅하시면 됩니다.<br><br>
![Alt text](/img/card_reg.png) <br><br>
* 아래 소스코드를 가맹점 주문페이지에 추가합니다.
```html
<!-- payple js 호출. 테스트/운영 선택 -->
<script src="https://testcpay.payple.kr/js/cpay.payple.1.0.1.js"></script> <!-- 테스트 -->
<script src="https://cpay.payple.kr/js/cpay.payple.1.0.1.js"></script> <!-- 운영 -->

<!-- 가맹점 주문페이지 '결제하기' 버튼 액션 -->
<script>	
$(document).ready( function () {        
    $('#payAction').on('click', function (event) {
        
        var pay_work = "CERT";
        var payple_payer_id = "d0toSS9sT084bVJSNThScnFXQm9Gdz09";     
        var buyer_no = "2335";
        var buyer_name = "홍길동";
        var buyer_hp = "01012345678";
        var buyer_email = "dev@payple.kr";
        var buy_goods = "휴대폰";
        var buy_total = "1000";
        var order_num = "test0553941001540967923";
        var is_reguler = "N";
        var pay_year = "";
        var pay_month = "";
        
        var obj = new Object();
        
        obj.PCD_CPAY_VER = "1.0.1";
        obj.PCD_PAY_TYPE = 'card';           
        obj.PCD_PAY_WORK = pay_work;
	obj.PCD_CARD_VER = "02"
	/* (필수) 가맹점 인증요청 파일 (Node.JS : auth => [app.js] app.post('/pg/auth', ...) */
        obj.payple_auth_file = '/pg/auth'; // 절대경로 포함 파일명 (예: /절대경로/payple_auth_file)
	/* End : 가맹점 인증요청 파일 */
	
	/* 결과를 콜백 함수로 받고자 하는 경우 함수 설정 추가 */
        obj.callbackFunction = getResult;  // getResult : 콜백 함수명 
        /* End : 결과를 콜백 함수로 받고자 하는 경우 함수 설정 추가 */
	
	/* 결과를 콜백 함수가 아닌 URL로 받고자 하는 경우 */
	obj.PCD_RST_URL = '/order_result.html';
	/* End : 결과를 콜백 함수가 아닌 URL로 받고자 하는 경우 */
	 
        obj.PCD_PAYER_NO = buyer_no;  
        obj.PCD_PAYER_EMAIL = buyer_email;
        obj.PCD_PAY_GOODS = buy_goods;
        obj.PCD_PAY_TOTAL = buy_total;
        obj.PCD_PAY_OID = order_num;     
        obj.PCD_REGULER_FLAG = is_reguler;
        obj.PCD_PAY_YEAR = pay_year; 
        obj.PCD_PAY_MONTH = pay_month;
	
        PaypleCpayAuthCheck(obj);
            
        event.preventDefault();     
    });   
});
</script>
```

파라미터 ID | 설명 | 필수 | 비고
:----: | :----: | :----: | ----
PCD_CPAY_VER | 결제창 버전 | O | 최신 : 1.0.1 
PCD_PAY_TYPE | 결제수단 | O | 
PCD_PAY_WORK | 결제요청 방식 | O | - AUTH : 계좌등록만 진행<br>- CERT : 가맹점 최종승인 후 계좌등록+결제 진행<br>- PAY : 가맹점 최종승인없이 계좌등록+결제 진행 
PCD_CARD_VER | 일반/정기결제 | O | - 01 : 정기<br>- 02 : 일반 
PCD_RST_URL | 결제(요청)결과 RETURN URL | O | - 결제결과를 콜백 함수가 아닌 URL로 수신할 경우만 해당<br>- 모바일에서 팝업방식은 상대경로, 다이렉트 방식은 절대경로로 설정  
PCD_PAYER_NO | 가맹점의 결제고객 고유번호 | O | maxlength=10
PCD_PAYER_EMAIL | 결제고객 이메일 | - | 
PCD_PAY_GOODS | 상품명 | O | 
PCD_PAY_TOTAL | 결제금액 | O | 
PCD_PAY_OID | 주문번호 | - | 미입력시 임의생성 
PCD_REGULER_FLAG | 정기결제 여부 | - | 
PCD_PAY_YEAR | 정기결제 적용연도 | - | PCD_REGULER_FLAG : 'Y' 일때 필수
PCD_PAY_MONTH | 정기결제 적용월 | - | PCD_REGULER_FLAG : 'Y' 일때 필수

<br><br>
#### 1-1. 결제생성 후 승인(PCD_PAY_WORK : CERT) 
* 가맹점의 최종 승인 후에 결제를 진행하며 REST Request 방식으로 진행합니다. 
* Request 예시 
```html
<!-- 결제 승인요청 --> 
POST /php/PayConfirmAct.php?ACT_=PAYM HTTTP/1.1
Host: testcpay.payple.kr
Content-Type: application/json
Cache-Control: no-cache
{
  "PCD_CST_ID": "test",										
  "PCD_CUST_KEY": "abcd1234567890",								
  "PCD_AUTH_KEY": "a688ccb3555c25cd722483f03e23065c3d0251701ad6da895eb2d830bc06e34d", 
  "PCD_PAY_REQKEY": "RmFBWWFBTWNS9qNTgzU2xdd2XRNHR2",					
  "PCD_PAYER_ID": "NS9qNTgzU2xRNHR2RmFBWWFBTWk5UT09"
}
```
* Request 파라미터 설명 

파라미터 ID | 설명 | 필수 | 비고
:----: | :----: | :----: | :----:
PCD_CST_ID | 가맹점 ID | O | 
PCD_CUST_KEY | 가맹점 식별을 위한 비밀키 | O | 
PCD_AUTH_KEY | 결제요청을 위한 Transaction 키 | O | 
PCD_PAY_REQKEY | 최종 승인요청용 키 | O | 
PCD_PAYER_ID | 결제고객 고유 ID | - | 정기결제(PCD_CARD_VER=01) 시 필수 

<br><br>
#### 1-2. 즉시 승인(PCD_PAY_WORK : PAY) 
* 가맹점의 최종 승인없이 즉시 결제를 진행하며 별도 Request 는 없습니다.  

<br><br><br>
### 2. 이후결제 - 일반결제 
* 단건결제도 최초 1회 이후결제를 위해서는 [1. 최초결제 - 공통](#1-최초결제---공통)과 동일한 스크립트를 사용합니다. 
* 사용자는 매번 결제정보를 입력해야하며, 최초결제 시 ARS 인증, 이후결제 시에는 SMS 인증으로 결제를 진행합니다. 

<br><br><br>
### 3. 이후결제 - 정기결제
* 정기결제는 최초 1회 이후 결제 시 별도 UI가 필요없기 때문에 REST Request 방식으로 진행합니다.
* Request 예시 
```html
<!-- 가맹점 인증 -->
POST /php/auth.php HTTP/1.1
Host: testcpay.payple.kr
Content-Type: application/json
Cache-Control: no-cache
{
  "cst_id": "test",
  "custKey": "abcd1234567890",
  /* 월 1회 정기결제 : PCD_REGULER_FLAG / 월 2회 이상 : PCD_SIMPLE_FLAG 이용  */
  "PCD_REGULER_FLAG": "Y",
  "PCD_PAY_TYPE": "card"
}

<!-- 결제요청  -->
POST /php/RePayCardAct.php?ACT_=PAYM HTTTP/1.1
Host: testcpay.payple.kr
Content-Type: application/json
Cache-Control: no-cache
{
   "PCD_CST_ID": "test",
   "PCD_CUST_KEY": "abcd1234567890",
   "PCD_AUTH_KEY": "a688ccb3555c25cd722483f03e23065c3d0251701ad6da895eb2d830bc06e34d",
   "PCD_PAY_TYPE": "card",							
   "PCD_PAYER_NO": "2324",
   "PCD_PAYER_ID": "NS9qNTgzU2xRNHR2RmFBWWFBTWk5UT09",
   "PCD_PAY_GOODS": "정기구독",
   "PCD_PAY_YEAR": "2018",	
   "PCD_PAY_MONTH": "04",	
   "PCD_PAY_TOTAL": "1000",
   "PCD_PAY_OID": "test201804000001",
   "PCD_REGULER_FLAG": "Y",
   "PCD_PAYER_EMAIL": "dev@payple.kr"
}
```

파라미터 ID | 설명 | 필수 | 비고
:----: | :----: | :----: | :----:
PCD_CST_ID | 가맹점 ID | O | 
PCD_CUST_KEY | 가맹점 식별을 위한 비밀키 | O | 
PCD_AUTH_KEY | 결제요청을 위한 Transaction 키 | O | 
PCD_PAY_TYPE | 결제수단 | O | 
PCD_PAYER_NO | 가맹점의 결제고객 고유번호 | O | 
PCD_PAYER_ID | 결제 키 | O | 해당 키를 통해 결제요청
PCD_PAY_GOODS | 상품명 | O | 
PCD_PAY_YEAR | 과금연도 | O | PCD_REGULER_FLAG 시 필수  
PCD_PAY_MONTH | 과금월 | O | PCD_REGULER_FLAG 시 필수
PCD_PAY_TOTAL | 결제금액 | O | 
PCD_PAY_OID | 주문번호 | O | 
PCD_REGULER_FLAG | 정기결제 여부 | O | 월 2회 이상 결제 시 PCD_SIMPLE_FLAG 이용 
PCD_PAYER_EMAIL | 결제고객 이메일 | - | 

* Response 예시 
```html
{
  "PCD_PAY_RST" => "success",
  "PCD_PAY_MSG" => "완료",
  "PCD_PAY_OID" => "test201804000001",	
  "PCD_PAY_TYPE" => "card",
  "PCD_PAYER_NO" => "2324",
  "PCD_PAYER_ID" => "NS9qNTgzU2xRNHR2RmFBWWFBTWk5UT09",
  "PCD_PAY_YEAR" => "2018",
  "PCD_PAY_MONTH" => "04",
  "PCD_PAY_GOODS" => "정기구독",
  "PCD_PAY_TOTAL" => "1000",
  "PCD_PAY_TIME" => "20180423130201",
  "PCD_PAY_CARDNANE" => "BC 카드",
  "PCD_PAY_CARDNUM" => "12345678****1234",
  "PCD_PAY_CARDTRADENUM" => "201904141320332692022400",
  "PCD_REGULER_FLAG" => "Y",
  "PCD_PAYER_EMAIL" => "dev@payple.kr"
}
```
* Response 파라미터 설명

파라미터 ID | 설명 | 예시
:----: | :----: | :----: 
PCD_PAY_RST | 계좌해지 요청 결과 | success / error 
PCD_PAY_MSG | 계좌해지 요청 결과 메세지 | 완료 / 실패
PCD_PAY_OID | 주문번호 | test201804000001
PCD_PAY_TYPE | 결제수단 | card 
PCD_PAYER_NO | 가맹점의 결제고객 고유번호 | 2324
PCD_PAYER_ID | 결제 키 | NS9qNTgzU2xRNHR2RmFBWWFBTWk5UT09
PCD_PAY_YEAR | 과금연도 | 2018 
PCD_PAY_MONTH | 과금월 | 04
PCD_PAY_GOODS | 상품명 | 정기구독
PCD_PAY_TOTAL | 결제금액 | 1000
PCD_PAY_TIME | 결제완료 시간 | 20180110152911
PCD_PAY_CARDNANE | 카드사명 | BC카드
PCD_PAY_CARDNUM | 카드번호 | 
PCD_PAY_CARDTRADENUM | 카드승인 거래번호 | 201904141320332692022400
PCD_REGULER_FLAG | 정기결제 여부 | Y / N
PCD_PAYER_EMAIL | 결제고객 이메일 | dev@payple.kr

<br><br><br>
### 4. 승인취소 
* 결제 후 승인취소를 요청하는 REST API 입니다. 
* Request 예시 
```html
<!-- 가맹점 인증 -->
POST /php/auth.php HTTP/1.1
Host: testcpay.payple.kr
Content-Type: application/json
Cache-Control: no-cache
{
  "cst_id": "test",
  "custKey": "abcd1234567890",
  "PCD_PAY_WORK": "PCD_PAYCANCEL_FLAG": "Y"
}

<!-- 승인취소 요청  -->
POST /php/PayChkAct.php HTTTP/1.1
Host: testcpay.payple.kr
Content-Type: application/json
Cache-Control: no-cache
{
  "PCD_CST_ID" : "test",
  "PCD_CUST_KEY" : "abcd1234567890",
  "PCD_AUTH_KEY" : "a688ccb3555c25cd722483f03e23065c3d0251701ad6da895eb2d830bc06e34d",
  "PCD_REFUND_KEY" : "a41ce010ede9fcbfb3be86b24858806596a9db68b79d138b147c3e563e1829a0",
  "PCD_PAYCANCEL_FLAG": "Y",
  "PCD_PAY_OID": "test201804000001",
  "PCD_PAY_TYPE": "card",
  "PCD_REGULER_FLAG": "Y",
  "PCD_PAY_YEAR": "2019",
  "PCD_PAY_MONTH": "05",
  "PCD_PAY_DATE": "20180502",
  "PCD_REFUND_TOTAL": "1000"
}
```

파라미터 ID | 설명 | 필수 | 비고
:----: | :----: | :----: | :----:
PCD_CST_ID | 가맹점 ID | O | 
PCD_CUST_KEY | 가맹점 식별을 위한 비밀키 | O | 
PCD_AUTH_KEY | 결제요청을 위한 Transaction 키 | O | 
PCD_REFUND_KEY | 취소용 키 | O | 관리자화면에서 확인 가능 
PCD_PAYCANCEL_FLAG | 취소요청 여부 | O | 
PCD_PAY_OID | 주문번호 | O | 
PCD_PAY_TYPE | 결제수단 | O | 
PCD_REGULER_FLAG | 정기결제 여부 | - | 
PCD_PAY_YEAR | 정기결제 구분 연도 | - | 
PCD_PAY_MONTH | 정기결제 구분 월 | - | 
PCD_PAY_DATE | 결제일자 | O | YYYYMMDD 
PCD_REFUND_TOTAL | 취소금액 | O | 원거래금액 미만 취소 시 부분취소 적용 

* Response 예시 
```html
{
  "PCD_PAY_RST" => "success",
  "PCD_PAY_MSG" => "취소완료",
  "PCD_PAY_OID" => "test201804000001",
  "PCD_PAY_TYPE" => "card",
  "PCD_PAYER_ID" => "NS9qNTgzU2xRNHR2RmFBWWFBTWk5UT09",
  "PCD_PAYER_NO" => "2324",
  "PCD_REGULER_FLAG" => "Y",
  "PCD_PAY_YEAR" => "2019",
  "PCD_PAY_MONTH" => "05",
  "PCD_PAY_GOODS" => "간편구독",
  "PCD_REFUND_TOTAL" => "1000"
}
```
* Response 파라미터 설명

파라미터 ID | 설명 | 예시
:----: | :----: | :----: 
PCD_PAY_RST | 승인취소 요청 결과 | success / error 
PCD_PAY_MSG | 승인취소 요청 결과 메세지 | 취소완료 / 실패
PCD_PAY_TYPE | 결제수단 | card 
PCD_PAYER_ID | 결제 키 | NS9qNTgzU2xRNHR2RmFBWWFBTWk5UT09
PCD_PAYER_NO | 가맹점의 결제고객 고유번호 | 2324
PCD_REGULER_FLAG | 정기결제 여부 | Y / N
PCD_PAY_YEAR | 정기결제 과금 연도 | 2019
PCD_PAY_MONTH | 정기결제 과금 월 | 05
PCD_PAY_GOODS | 결제 상품명 | 간편구독
PCD_REFUND_TOTAL | 취소금액 | 1000

<br><br><br>
### 5. 등록카드 해지(정기결제) 
* 정기결제 시 최초 등록된 카드정보를 해지하는 REST API 입니다.
* Request 예시 
```html
<!-- 가맹점 인증 -->
POST /php/auth.php HTTP/1.1
Host: testcpay.payple.kr
Content-Type: application/json
Cache-Control: no-cache
{
  "cst_id": "test",
  "custKey": "abcd1234567890",
  "PCD_PAY_WORK": "PUSERDEL"
}

<!-- 해지 요청  -->
POST /php/cPayUser/api/cPayUserAct.php?ACT_=PUSERDEL HTTP/1.1
Host: testcpay.payple.kr
Content-Type: application/json
Cache-Control: no-cache
{
  "PCD_CST_ID" : "test",
  "PCD_CUST_KEY" : "abcd1234567890",
  "PCD_AUTH_KEY" : "a688ccb3555c25cd722483f03e23065c3d0251701ad6da895eb2d830bc06e34d",
  "PCD_PAYER_ID" : "NS9qNTgzU2xRNHR2RmFBWWFBTWk5UT09",
  "PCD_PAYER_NO" : "2324"
}
```

파라미터 ID | 설명 | 필수 | 비고
:----: | :----: | :----: | :----:
PCD_CST_ID | 가맹점 ID | O | 
PCD_CUST_KEY | 가맹점 식별을 위한 비밀키 | O | 
PCD_AUTH_KEY | 결제요청을 위한 Transaction 키 | O | 
PCD_PAYER_ID | 결제 키 | O | 
PCD_PAYER_NO | 가맹점의 결제고객 고유번호 | - | 

* Response 예시 
```html
{
  "PCD_PAY_RST" => "success",
  "PCD_PAY_MSG" => "해지완료",
  "PCD_PAY_TYPE" => "card",
  "PCD_PAY_WORK" => "PUSERDEL",
  "PCD_PAYER_ID" => "NS9qNTgzU2xRNHR2RmFBWWFBTWk5UT09",
  "PCD_PAYER_NO" => "2324"
}
```
* Response 파라미터 설명

파라미터 ID | 설명 | 예시
:----: | :----: | :----: 
PCD_PAY_RST | 해지요청 결과 | success / error 
PCD_PAY_MSG | 해지요청 결과 메세지 | 완료 / 실패
PCD_PAY_TYPE | 결제수단 | card 
PCD_PAY_WORK | 업무구분 | PUSERDEL (해지)
PCD_PAYER_ID | 결제 키 | NS9qNTgzU2xRNHR2RmFBWWFBTWk5UT09
PCD_PAYER_NO | 가맹점의 결제고객 고유번호 | 2324

<br><br><br>
## 결제결과 수신  
> 결제결과를 콜백 함수로 수신하는 경우에는 아래 절차가 필요없습니다. 
* 콜백 함수를 이용하지 않는 경우에는 아래 소스코드를 가맹점 결제완료 페이지에 추가하고 가맹점 환경에 맞는 개발언어로 수정해주세요.
```php
<?
$PCD_PAY_RST = (isset($_POST['PCD_PAY_RST'])) ? $_POST['PCD_PAY_RST'] : ""; 
$PCD_PAY_MSG = (isset($_POST['PCD_PAY_MSG'])) ? $_POST['PCD_PAY_MSG'] : ""; 
$PCD_AUTH_KEY = (isset($_POST['PCD_AUTH_KEY'])) ? $_POST['PCD_AUTH_KEY'] : "";
$PCD_PAY_REQKEY = (isset($_POST['PCD_PAY_REQKEY'])) ? $_POST['PCD_PAY_REQKEY'] : "";
$PCD_PAY_COFURL = (isset($_POST['PCD_PAY_COFURL'])) ? $_POST['PCD_PAY_COFURL'] : "";
$PCD_PAY_OID = (isset($_POST['PCD_PAY_OID'])) ? $_POST['PCD_PAY_OID'] : "";         
$PCD_PAY_TYPE = (isset($_POST['PCD_PAY_TYPE'])) ? $_POST['PCD_PAY_TYPE'] : "";      
$PCD_PAY_WORK = (isset($_POST['PCD_PAY_WORK'])) ? $_POST['PCD_PAY_WORK'] : "";      
$PCD_PAYER_ID = (isset($_POST['PCD_PAYER_ID'])) ? $_POST['PCD_PAYER_ID'] : "";      
$PCD_PAYER_NO = (isset($_POST['PCD_PAYER_NO'])) ? $_POST['PCD_PAYER_NO'] : "";      
$PCD_REGULER_FLAG = (isset($_POST['PCD_REGULER_FLAG'])) ? $_POST['PCD_REGULER_FLAG'] : "";
$PCD_PAY_YEAR = (isset($_POST['PCD_PAY_YEAR'])) ? $_POST['PCD_PAY_YEAR'] : ""; 
$PCD_PAY_MONTH = (isset($_POST['PCD_PAY_MONTH'])) ? $_POST['PCD_PAY_MONTH'] : "";
$PCD_PAY_GOODS = (isset($_POST['PCD_PAY_GOODS'])) ? $_POST['PCD_PAY_GOODS'] : "";
$PCD_PAY_TOTAL = (isset($_POST['PCD_PAY_TOTAL'])) ? $_POST['PCD_PAY_TOTAL'] : "";
$PCD_PAY_TIME = (isset($_POST['PCD_PAY_TIME'])) ? $_POST['PCD_PAY_TIME'] : ""; 
$PCD_CARDNUM = (isset($_POST['PCD_CARDNUM'])) ? $_POST['PCD_CARDNUM'] : "";
$PCD_CARDNAME = (isset($_POST['PCD_CARDNAME'])) ? $_POST['PCD_CARDNAME'] : "";
$PCD_CARDTRADENUM = (isset($_POST['PCD_CARDTRADENUM'])) ? $_POST['PCD_CARDTRADENUM'] : "";
?>
```

* 파라미터 설명 

파라미터 ID | 설명 | 예시
:----: | :----: | :----: 
PCD_PAY_RST | 결제요청 결과 | success / error 
PCD_PAY_MSG | 결제요청 결과 메세지 | 출금이체완료 / 실패 등 
PCD_AUTH_KEY | 결제요청을 위한 Transaction 키 | a688ccb3... 
PCD_PAY_REQKEY | 결제생성후 승인을 위한 키 | RmFBWWFBTWNS9qNTgzU2xdd2XRNHR2
PCD_PAY_COFURL | 결제생성, 승인후 리턴 URL | https://cpay.payple.kr/php/PayConfirmAct.php 
PCD_PAY_OID | 주문번호 | test201804000001
PCD_PAY_TYPE | 결제수단 | 
PCD_PAY_WORK | 결제요청방식 | CERT / PAY 
PCD_PAYER_ID | 결제 키 | NS9qNTgzU2xRNHR2RmFBWWFBTWk5UT09
PCD_PAYER_NO | 결제고객 고유번호 | 1234 
PCD_REGULER_FLAG | 정기결제 여부 | Y / N
PCD_PAY_YEAR | 과금연도<br>(정기결제) | 2018 
PCD_PAY_MONTH | 과금월<br>(정기결제) | 08
PCD_PAY_GOODS | 상품명 | 정기구독 
PCD_PAY_TOTAL | 결제금액 | 1000
PCD_PAY_TIME | 결제완료 시간 | 20180110152911
PCD_CARDNUM | 카드번호 | 12345678 ** ** 1234
PCD_CARDNAME | 카드사명 | BC카드
PCD_CARDTRADENUM | 거래번호 | 201904141320332692022400

<br><br><br>
## 결제결과 조회  
* 해당 REST API를 통해 가맹점에서는 언제든 결제결과를 수신 가능합니다.
* Request 예시 
```html
POST /php/auth.php HTTP/1.1
Host: testcpay.payple.kr
Content-Type: application/json
Cache-Control: no-cache
{
  "cst_id": "test",
  "custKey": "abcd1234567890",
  "PCD_PAYCHK_FLAG": "Y"
}

POST /php/PayChkAct.php HTTTP/1.1
Host: testcpay.payple.kr
Content-Type: application/json
Cache-Control: no-cache
{
   "PCD_CST_ID": "test",
   "PCD_CUST_KEY": "abcd1234567890",
   "PCD_AUTH_KEY": "a688ccb3555c25cd722483f03e23065c3d0251701ad6da895eb2d830bc06e34d",
   "PCD_PAYCHK_FLAG": "Y",
   "PCD_PAY_TYPE": "card",
   "PCD_REGULER_FLAG": "Y",							
   "PCD_PAY_YEAR": "2018",	
   "PCD_PAY_MONTH": "04",	
   "PCD_PAY_OID": "test201804000001",
   "PCD_PAY_DATE": "20180502"
}
```
* Request 파라미터 설명 

파라미터 ID | 설명 | 필수 | 비고
:----: | :----: | :----: | :----:
PCD_CST_ID | 가맹점 ID | O | 
PCD_CUST_KEY | 가맹점 식별을 위한 비밀키 | O | 
PCD_AUTH_KEY | 결제요청을 위한 Transaction 키 | O | 
PCD_PAYCHK_FLAG | 결과조회 여부 | O | 
PCD_PAY_TYPE | 결제수단 | O | 
PCD_REGULER_FLAG | 정기결제 여부 | - | 정기결제
PCD_PAY_YEAR | 정기결제 과금연도 | - | 정기결제
PCD_PAY_MONTH | 정기결제 과금월 | - | 정기결제
PCD_PAY_OID | 주문번호 | O | 
PCD_PAY_DATE | 결제요청일자(YYYYMMDD) | O | 

* Response 예시 
```html
{
   "PCD_PAY_RST" => "success",
   "PCD_PAY_MSG" => "조회완료",
   "PCD_PAY_OID" => "test201804000001",
   "PCD_PAY_TYPE" => "card",
   "PCD_PAYER_NO" => "1234",
   "PCD_PAYER_ID" => "NS9qNTgzU2xRNHR2RmFBWWFBTWk5UT09",
   "PCD_PAY_YEAR" => "2018",
   "PCD_PAY_MONTH" => "05",
   "PCD_PAY_GOODS" => "간편상품",
   "PCD_PAY_TOTAL" => "1000",
   "PCD_PAY_TIME" => "20180423130201",
   "PCD_REGULER_FLAG" => "Y",
   "PCD_PAYER_EMAIL" => "dev@payple.kr",
   "PCD_CARDNUM" => "12345678 ** ** 1234",
   "PCD_CARDNAME" => "BC카드",
   "PCD_CARDTRADENUM" => "201904141320332692022400"
}
```
* Response 파라미터 설명 

파라미터 ID | 설명 | 예시
:----: | :----: | :----:
PCD_PAY_RST | 결제요청 결과 | success / error 
PCD_PAY_MSG | 결제요청 결과 메세지 | 출금이체완료 / 실패 등 
PCD_PAY_OID | 주문번호 | test201804000001
PCD_PAY_TYPE | 결제수단 | 
PCD_PAYER_NO | 결제고객 고유번호 | 1234 
PCD_PAYER_ID | 결제 키 | NS9qNTgzU2xRNHR2RmFBWWFBTWk5UT09
PCD_PAY_YEAR | 과금연도<br>(정기결제) | 2018 
PCD_PAY_MONTH | 과금월<br>(정기결제) | 08
PCD_PAY_GOODS | 상품명 | 정기구독 
PCD_PAY_TOTAL | 결제금액 | 1000
PCD_PAY_TIME | 결제완료 시간 | 20180110152911
PCD_REGULER_FLAG | 정기결제 여부 | Y / N
PCD_PAYER_EMAIL | 결제고객 이메일 | dev@payple.kr
PCD_CARDNUM | 카드번호 | 12345678 ** ** 1234
PCD_CARDNAME | 카드사명 | BC카드
PCD_CARDTRADENUM | 거래번호 | 201904141320332692022400

<br><br><br>
## 문의  
* 기술문의 : dev@payple.kr 을 통해 보다 자세한 문의가 가능합니다.
* 가입문의 : 페이플 웹사이트 [가입문의하기](https://www.payple.kr) 를 통하시면 가장 빠르게 안내 받으실 수 있습니다. 
