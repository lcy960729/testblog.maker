---
title: 'HTTP 1.1 Reference - Semantics and Content (최종 수정 날짜 : 2021-02-04)'
tags: Web HTTP
categories: http
author: CY
key: HTTP 1.1 Reference - Semantics and Content
---
# 참고 자료
* 이병록님이 번역하신 HTTP 1.1 Semantics and Content 번역본  
  [https://roka88.dev/106](https://roka88.dev/106)
* MDN Web Docs  
  [https://developer.mozilla.org/ko/docs/Web/HTTP](https://developer.mozilla.org/ko/docs/Web/HTTP)

이외 자료는 내용 중 링크 추가
##### 틀린 내용, 부족한 내용 꼭 지적 부탁드립니다!

# HTTP 1.1 Reference - Semantics and Content
# 1. 간단한 설명

HTTP 메시지는 요청 또는 응답을 나타낸다.

서버는 클라이언트에게 요청을 받고 수신된 메시지를 해석 후 허락된 클라이언트에게 하나 이상의 응답 메시지를 반환한다. (서버가 먼저 클라이언트에게 요청할 수 는 없다. 웹소켓 제외)

클라이언트는 특정한 의도를 나타내는 요청 메시지를 서버에게 보낸후 수신된 응답을 확인하여 (상태 코드 확인) 의도대로 수행되었는지 확인 후 응답 메세지를 결과를 확인한다(Response Body의 내용을 확인). 

# 2. Resources

HTTP의 요청 대상을 resource라고 부른다. HTTP는 리소스와 상호 작용을 할 수 있는 인터페이스(아마 HTTP가 요청과 응답하는 구조의 내용일듯)를 정의하고 리소스는 URI로 식별된다. 

HTTP의 설계 목표 중 하나는 요청의 의미론에서 리소스 식별을 분리 하는것이다. 예를 들어 URI는 리소스를 처리할 행위 모두를 나타내는것이 아니라 URI는 리소스를 식별하는 역할만 하는것이고 메소드나 다른 헤더를 통해 어떠한 역할을 수행해야하는지를 식별하도록 하는것이다. 그렇기 때문에 URI는 겹칠 수 있지만 메소드가 다를 경우 다른 동작을 하게 되는 것이다. 

```
POST http://localhost/get_user
```

위에 내용을 보충 해보면 조금 극단적인 예라 생각 하지만 위 요청처럼 URI에 리소스에 대한 행위를 담는것이 아니라 아래처럼 메소드나 부가적인 헤더를 이용하여 행위를 나타내는것이 HTTP 설계 목표이다. (라고 이해했다 나는...)

```
GET http://localhost/user
POST http://localhost/user
```

# 3. Representations

참고자료 ([https://blog.npcode.com/2017/04/03/rest의-representation이란-무엇인가/](https://blog.npcode.com/2017/04/03/rest%EC%9D%98-representation%EC%9D%B4%EB%9E%80-%EB%AC%B4%EC%97%87%EC%9D%B8%EA%B0%80/))

리소스는 무엇이든 될 수 있으며 해당 행위들은 여러가지가 될 수 있다. 이러한 점에서 우리는 서버에 원하는 요청(상태)를 나타내기위한 추상화가 필요한데 그 추상화를 우리는 REST라고 부른다. 

HTTP의 목적 상 Representations는 주어진 리소스의 상태(과거, 현재, 또는 원하는 상태)를 프로토콜을 통해 쉽게 전달할 수 있는 형식으로 반영하기 위한 정보이다. 표현 데이터는 데이터 스트림으로 구성된다. 

Representations의 예를 들어보면 아래의 요청에 대해 아래와 같은 응답들을 볼 수 있다. 이러한 응답들을 representation이라고 한다. 첫번째는 사용자1이라는 리소스를 한글 사용자들을 위한 상태로 반환 한것이다. 두번째는 영문 사용자, 세번째는 html로, 네번째는 json의 형태로 반환 한것이다. 

```
GET http://localhost/user
Content-Type: text/plain
Content-Language: ko

응답 : 사용자1
```

```
GET http://localhost/user
Content-Type: text/plain
Content-Language: en

응답 : user1
```

```
GET http://localhost/user
Content-Type: text/html

응답 : <html><body>사용자1</body></html>
```

```
GET http://localhost/user
Content-Type: application/json
응답 : {'name' : 'user1'}
```

## 3.1. Representation Metadata

Representation 헤더 필드는 Representation에 대한 메타 데이터를 제공한다. 메시지에 페이로드 본문이 포함된 경우 Representation header 필드에는 본문에 포함된 Representation 데이터를 해석하는 방법을 나타낸다.

### 3.1.1. Processing Representation Data

#### 3.1.1.1. Media Type

HTTP는 유연한 데이터 입력과 반환을 위해 Content-Type과 Accept헤더 필드를 사용한다. 미디어 타입은 데이터 형식과 다양한 처리 모델을 정의 할 수 있다. 결론적으로 미디어 타입은 데이터가 어떤 모양으로 수신 됐으며 어떤 모양으로 반환을 해야한다는 정보를 명시해주는 것이다.

media-type = type / subtype 형태를 이룬다. 다들 알다 시피 text/html이나 application/json등 이러한 형태이다. type과 subtype는 대소문자를 구분하지 않는다. 이후 type / subtype; parameter=token과 같은 형태가 있으며 이때 parameter의 값인 token은 parameter에 따라 대소문자를 구분하거나 구분하지 않을 수 있다. 또한 = 앞과 뒤에는 공백을 허용하지 않는다. 예를 들면 text/html;charset=utf8과 같은 형태이다. 뒤에 붙는 매개 변수의 존재나 부재는 미디어 타입 레지스트리 내의 정의에 따라 미디어 타입 처리에 중요할 수 있다. 

#### 3.1.1.2. Charset

Charset는 HTTP의 문자 인코딩 방식을 표현할때 사용한다. Charset는 대소문자를 구분하지 않는다.

#### 3.1.1.3. Canonicalization and Text Defaults (정규화 및 텍스트 기본 값)

text 타입의 미디어 하위 타입이 텍스트 줄바꿈을 할땐 CRLF를 사용하여야 한다. HTTP는 이러한 줄 바꿈이 전체 표현에 일관 될 때, 줄 바꿈을 나태는것을 CR 또는 LF만으로 텍스트를 전송할 수 있다. 줄바꿈과 관련됨 내용은 'text' 미디어 타입이 할당 된 Representation 내 텍스트에만 적용 된다.

#### 3.1.1.4. Multipart Types ( 참고 내용 : [https://qssdev.tistory.com/47](https://qssdev.tistory.com/47))

단일 메시지 본문 내에서 하나 이상의 표현을 캡슐화한 여려 "multipart" 타입을 제공한다. 대부분 데이터 전송 'multipart/form-data' 타입을 사용하는 경우가 많다. 멀티파트 타입은 미디어 타입 값의 일부로 경계 매개변수를 포함한다. 경계 매개 변수를 얘를 들어 보면  아래와 같다.

```html
<form action = "http://localhost/images"
      enctype = "multipart/form-data"
      method = "post">

    <input type="text" name = "imageName"><br>
    <input type="file" name = "image"><br>
    <input type="submit" name = "upload"><br>

</form>
```

위 처럼 폼을 만들어주고 Request 구문을 생성한것이 아래와 같다. 바운더리 변수가 만들어지고 form data의 타입마다 경계를 주어 구문을 분석한다. 메시지 본문은 그 자체로 프로토콜 요소이며 발신자는 본문 부분 사이의 줄 바꿈을 나타내기위해 CRLF만 생성해야한다.

```html
POST /images HTTP/1.1
Host: localhost
Content-Length: 228
Content-Type: multipart/form-data; boundary=----WebKitFormBoundary7MA4YWxkTrZu0gW

----WebKitFormBoundary7MA4YWxkTrZu0gW
Content-Disposition: form-data; name="imageName"

image
----WebKitFormBoundary7MA4YWxkTrZu0gW
Content-Disposition: form-data; name="file"

123412341234
----WebKitFormBoundary7MA4YWxkTrZu0gW
```

#### 3.1.1.5. Content-Type

Content-Type 헤더 필드는 메시지에 포함된 페이로드(메시지 본문(body)이라고 생각)의 미디어 타입을 나타낸다. 미디어 타입은 representations을 디코딩과 인코딩 하는 정보를 제공한다. representation을 발신자가 알 수 없는 경우가 아니라면 Content-Type 헤더 필드를 생성해야 한다. 만약 없는 경우 수신자는  application/optet- stream라는 미디어 타입을 가정하거나 데이터를 검토하여 타입을 결정할 수 있다. 리소스 소유자가 올바른 Content-Type을 제공하도록 구성하지 않으면 클라이언트가 메시지의 본문을 콘텐츠 검사하고 지정된 타입을 재정의 한다. 잘못된 타입으로 정의할 수 있으며 보안 위험(예를 들면 privilege escalation,([https://ko.wikipedia.org/wiki/권한_확대](https://ko.wikipedia.org/wiki/%EA%B6%8C%ED%95%9C_%ED%99%95%EB%8C%80)) 권한 확대라는 보안 위험인데... 잘 모르겠다.)이 생길 수 있다. 구현자는 content sniffing을 방지할 수 있는 수단을 제공해야한다. ([https://en.wikipedia.org/wiki/Content_sniffing](https://en.wikipedia.org/wiki/Content_sniffing))

### 3.1.2. Encoding for Compression or Integrity (압축 또는 무결성을 위한 인코딩)

#### 3.1.2.1. Content Codings
Content Coding은 주로 representations을 압축하거나 다른 용도로 사용할 수 있도록 하기 위해 사용되며 기본적인 미디어 타입의 정체성을 상실하지 않고 정보의 손실 없이 사용된다. representations은 코드화된 형태로 저장되고, 직접 전송되며, 최종 수신자에 의해서만 해독 된다. 모든 컨텐츠 코딩 값은 대소문자를 구분하지 않으며 HTTP Content Coding Registry에 등록해야한다. Accept-Encoding 및 Content-Encoding 헤더 필드에 사용된다. 정의되는 콘텐츠 코딩 값은 Compress Coding, Deflate Coding, Gzip Coding가 있다.

#### 3.1.2.2. Content-Encoding
Content Encoding 헤더 필드는 미디어 타입의 정체성을 잃지 않고 압축될 수 있도록 하기 위해 사용한다. 하나 이상의 인코딩이 representations에 적용된 경우, 인코딩을 적용한 발신자는 해당 인코딩이 적용된 순서대로 Content-Encoding 헤더 필드를 생성해야한다. Content-Encoding 헤더는 Content-Type 헤더에 의해 참조되는 미디어 타입을 얻도록 디코드하는 방법을 클라이언트에게 알려준다. 서버에서는 요청 메시지의 표현에 허용되지 않는 콘텐츠 코딩이 있는 경우 415(Unsupported Media Type)상태 코드를 반환 한다.