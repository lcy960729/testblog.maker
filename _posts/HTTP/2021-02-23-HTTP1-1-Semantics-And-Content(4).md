---
title: 'HTTP 1.1 Reference - Semantics and Content (4) (최종 수정 날짜 : 2021-02-23)'
tags: Web HTTP1.1
categories: http
author: CY
key: HTTP 1.1 Reference - Semantics and Content (4)
---
# 참고 자료
* 이병록님이 번역하신 HTTP 1.1 Semantics and Content 번역본  
  [https://roka88.dev/106](https://roka88.dev/106)
* MDN Web Docs  
  [https://developer.mozilla.org/ko/docs/Web/HTTP](https://developer.mozilla.org/ko/docs/Web/HTTP)

이외 자료는 내용 중 링크 추가
##### 틀린 내용, 부족한 내용 꼭 지적 부탁드립니다!

# 6. Response Status Codes
status-code 요소는 요청을 이해하고 충족시키려는 시도의 결과를 제공하는 세 자리 정수 코드다.

HTTP 상태 코드는 확장 가능하다. HTTP 클라이언트는 등록된 모든 상태 코드의 의미를 이해 할 필요는 없지만, 
그러한 이해는 분명히 바람직하다. 그러나 클라이언트는 첫 번째 숫자로 표시된 상태 코드의 클래스를 이해하고 
인식되지 않는 상태 코드를 해당 클래스의 x00 상태 코드에 해당하는 것으로 취급해야 하며, 
단, 수신자는 인식되지 않는 상태 코드로 응답을 캐시하면 안 된다는 점을 제외한다.

예를 들어, 인식되지 않는 상태 코드 471이 클라이언트에 의해 수신된 경우, 클라이언트는 자신의 요청에 이상이 있다고 가정하고, 
응답은 400(Bad Request) 상태 코드를 받은 것처럼 취급할 수 있다. 응답 메시지에는 일반적으로 상태를 설명하는 representation이 포함될 것이다.

상태 코드의 첫 번째 자릿수는 응답 클래스를 정의한다. 마지막 두 자리에는 분류 역할이 없다(마지막 두 자리가 어떠한 의미로 분류되지 않음). 첫번째 자리에는 다섯 개의 값이 있다.
* 1xx(Informational)  : 요청이 수신되어 프로세스가 계속 진행됨
* 2xx(Successful)     : 요청이 성공적으로 수신, 이해 및 수락됨
* 3xx(Redirection)    : 요청을 완료하려면 추가 조치를 취해야함
* 4xx(Client Error)   : 요청에 잘못된 구문이 포함되어 있거나 충족할 수 없음 (요청 자체가 잘못됐을때)
* 5xx(Server Error)   : 서버가 유효한 요청을 수행하지 못함(요청은 올바른데 서버에서 처리를 못할때)

## 6.1. Overview of Status Codes
아래에 나열된 상태 코드의 Reason-Phrase는 권장 사항일 뿐이며 프로토콜에 영향을 주지 않고 로컬 등가물(이게 뭔말이지)로 대체 될 수 있다.

기본적으로 캐시 가능한 상태 코드(이 명세는 200, 203, 206, 300, 301, 404, 405, 410, 414, 501)가 있는 응답은 
메서드 정의나 명시적 캐시 제어[RFC7234 5.2 참고]로 달리 명시되지 않는 한 경험적 접근 만료를 가진 캐시에서 재사용할 수 있으며, 다른 모든 상태 코드는 
기본적으로 캐시 가능하지 않다.

Code|Reason-Phrase
---|---
100|Continue
101|Switching Protocols
200|OK
201|Created
202|Accepted
203|Non-Authoritative Information
204|No Content
205|Reset Content
206|Partial Content
300|Multiple Choices
301|Moved Permanently
302|Found
303|See Other
304|Not Modified
305|Use Proxy
307|Temporary Redirect
400|Bad Request
401|Unauthorized
402|Payment Required
403|Forbidden
404|Not Found
405|Method Not Allowed
406|Not Acceptable
407|Proxy Authentication Required
408|Request Timeout
409|Conlict
410|Gone
411|Length Required
412|Precondition Failed
413|Payload Too Large
414|URI Too Long
415|Unsupported Media Type
416|Range Not Satisfiable
417|Expectation Failed
426|Upgrade Required
500|Internal Server Error
501|Not Implemented
502|Bad GateWay
503|Service Unavailable
504|Gateway Timeout
505|HTTP Version Not Supported

이 목록은 모든 상태코드를 나타내는것은 아니며, 다른 사양에 정의된 확장 상태 코드를 포함하지 않고 있다. 
상태 코드의 전체 목록은 IANA에 의해 유지 된다.

### 6.2. Informational 1xx
1xx (Informational) 등급의 상태 코드는 요청된 조치를 완료하고 최종 응답을 보내기 전에 커넥션 상태 
또는 요청 진행 상황을 전달하기 위한 중간 응답을 나타낸다. 1xx 응답은 status-line(헤더 부문의 끝을 알리는 빈 라인) 
후 첫 번째 빈 라인에 의해 종료 된다. HTTP/1.0은 1xx 상태 코드를 정의하지 않았으므로 서버는 1xx 응답을 
HTTP/1.0 클라이언트에 전송해서는 안 된다.

클라이언트는 최종 응답 전에 받은 1xx 응답 중 하나 이상을 구문 분석할 수 있어야 한다. 
사용자 에이전트는 예기치 않은 1xx 응답을 무시할 수 있다.

프록시 자체가 1xx 응답 생성을 요청하지 않는 한 프록시는 1xx 응답을 전달해야 한다. 
예를 들어, 요청을 전달할 때 프록시가 "Expect:100-continue" 필드를 추가하는 경우, 
해당 100(Continue) 응답을 전송할 필요가 없다.

### 6.2.1. 100 Continue
100 (Continue) 상태 코드는 요청의 초기 부분이 수신되어 아직 서버에서 거부되지 않았음을 나타낸다. 
서버는 요청이 완전히 접수되고 조치된 후 최종 응답을 보내려고 한다.

요청이 100-continue를 포함하는 Expect 헤더 필드를 포함할 경우, 100 응답은 서버가 요청 페이로드 본문을 수신하기를 원함을 나타낸다. 
클라이언트는 요청을 계속 보내고 100 응답을 폐기해야 한다.

요청이 100-continue를 포함하는 Expect 헤더 필드를 포함하지 않은 경우, 
클라이언트는 이 중간 응답을 간단히 폐기할 수 있다.

### 6.2.2. 101 Switching Protocols
101 (Switching Protocols) 상태 코드는 서버가 이 커넥션에 사용되는 어플리케이션 프로콜의 변경에 대해 Upgrage 헤더 필드를
통해 클라이언트의 요청을 이해하고 이를 준수할 의사가 있음을 나타낸다. 서버는 101 응답과 전환할 프로토콜을 나타내는 Upgrage 헤더 필드를 생성해야 한다.

서버가 프로토콜 전환에 유리한 경우에만 동의할 것으로 가정한다. 예를 들어, 이전 버전보다 새로운 버전의 HTTP로 전환하는 것이 유리할 수 있으며, 
이러한 기능을 사용하는 리소스를 제공할 때 실시간 동기식 프로토콜로 전환하는 것이 유리할 수 있다.

## 6.3. Successful 2xx
2xx (Successful) 등급의 상태 코드는 클라이언트의 요청이 성공적으로 수신, 이해 및 수용되었음을 나타낸다.

### 6.3.1. 200 OK
200 (OK) 상태 코드는 요청이 성공했음을 나타낸다. 200개의 응답으로 전송되는 페이로드는 요청 메소드에 따라 달라진다. 
이 명세에 의해 정의된 메서드에 대해, 페이로드의 의도된 의미는 다음과 같이 요약할 수 있다.

GET : 대상 리소스의 representation
HEAD : GET와 동일한 representation이지만 representation 데이터가 없는 경우.
POST : 원하는 동작에 대한 조치 상태 또는 결과 표시
PUT, DELETE : 조치 상태 표시
OPTIONS : 통신 옵션의 표시
TRACE : 엔드서버에서 수신한 요청 메시지의 representation

CONNECT에 대한 응답 외에도 , 200의 응답은 항상 페이로드를 가지고 있지만, 원 서버는 0의 길이의 페이로드 본문을 생성할 수 있다. 
페이로드가 필요 없는 경우, 원서버는 대신 204 (No content)를 전송해야 한다. CONNECT의 경우, 성공적인 결과가 200 응답 헤더 부문 직후에 시작되는 
터널이기 때문에 페이로드가 허용되지 않는다.

메서드 정의나 명시적 캐시 제어에 의해 달리 명시되지 않는 한 기본적으로 200 응답은 캐시할 수 있다.

### 6.3.2. 201 Created
201 (Created) 상태 코드는 요청이 이행되어 하나 이상의 새로운 리소스가 생성되었음을 나타낸다. 
요청에 의해 생성된 기본 리소스는 응답의 Location 헤더 필드를 통해 식별하며, 수신된 Location 필드가 없는 경우 유효한 요청 URI를 통해 식별된다.

201 응답 페이로드에는 일반적으로 생성된 리소스가 설명되고 링크된다.

### 6.3.3. 202 Accepted
202 (Accepted) 상태 코드는 요청이 접수되었으나 처리가 완료되지 않았음을 표시한다. 
(요청을 받았지만 아직 수행되지 않은 상태. 요청을 일괄적으로 받고 나중에 처리할 때 이 상태코드 반환하면 될듯)
요청은 실제로 처리될 때 허용되지 않을 수 있기 때문에 결국 실행될 수도 있고 실행되지 않을 수도 있다. 
HTTP에는 비동기 작업에서 상태 코드를 재전송할 수 있는 기능이 없다.

202 응답은 의도적으로 모호하다. 그 목적은 프로세스가 완료될 때까지 사용자 에이전트의 서버 연결이 지속되지 않아도 되며 
서버가 다른 프로세스(아마도 하루에 한번만 실행되는 배치 지향 프로세스)에 대한 요청을 수락하도록 허용하는 것입니다.
이 응답과 함께 전송되는 representation은 요청의 현재 상태를 기술해야 하며, 
사용자에게 요청 이행 시기에 대한 추정치를 제공할 수 있는 상태 감시를 나타내거나 포함해야 한다.

### 6.3.4. 203 Non-Authoritative Information
203 (Non-Authoritative Inforamtion) 상태 코드는 요청이 성공적이었으나, 
변환 프록시에 의해 원서버의 200(OK) 응답으로 부터 포함된 페이로드가 수정되었음을 나타낸다. 
이 상태 코드는 변환이 적용되면 해당 정보가 컨텐츠에 대한 이후 결정에 영향을 미칠 수 있으므로 프록시가 수신자에게 통지할 수 있다. 
예를 들어 컨텐츠에 대한 향후 캐시 유효성 검사 요청은 동일한 요청 경로(동일한 프록시를 통해)를 통해서만 적용할 수 있다.

203 응답은 어떤 상태 코드로든 응답에 적용할 수 있다는 장점이 있는 214 Transformation Apply의 Warning 코드와 유사하다.

메서드 정의나 명시적 캐시 제어에 의해 달리 명시되지 않는 한 기본적으로 203 응답은 캐시할 수 있다.

### 6.3.5. 204 No Content
204 (No Content) 상태 코드는 서버가 요청을 성공적으로 이행 했으며 응답 페이로드 본문으로 보낼 추가 컨텐츠가 없음을 나타낸다. 
응답 헤더 필드의 메타 데이터는 요청된 작업이 적용된 후 대상 리소스와 선택된 representation을 가리킨다.

예를 들어, PUT 요청에 대한 응답으로 204 상태 코드가 수신되고 응답에 Etag 헤더 필드가 포함된 경우 PUT은 성공했으며 
ETage field-value는 대상 리소스의 새로운 representation을 위한 entitiy-tage가 포함되어 있다.

204 응답은 서버가 대상 리소스에 작업이 성공적으로 적용되었음을 나타내는 동시에 사용자가 에이전트가 현재 "document view"(있는 경우)에서 벗어날 필요가 없음을 암시한다. 
서버는 사용자 에이전트가 자신의 인터페이스와 일치하여 그 사용자에게 성공에 대한 어떤 표시를 제공할 것으로 가정하고, 
그 활성 representation에 대한 응답으로 새로운 메타 데이터나 업데이트된 메타 데이터를 적용한다.

예를 들어, 204 상태 코드는 일반적으로 "save" 작업에 해당하는 문서 편집 인터페이스와 함께 사용되어, 저장 중인 문서가 사용자가 편집할 수 있도록 유지 된다. 
또한 분산 버전 제어 시스템 내에서 처럼 자동화된 데이터 전송이 보편화될 것으로 예상하는 인터페이스에서도 자주 사용된다.

204 응답은 메시지 본문을 포함할 수 없기 때문에 헤더 필드 뒤에 있는 첫번 째 빈 줄에 의해 종료 된다.

메서드 정의나 명시적 캐시 제어에 의해 달리 명시되지 않는 한 기본적으로 204 응답은 캐시할 수 있다.

### 6.3.6. 205 Reset Content
205 (Reset Content) 상태 코드는 서버가 요청을 이행했음을 나타내며, 
사용자 에이전트가 요청을 보낸 "document view"를 리셋하라고 알린다.

이 응답은 사용자가 데이터 엔트리(폼, 노트패드, 캔버스 등)를 지원하는 콘텐츠를 수신하고, 그 공간에서 데이터를 입력하거나 조작하고, 
입력된 데이터를 요청으로 제출하게 한 후, 다음 입력에 대해 데이터 입력 메니즘을 재설정하여 사용자가 다른 입력 작업을 쉽게 시작할 수 있도록 하는 
공통 데이터 입력 사용 사례를 지원하기 위한 것이다.

205 상태 코드는 추가 콘텐츠가 제공되지 않음을 의미하므로 서버는 205 응답에서 페이로드를 생성해서는 안 된다.
즉, 서버는 205 응답에 대해 다음 중 하나를 수행해야 한다.
* 값이 0인 Content-Length 헤더 필드를 포함하여 응답의 길이가 0인 본문을 나타내라
* 청크 값을 가진 Transfer-Encoding 헤더 필드와 0 길이의 단일 청크로 구성된 메시지 본문을 포함하여 응답에 대한 
  0 길이의 페이로드로 나타내라 (청크란 한 덩어리로 데이터를 보내지 않고 나눠서 보내는 것)
* 헤더 부분을 종료하는 빈줄을 보낸 후 즉시 커넥션을 닫아라.

### 6.4. Redirection 3xx
상태 코드 3xx(Redirection) 등급은 요청을 이행하기 위해 사용자 에이전트의 추가 조치가 필요하다는 것을 나타낸다.
Location 헤더 필드가 제공되는 경우, 사용자 에이전트는 특정 상태 코드를 이해하지 못하더라도 Location 필드 값이 참조하는 URI로 요청을 자동으로 
리다이렉트 할 수 있다. 사용자가 안전하지 않은 요청을 리다이렉트 하기를 원하지 않을 수 있으므로, 안전하지 않은 메소드를 주의하여 자동 리다이렉트를 수행 해야 한다.

리다이렉트의 유형
1. 상태 코드 301(Moved Permanently), 302 (Found), 307(Temporary Redirect)과 같이 Location 필드에서 제공하는 대로 다른 URI에서 리소스를 사요할 수 있음을 나타내는 리다이렉트.
2. 300 (Multiple Choices) 상태 코드에서와 같이 각각 원래의 요청 대상을 나타낼 수 있는 매칭 리소스의 선택을 제공하는 리다이렉트.
3. 303 (See Other) 상태 코드에서와 같이 요청에 대한 간접적인 응답을 나타낼 수 있는 위치 필드로 실벽된 다른 리소스로의 리다이렉트
4. 304 (Not Modified) 상태코드에서와 같이 이전에 캐시된 결과로 리다이렉트

참고 : HTTP/1.0에서는 첫 번째 리디렉션 유형에 대한 상태 코드 301, 302가 정의 되었다. 초기 사용자 에이전트는 리다이렉트 대상에 
적용된 메서드가 원래 요청과 같을 것인지 아니면 GET로 다시 작성될 것인지에 대해 분할 했다. HTTP 는 원래 301과 302에 대한 이전의 의미론을 정의하고 
303을 후자 의미론과 일치하도록 정의 했지만, 지배적인 관행은 301과 302에 대해서도 점차적으로 후자의 의미론에 융합되었다. 
HTTP/1.1의 첫 번째 개정은 307을 추가하여 다양한 관행에 영향을 받지 않고 이전의 의미론을 표시 하였다. 
10년이 지난 후에도 대부분의 사용자 에이전트는 여전히 301과 302에 대해 메서드 재작성을 한다. 
따라서 이 규격은 원래 요청이 POST 일 때 그러한 행동을 준수하도록 한다.
(301과 302는 요청 메서드와 달리 GET으로 다시 리다이렉트 하도록 의미를 가지고 307은 요청 메서드와 같은 메서드로 리다이렉트 하도록 의미를 가진다.)

클라이언트는 주기적 리디렉션(리디렉션 루프)을 감지하고 개입해야 한다.

참고 : 이 명세의 이전 버전에서는 최대 5개의 리디렉션을 권장했다. 컨텐츠 개발자들은 일부 클라이언트가 그러한 고정된 제한을 구현할 수 있다는것을 알아야 한다.

### 6.4.1. 300 Multiple Choices
300 (Multiple Choices) 상태 코드는 대상 리소스가 각각 더 구체적인 식별자를 가진 둘 이상의 representation을 가지고 있음을 나타내며, 
사용자(또는 사용자 에이전트)가 해당 식별자 중 하나 이상으로 요청을 리디렉션하여 선호하는 representation을 선택할 수 있도록 대안에 대한 ㅈ어보가 제공되고 있다.
즉, 서버는 사용자 에이전트가 자신의 요구에 가장 적합한 표현을 선택하기 위해 reactive negotiation에 임하기를 바란다.

서버에 기본 선택사항이 있는 경우, 서버는 기본 선택사항의 URI참조가 포함된 Location 헤더필드를 생성해야 한다. 
사용자 에이전트는 자동 리디렉션에 Location 필드 값을 사용할 수 있다.

메서드 정의나 명시적 캐시 제어에 의해 달리 명시되지 않는 한 기본적으로 300 응답은 캐시할 수 있다.

참고 : 300 상태 코드에 대한 원래 제안에서는 URI 헤더 필드를 200, 300 및 406 응답에 사용할 수 있고 
HEAD 메서드에 대한 응답으로 이전할 수 있는 대체 표현 목록을 제공하는 것으로 정의 했다. 
그러나 배치 부족과 구문에 대한 의견 불일치로 인해 URI와 Alternates(이 후의 제안)가 이 명세에서 모두 삭제 되었다.
닭과 달걀의 문제지만 각 "대안"의 관계를 가진 Link 헤더필드 집합을 사용하여 목록을 전달할 수 있다.
(이 구문은 무슨 의미인지 잘 모르겠네요)

### 6.4.2. 301 Moved Permanently
301 (Moven Permanently) 상태 코드는 대상 리소스에 새 영구 URI가 할당 되었으며 이 리소스에 대한 향후 참조는 
동봉된 URI 중 하나를 사용해야 함을 나타낸다. 링크 편집 기능을 가진 클라이언트는 가능한 경우, 
서버가 보낸 하나 이상의 새로운 참조에 유효한 요청 URI에 대한 참조로 자동으로 다시 연결 해야 한다.

서버는 응답을 Location 헤더 필드에 새 영구 URI에 대한 기본 URI 참조를 포함하여 생성해야 한다. 
사용자 에이전트는 자동 리디렉션에 Location 필드 값을 사용할 수 있다.
서버의 응답 페이로드에는 일반적으로 새 URI에 대한 하이퍼링크가 포함된 짧은 하이퍼텍스트 노트가 포함되어 있다.

참고 : 역사적 이유로, 사용자 에이전트는 후속 요청에 대한 요청 메서드를 POST에서 GET로 변경할 수 있다. 
이 동작이 원치 않으면 307 상태 코드를 대신 사용할 수 있다.

메서드 정의나 명시적 캐시 제어에 의해 달리 명시되지 않는 한 기본적으로 301 응답은 캐시할 수 있다.

### 6.4.3. 302 Found
302 (Found) 상태 코드는 대상 리소스가 일시적으로 다른 URI 아래에 있음을 나타낸다. 
리디렉션은 때때로 변경될 수 있으므로 클라이언트는 향후 요청에 대해 유효한 요청 URI를 계속 사용해야 한다.

서버는 다른 URI에 대한 URI 참조가 포함된 응답에 Location 헤더 필드를 생성해야 한다. 
서버의 응답 페이로드에는 일반적으로 새 URI에 대한 하이퍼링크가 포함된 짧은 하이퍼텍스트 노트가 포함되어 있다.

참고 : 역사적 이유로, 사용자 에이전트는 후속 요청에 대한 요청 메서드를 POST에서 GET로 변경할 수 있다. 
이 동작이 원치 않으면 307 상태 코드를 대신 사용할 수 있다.

### 6.4.4. 303 See Other
303 (See Other) 상태 코드는 원래 요청에 대한 간접적인 응답을 제공하기 위해 서버가 사용자 에이전트를 
Location 헤더 필드의 URI가 표시한 다른 리소스로 리다이렉트 하고 있음을 나타낸다.
(클라이언트가 요청한 리소스를 다른 URI에서 GET 요청을 통해 얻어야 할 때, 서버가 클라이언트로 리디렉션 하라고 보내는 응답)
사용자 에이전트는 또한 리다이렉트 될 수 있는 URI(HTTP를 사용하는 경우 GET 또는 HEAD)를 대상으로 검색 요청을 수행하여 
최종 결과를 원래 요청에 대한 응답으로 제시할 수 있다. Location 헤더필드의 새 URI는 유효한 요청 URI와 동등한 것으로 간주 되지 않는다.

이 상태 코드는 모든 HTTP 메서드에 적용된다. POST 동작의 출력이 사용자 에이전트를 선택한 리소스로 리디렉션하는 데 주로 사용됩니다. 
POST 응답에 해당하는 정보를 원래 요청과는 독립적으로 별도의 식별, 북마크 및 캐시할 수 있는 형식으로 제공하기 때문이다.

GET 요청에 대한 303 응답은 원서버가 HTTP를 통해 서버에 의해 전송될 수 있는 대상 리소스의 representation을 가지고 있지 않음을 나타낸다. 
그러나 Location 필드 값은 다른 리소스에 대한 검색 요청을 하면 원래 대상 리소스를 나타내지 않고 수신자에게 유요
용한 representation이 될 수 있으므로 대상 리소스를 설명하는 리소스를 말한다. 
(예를 들면 업로드 동작 후 확인창, 로딩창으로 리디렉션, (업로드 중이니 리소스에 대한 응답을 못함) 내가 리소스를 가지고 있지 않고 리소스에 대한 설명을 하는곳으로 리디렉션)
나타낼 수 있는것, 적절한 표현, 유용한 설명의 질문에 대한 답변은 HTTP의 범위를 벗어난다는 것을 참고한다.

HEAD 요청에 대한 응답을 제외하고, 303 응답의 표현에는 Location 헤더 필드에 제공된 동일한 URI 참조에 대한 하이퍼링크가 있는 짧은 
하이퍼텍스트 노트가 포함되어야 한다.

### 6.4.5 Not Modified
캐시를 목적으로 사용된다. 클라이언트에게 응답이 수정되지 않음을 알려주며, 클라이언트는 계속해서 응답의 캐시된 버전을 사용할 수 있다.

### 6.4.6. 305 Use Proxy
305 (Use Proxy) 상태 코드는 이 명세의 이전 버전에서 정의되었으며 현재 더 이상 사용되지 않는다.

### 6.4.7. 306 Unused
306 (Unused) 상태 코드는 이 명세의 이전 버전에서 정의되었으며 현재 더 이상 사용되지 않는다.

### 6.4.8. 307 Temporary Redirect
307(Temporary Redirect) 상태 코드는 대상 리소스가 일시적으로 다른 URI에 상주하고 있다는 것을 나타내며, 
사용자 에이전트는 해당 URI로 자동 리디렉션을 수행할 경우 요청 메서드를 변경 하면 안 된다. 리디렉션은 시간이 지남에 따라 
변경될 수 있으므로 클라이언트는 향후에 원래의 유효한 요청 URI를 게속 사용해야 한다.

서버는 다른 URI에 대한 URI 참조가 포함된 응답에 Location 헤더 필드를 생성해야 한다. 
사용자 에이전트는 자동 리디렉션에 Location 필드 값을 사용할 수 있다. 서버의 응답 페이로드에는 일반적으로 다른 URI에 대한 
하이퍼링크가 포함된 짧은 하이퍼 텍스트 노트가 포함되어 있다.

참고 : 이 상태 코드는 요청 메서드를 POST에서 GET으로 변경할 수 없다는 점을 제외하고 302(Found)와 유사하다.
이 명세는 301(Moved Permanent)과는 동등하지 않다 (이 목적을 위한 상태 코드 308 (Permanent Redirect))

### 6.4.9. 308 Permanent Redirect
308 (Permanent Redirect) 상태 코드는 301과 동등하며 요청 메소드와 리디렉션 메소드가 동일 해야한다.

## 6.5. Client Error 4xx
상태 코드의 4xx(Client Error) 등급은 클라이언트가 오류가 발생한 것으로 보인다는 것을 나타낸다. HEAD 요청에 응답하는 경우를 제외 하고, 
서버는 오류 상황 및 일시적 또는 영구적 조건의 설명이 포함된 표현을 보내야 한다. 이 상태 코드는 모든 요청 메서드에 적용할 수 있다.
사용자 에이전트는 사용자에게 포함된 표현을 표시해야 한다.

### 6.5.1. 400 Bad Request
400 (Bad Request) 상태 코드는 클라이언트의 오류로 인신되는 것(e.g 잘못된 요청 구문, 잘못된 요청 메시지 프레임, 또는 기만적인 요청 라우팅(기만적인게 뭐지?))로 
인해 서버가 요청을 처리할 수 없거나 처리할 수 없음을 나타낸다.

### 6.5.2. 401 Unauthorized
HTTP 표준에서는 "미승인"을 명확히 하고 있지만, 의미상 이 응답은 "비인증"을 의미한다. 클라이언트는 요청한 응답을 받기 위해서 자신을 인증해야 한다.
(미승인은 서버가 승인하지 않은것이고 비인증은 클라이언트가 인증을 거치지 않은 의미라고 생각하면 되나?)
WWW-Authenticate 헤더와 함께 정송되며, 이 헤더는 올바르게 인증하는 방법에 대한 정보를 포함하고 있다.

### 6.5.3. 402 Payment Required 
402 상태 코드는 향후 사용을 위해 예약되어있다. 아직은 사용되지 않는다.

### 6.5.4. 403 Forbidden
403 (Forbidden) 상태 코드는 서버가 요청을 이해했지만 승인을 거부함을 나타낸다. 요청이 금지된 이유를 공개하고자 하는 서버는, 
응답 페이로드에서 그 이유를 설명할 수 있다.

요청에 인증 자격 증명이 제공된 경우, 서버는 인증 자격 증명이 엑세스 권한을 부여하기에 불충분하다고 간주한다. 
클라이언트는 동일한 자격증명으로 요청을 자동으로 반복해서는 안 된다. 클라이언트는 새 자격 증명 또는 다른 자격 증명으로 요청을 반복할 수 있다. 
그러나 인증 정보와 무관한 이유로 요청이 금지될 수 있다.
(403은 서버는 클라이언트를 승인하지 않는 거절을 표현하는 응답이다. 401과 다른점은 서버가 클라이언트가 누구인지 알고 있다.)

금지된 대상 리소스의 현재 존재를 "hide" 원하는 원 서버는 대신 상태 코드 404 (Not Found)로 응답할 수 있다.

### 6.5.5. 404 Not Found
404 (Not Found) 상태 코드는 원서버가 대상 리소스에 대한 현재 representation을 찾지 못했거나 존재하는 것을 공개하지 않으려는 것을 나타낸다.
404 상태 코드는 이러한 representation 부족이 일시적인지 영구적인지를 나타내지 않는다. 
410(Gone) 상태코드는 원서버가 어떤 구성 가능한 수단을 통해 조건이 영구적일 가능성이 있다는 것을 알고 있는 경우 404보다 선호 된다.

메서드 정의나 명시적 캐시 제어에 의해 달리 명시되지 않는 한 기본적으로 404 응답은 캐시할 수 있다.

### 6.5.6. 405 Method Not Allowed
405(Method Not Allowed) 상태 코드는 request-line에 수신된 메서드가 원서버에 의해 알려져 있지만 대상 리소스에는 지원 되지 않음을 나타낸다. 
원서버는 대상 리소스의 현재 지원되는 메서드 목록을 포함하는 405 응답에 Allow 헤더 필드를 생성해야 한다.

메서드 정의나 명시적 캐시 제어에 의해 달리 명시되지 않는 한 기본적으로 405 응답은 캐시할 수 있다.

### 6.5.7. 406 Not Acceptable
406 (Not Acceptable) 상태 코드는 요청에 수신된 proactive negotiation 헤더 필드에 따라 대상 리소스가 사용자 에이전트가 
수용할 수 있는 현재 representation을 가지고 있지 않으며, 서버가 기본 representation을 제공하지 않음을 나타낸다.

서버는 사용자 또는 사용자 에이전트가 가장 적합한 것을 선택할 수 있는 사용 가능한 representation 특성 및 해당 리소스 식별자 목록을 
포함하는 페이로드를 생성해야 한다. 사용자 에이전트는 해당 목록에서 가장 적절한 선택을 자동으로 선택할 수 있다. 
그러나 이 명세는 그러한 자동 선택에 대한 표준을 정의하지 않는다.

### 6.5.8 407 Proxy Authentication Required
이것은 401과 비슷하지만 프록시에 의해 완료된 인증이 필요하다.
HTTP 407 Proxy Authentication Required 클라이언트 오류 상태 응답 코드는 요청 된 리소스에 
액세스 할 수있는 서버와 브라우저 사이에 있는 프록시 서버에 대한 유효한 인증 자격 증명이 없기 때문에 
요청이 적용되지 않았음을 나타낸다

이 상태는 Proxy-Authenticate올바른 인증 방법에 대한 정보가 포함 된 헤더 와 함께 전송 됩니다.

### 6.5.9. 408 Request Timeout
408 (Request Timeout) 상태 코드는 서버가 대기할 준비가 된 시간 내에 완전한 요청 메시지를 수신하지 않았음을 나타낸다. 
408은 서버가 계속 대기하기 보다는 커넥션을 종료하기로 결정하였음을 의미하므로, 서버는 응답으로 "close" 커넥션 옵션을 보내야 한다. 
클라이언트가 미결 요청을 가지고 있는 경우, 클라이언ㄴ트는 새로운 커넥션에서 해당 요청을 반복할 수 있다. 

### 6.5.10. 409 Conflict
409 (Conflict) 상태 코드는 대상 리소스의 현재 상태와 충돌하여 요청을 완료할 수 없음을 나타낸다. 
이 코드는 사용자가 충돌을 해결하고 요청을 다시 제출할 수 있는 상황에서 사용된다. 
서버는 사용자가 충돌의 원인을 인식하기에 충분한 정보를 포함하는 페이로드를 생성해야 한다.

PUT 요청에 대응하여 충돌이 발생할 가능성이 가장 높다. 예를 들어 버전 지정이 사용 중이고 PUT에 있는 representation에 이전(서드 파티) 요청에 의해 
수행된 리소스와 충돌하는 리소스의 변경 사항이 포함된 경우, 원서버는 요청을 완료할 수 없음을 나타내기 위해 409 응답을 사용할 수 있다.
이 경우, 응답 representation에는 개정 이력에 기초한 차이를 병합하는데 유용한 정보가 포함될 수 있다.
(구버전에서 PUT으로 수정할 수 있는 리소스(ex, {'name, age'})와 최신버전에서 PUT으로 수정할 수 있는 리소스(ex, {'name'})이 다를때, 
구버전의 리소스로 최신버전 리소스의 PUT을 동작하면 ('age'에 대한 내용으로 인해 충돌) 409를 반환하는 상황인듯.)

### 6.5.11. 410 Gone
410 (Gone) 상태 코드는 대상 리소스에 대한 엑세스를 원 서버에서 더 이상 사용할 수 없으며 
이 조건이 영구적일 가능성이 있음을 나타낸다. 원본 서버가 조건이 영구적인지 여부를 알 수 없거나 확인할 수 있는 기능이 없는 경우, 
대신에 상태 코드 404 (Not Found)를 사용해야 한다.

410 응답은 주로 수신자에게 리소스를 의도적으로 사용할 수 없으며 서버 소유자가 해당 리소스에 대한 원격 링크를 제거하기를 원한다는 것을 통지함으로써 
웹 유지보수의 작업을 지원하기 위한 것이다. 이러한 이벤트는 제한된 시간의 홍보 서비스 및 원서버의 사이트와 더 이상 연관되지 않는 개인에 속하는 리소스에 대해 일반 적이다. 
영구적으로 사용할 수 없는 모든 리소스를 "gone"로 표시하거나 일정 시간 동안 표시를 유지할 필요는 없다.
이것은 서버 소유자의 재량이다.

메서드 정의나 명시적 캐시 제어에 의해 달리 명시되지 않는 한 기본적으로 410 응답은 캐시할 수 있다.

### 6.5.12. 411 Length Required
411 (Length Required) 상태 코드는 Content-Length 포함되지 않은 요청을 거부함을 나타낸다. 
클라이언트 요청 메시지에 메시지 본문의 길이가 포함된 유효한 Content-Length 헤더 필드를 추가하는 경우 요청을 반복할 수 있다.

### 6.5.13. 412 Precondition Failed
클라이언트의 헤더에 있는 전제조건은 서버의 전제조건에 적절하지 않습니다.

### 6.5.13. 413 Payload Too Large
413 (Payload Too Large) 상태 코드는 요청 페이로드가 서버가 처리할 의향 또는 처리할 수 있는 능력보다 크기 때문에, 
서버가 요청을 거부함을 나타낸다. 서버는 클라이언트가 요청을 계속하지 못하도록 커넥션을 닫는다.

일시적 조건인 경우 서버는 일시적임을 나타내기 위해 Retry-After 헤더 필드를 생성하여 클라이언트가 다시 시도할 수 있는 시간 이후를 표시 해야 한다.

### 6.5.14. 414 URI Too Long
414 (URI Too Long) 상태 코드는 request-target이 서버가 해석하려는 것보다 길기 때문에 서버가 요청을 거부함을 나타낸다. 
이 조건은 클라이언트가 긴 쿼리 정보가 있는 GET 요청으로 POST 요청을 부적절하게 변환 했을 때, 
클라이언트가 "black hole" 리디렉션(예를 들어 자체 접미사를 가리키는 리다이렉트 URI 접두사)로 내려갔을 때, 
또는 서버가 잠재적인 보안 구멍을 이용하려고 시도하는 클라이언트의 공격을 받을 때에만 발생할 수 있다.

메서드 정의나 명시적 캐시 제어에 의해 달리 명시되지 않는 한 기본적으로 414 응답은 캐시할 수 있다.

### 6.5.15. 415 Unsupported Media Type
415 (Unsupported Media Type) 상태 코드는 대상 리소스에 대해 이 메서드로 지원하지 않는 형식의 페이로드가 있기 때문에, 
원서버가 요청을 거부함을 나타낸다. 형식 문제는 요청에 표현된 Content-Type 또는 Content-Encoding 또는 데이터를 직접 검사한 결과일 수 있다.

### 6.5.16. 417 Expectation Failed
417 (Expectation Failed) 상태 코드는 요청의 Expect 헤더 필드에 주어진 기대치를 인바운드 서버 중 하나 이상이 
충족할 수 없음을 나타낸다.

### 6.5.17. 426 Upgrade Required
426(Upgrade Required) 상태 코드는 서버가 현재 프로토콜을 사용하여 요청을 수행하기를 거부하지만 클라이언트가 다른 프로토콜로 
업그레이드한 후 요청을 수행할 의사가 있음을 나타낸다. 서버는 반드시 필요한 프로토콜을 나타내기 위해 426 응답으로 Upgrage 헤더 필드를 전송 해야 한다.

예를 들어 이 서비스는 HTTP/3.0 프로토콜 사용을 필요로 한다.
```HTTP
HTTP/1.1 426 Upgrade Required
Upgrade: HTTP/3.0
Connection: Upgrade 
Content-Length: 53 
Content-Type: text/plain
```

## 6.6. Server Error 5xx
상태 코드의 5xx (Server Error) 등급은 서버가 요청된 메서드를 잘못했거나, 수행할 수 없음을 나타낸다.
HEAD 요청에 응답하는 경우를 제외하고, 서버는 오류 상황에 대한 설명과 일시적 조건인지 영구적인 조건인지를 포함하는 
representation을 보내야 한다. 사용자 에이전트는 사용자에게 포함된 모든 representation을 표시해야 한다. 
이 응답 코드는 모든 요청 메서드에 적용 된다.

### 6.6.1. 500 Internal Server Error
500 (Internal Server Error) 상태 코드는 서버가 요청을 이행하지 못하게 하는 예기치 않은 조건을 만났음을 나타낸다.

### 6.6.2. 501 Not Implemented
501 (Not Implemented) 상태 코드는 서버가 요청을 이행하는데 필요한 기능을 지원하지 않음을 나타낸다. 
이것은 서버가 요청 메서드를 인식하지 못하고 어떤 리소스에도 그것을 지원할 수 없을 때 적절한 대응이다.

메서드 정의나 명시적 캐시 제어에 의해 달리 명시되지 않는 한 기본적으로 501 응답은 캐시할 수 있다.

### 6.6.3. 502 Bad Gateway
502 (Bad Gateway) 상태 코드는 서버가 게이트웨이 또는 프록시 역할을 하는 동안 요청을 이행하는 중 
엑세스한 인바운드 서버로 부터 잘못된 응답을 수신했음을 나타낸다.

### 6.6.4. 503 Service Unavailable
503 (Service Unavailable) 상태 코드는 서버가 현재 일시적인 과부하 또는 예정된 유지보수로 인해 요청을 처리할 수 없음을 나타내며, 
어느 정도 지연된 후에 완화될 가능성이 높다. 서버는 Retry-After 헤더필드를 보내 클라이언트가 요청을 재시도하기 전에 대기할 시간을 제안할 수 있다.

참고: 503 상태 코드가 존재한다고 해서 서버가 과부하가 걸렸을 때 이를 사용해야 하는 것은 아니다. 일부 서버는 단순히 커넥션을 거부할 수 있다.

### 6.6.5. 504 Gateway Timeout
504 (Gateway Timeout) 상태 코드는 서버가 게이트웨이 또는 프록시 역할을 하는 동안 요청을 완료하기 위해 엑세스해야 하는 업스트림 서버로 부터 
적시에 응답을 받지 못했음(지정한 시간 제한을 초과함)을 나타낸다.
[추가 설명](https://blog.lael.be/post/9251)

### 6.6.6. 505 HTTP Version Not Supported
505 (HTTP Version Not Supported) 상태 코드는 서버가 요청 메시지에 사용된 HTTP의 주요 버전을 지원하지 않거나 지원을 거부함을 나타낸다. 
서버는 오류 메시지를 제외하고 클라이언트와 동일한 주요 버전을 사용하여 요청을 완료할 수 없거나 요청을 완료할 의사가 없음을 나타내고 있다. 
서버는 해당 버전이 지원되지 않는 이유와 해당 서버에서 지원하는 다른 프로토콜을 설명하는 505 응답에 대한 표현을 생성해야 한다.
