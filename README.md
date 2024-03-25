[스프링 웹 MVC]

Web Server: 정적 리소스(클라이언트로부터 요청이 들어왔을 때 요청에 대한 리소스가 이미 만들어져 있어 그대로 응답하는 경우)를 제공한다. (NGINX, APACHE)
WAS(Web Application Server): 웹 서버의 기능 + 프로그램 로직을 수행 (Tomcat, Jetty, Undertow)

정적 데이터만 전달하는 웹 서버는 요청에 따른 동적인 처리가 불가능하고 이를 처리하기 위해 나온 것이 CGI이다.
CGI는 Common Gateway Interface로 웹 서버로부터 동적 데이터를 처리하는 인터페이스

CGI의 단점
1. 리퀘스트가 들어올 때마다 프로세스를 생성
2. 같은 구현체를 사용해도 프로세스마다 CGI 구현체를 새로 생성

** 프로세스: 메모리에 적재된 실행 중인 프로그램 인스턴스
** 쓰레드: 한 프로세스 내의 동작 흐름(메모리를 공유)

프로세스 -> 쓰레드로 개선
CGI 구현체 -> 싱글톤인 servlet 구현체로 변경하여 객체 공유하도록 하여 나온 것이 서블릿

서블릿
@ServletComponentScan: 서블릿을 직접 등록해서 사용할 수 있도록 지원
@WebServlet(name=“서블릿이름”, urlPatterns=“URL매핑”)

: 클라이언트의 요청을 받아 처리하고 그 결과를 반환하는 자바 기반의 동적 웹 페이지 기술, HttpServlet을 상속받아 사용하며 request, response 객체를 지원한다.
서블릿 컨테이너: 서블릿을 지원하는 WAS를 말함. 서블릿 객체를 생성, 초기화, 호출, 종료하는 생명주기를 관리한다. 서블릿 객체는 싱글톤으로 관리, 멀티 쓰레드 처리 지원

싱글톤: 하나의 객체 만을 생성한 후 공유해서 사용하는 것
private static final ClassName 변수 = new ClassName();
싱글톤 구현 시 생성자는 private 접근자로 막는다.

HttpServletRequest
: 서블릿은 개발자가 HTTP 요청 메세지를 편리하게 사용할 수 있도록 파싱 후 객체에 담아서 제공

** 임시 저장소 기능: HTTP 요청이 끝날 때까지 유지되는 기능
- 저장: request.setAttribute(name, value)
- 조회: request.getAttribute(name)

HTTP 요청 데이터
1. GET - 쿼리 파라미터 [검색, 필터, 페이징 등에서 사용]
URL의 쿼리 파라미터에 데이터를 포함해서 전달

// 복수 파라미터 조회
    request.getParametrValues("");
2. POST - HTML FORM [회원 가입, 상품 주문 등에서 사용]
메시지 바디에 쿼리 파라미터 형식으로 전달

//파라미터 조회 메서드는 쿼리 파라미터 조회와 동일
    requset.getParameter("")
3. HTTP message body
HTTP API에서 주로 사용(JSON, TEXT)

// 데이터 조회
    requset.getInputStream() //message body의 내용을 byte로 읽음
// byte code -> String
    StreamUtils.copyToString(bytecode, Charset);

HTTP 응답 데이터
1. 단순 텍스트
write.println("");
2. HTML 응답
HTML로 응답 시 content-type을 text/html로 지정
3. HTTP API - JSON
//객체를 JSON으로 변환하는 Jackson 라이브러리
    objectMapper.writeValueAsString() 


JSP, MVC 패턴
템플릿 엔진
: 서블릿을 통해 동적으로 원하는 HTML을 만들 수 있으나 비효율적이다. -> 템플릿 엔진을 사용하면 HTML 문서에서 필요한 곳만 코드를 적용해서 동적으로 변경할 수 있다.
템플릿 엔진에는 JSP, Tymeleaf, Freemaker, Velocity 등이 있다.

1. JSP: HTML 안에 Java 코드를 넣어 동적 web을 생성하는 도구, 서버 내부에서 서블릿으로 변환
<%@ page contentType="text/html;charset="UTF-8" language="java"%> // JSP 시작
<% ~~ %>  // Java 입력
<%= ~~ %> // Java 츨력

JSP의 경우 forward()를 통해 이동해야 렌더링이 된다.
dispatcher.forward(): 다른 서블릿이나 JSP로 이동할 수 있는 기능

서블릿과 JSP의 한계
서블릿으로 개발할 때는 view를 위한 HTML을 만드는 작업이 자바와 섞여 있어서 복잡했다.
JSP를 사용했을 때는 HTML을 깔끔하게 가져가고 중간에 동적인 부분만 자바로 구현하였지만 JSP 또한 Java 코드, 데이터 조회 리포지토리 등 다양한 로직이 포함되어 있다.
-> 비즈니스 로직은 서블릿과 같은 곳에서 처리하고, JSP는 HTML로 화면을 그리는 것에 초점을 두어야 하기 위해 MVC 패턴이 등장

2. MVC 패턴
- Controller: Http 요청을 받아서 비즈니스 로직이 있는 서비스를 호출 
- Model: 애플리케이션의 정보, 데이터
- View: 사용자에게 제공할 화면
서블릿을 컨트롤러로, JSP를 뷰로 사용해서 MVC 패턴을 구현

MVC 패턴의 한계
MVC 컨트롤러의 경우 포워드와 viewPath가 중복되거나 공통 처리가 어려워 프론트 컨트롤러를 도입


MVC 프레임워크
: 프론트 컨트롤러 서블릿 하나로 클라이언트의 요청을 받아 요청에 받아 컨트롤러를 찾아서 호출
요청에 맞는 컨트롤러는 URL 매핑을 통해 호출 => Map.put(“URL”, new Controller());

로직: Http 요청 -> 핸들러 매핑 정보 조회 -> 핸들러를 처리할 수 있는 핸들러 어댑터 조회 -> 핸들러 어탭터에서 핸들러 호출 -> ModelView(뷰 이름과 model객체) 반환 -> viewResolver 호출 -> MyView 반환 -> rendering -> HTML 응답
viewResolver: 컨트롤러가 반환한 논리 뷰 이름을 실제 물리 뷰 경로로 변경, 렌더링을 담당하는 뷰 객체 반환

MVC 프레임워크 설계 버전
Version1. front controller도입해서 mapping 정보를 가지고 controller 호출
Version2. view 로직을 분리
: Controller 인터페이스의 process 메소드 반환 타입이 MyView로 바뀜
Version3. (서블릿 종속성 제거) 요청 파라미터 정보를 Map형태로 넘기면 서블릿 객체를 사용할 필요가 없다. -> request 객체를 대신에 별도의 model객체를 만들어서 사용
		 (뷰 이름 중복 제거) 컨트롤러는 뷰의 논리적인 이름만을 반환하고 resolver를 통해 실제 물리 뷰 경로로 변경
Version4. 컨트롤러가 ModelView가 아닌 뷰의 논리 이름을 반환하고 model객체는 파라미터로 전달하여 사용
Version5. 핸들러(컨트롤러)를 처리할 수 있는 핸들러 어댑터 패턴을 추가, 핸들러에서의 처리 방식이 다 다르기 때문에 어댑터가 다른 방식을 처리할 수 있도록 해준다.
이전에는 프론트 컨트롤러가 실제 컨트롤러를 호출했지만 이제는 이 어댑터를 통해 실제 컨트롤러가 호출


스프링 MVC
로직: Http 요청 -> 핸들러 매핑을 통해 요청 URL에 매핑된 핸들러 조회 -> 핸들러를 실행할 수 있는 핸들러 어댑터 조회 -> 핸들러 어댑터 실행 -> 실제 핸들러 실행 -> ModelAndView 반환 -> 뷰 리졸버 호출 -> 뷰 반환 -> 렌더링
DispatcherServlet: mvc 프레임워크에서 프론트 컨트롤러에 해당, 서블릿을 자동으로 등록하면서 모든 경로에 대해 매핑
￼![HTTP 요청](https://github.com/minjoo-6/springMVC/assets/65073916/a3e462a7-9948-4ac2-93f5-248280c70c53)


컨트롤러 호출 조건
1. 핸들러 매핑에서 해당 컨트롤러를 찾을 수 있어야 한다.
2. 핸들러 매핑을 통해 찾은 핸들러를 실행할 수 있는 핸들러 어댑터가 필요

스프링의 경우 핸들러 매핑과 핸들러 어댑터를 대부분 구현해둠
@RequestMapping: 애노테이션 기반의 컨트롤러를 지원하는 매핑과 어댑터
@Controller: 반환 값이 String인 경우 뷰 이름으로 인식하여 뷰를 찾고 렌더링

mv.addObject(“member”, member): ModelAndView를 통해 데이터를 추가

PathVariable(경로 변수)
@PathVariable(“userId”) String userId (경로 변수의 이름과 파라미터의 이름이 같으면 (“userId”)는 생략가능)


HTTP 요청 파라미터
1. 쿼리, HTML Form
request.getParameter()

2. @RequestParam
	: 파라미터 이름을 바인딩
-> @RequestParam(“username”) String memberNname
-> @RequestParam String username (HTTP 파라미터 이름과 변수 이름이 같으면 생략 가능)
-> String username (String, int 등의 단순 타입이면 @RequestParam도 생략 가능)

required: 파라미터 필수 여부 (기본값은 true, 애노테이션을 생략하고 파라미터 선언 시 기본값은 false)
defaultValue: 파라미터의 기본 값 설정

3. @ModelAttribute
	: 요청 파라미터를 받아서 필요한 객체를 만들고 그 객체에 값을 넣어주는 과정을 자동화해주는 애노테이션(생략 가능)
@RequestParam String username;
@RequestParam int age;
HelloData data = new HelloData();
data.setUsername(username);
data.setAge(age);

↓↓

Public String modelAttritbute(@ModelAttribute HelloData helloData){}
: HelloData 객체를 생성하고 요청 파라미터의 이름으로 HelloData 객체의 프로퍼티를 찾는다. 그 후 해당 프로퍼티의 setter를 호출해서 값을 바인딩


HTTP 요청 메세지
1. 단순 텍스트
: HTTP 메세지 바디에 데이터를 직접 담아서 요청
@RequestBody: 메세지 바디 정보를 직접 조회
HttpEntity: 헤더, 바디 정보를 편리하게 조회 및 반환

2. JSON
@RequsetBody를 사용해서 HTTP 메세지에서 데이터를 꺼내고 messageBody에 저장한 후 obejectMapper를 통해 자바 객체로 변환
@RequsetBody는 생략 불가능
생략 시, @ModelAttribute가 자동으로 적용되어 HTTP 메세지 바디가 아닌 요청 파라미터로 처리


HTTP 응답
1. 정적 리소스
src/main/resources/static 경로에 있으면 웹 브라우저에서 파일을 변경 없이 그대로 서비스

2. 뷰 템플릿
src/main/resources/templates
뷰 템플릿을 거쳐서 HTML이 생성되고 뷰가 응답을 만들어서 전달

3. HTTP API, 메세지 바디에 직접 입력
@ResponseBody: 응답의 경우에도 view 조회를 하지 않고 HTTP 메세지 바디에 내용을 넣음
@RestController: 반환 값으로 뷰를 찾는 것이 아닌 HTTP 메세지 바디에 바로 입력


HTTP 메세지 컨버터
뷰 템플릿으로 HTML을 생성해서 응답하는 것이 아닌 HTTP API처럼 JSON 데이터를 HTTP 메세지 바디에서 직접 읽거나 쓰는 경우 사용
- HTTP 요청: @RequestBody, HttpEntity(RequestEntity)
- HTTP 응답: @ResponseBody, HttpEntity(ResponseEntity)
canRead(), canWrite()를 통해 메세지 컨버터가 해당 클래스, 미디어타입을 지원하는지 체크하고 read(), write()를 실행

1. ByteArrayHttpMessageConverter: byte[] 데이터 처리
2. StringHttpMessageConverter: String을 데이터로 처리
3. MappingJackson2HttpMessageConverter: application/json 처리

￼
@RequestMapping을 처리하는 핸들러 어댑터인 @RequestMappingHandlerAdapter에는 ArgumentResolver가 있다.
ArgumentResolver: 핸들러가 필요로 하는 매우 다양한 종류의 파라미터를 유연하게 처리하여 파라미터 생성
- ArgumentResolver에 요청하는 파라미터가 @RequestBody or HttpEntity인 경우 HTTP 메세지 컨버터를 사용해 read
- 응답의 경우에도 @ResponseBody or HttpEntity를 처리하는 ReturnValueHandler에서 HTTP 메세지 컨버터를 호출해 write


타임리프
: th:xxx가 붙은 부분은 서버사이드에서 렌더링 되고, 기존 것을 대체

* 사용 선언 - th:href=@{/css/bootstrap.min.css}”
* URL 링크 표현식 - @{…}
* 속성 변경 - th:onclick
* 리터럴 대체 - |…| : 문자와 표현식 등을 더해서 사용할 수 있다.
* 반복 출력 - th:each
* 변수 표현식 - ${…}
* 내용 변경 - th:text

타임리프는 내츄럴 템플릿(순수 html 파일은 유지하면서 서버를 통해 뷰 템플릿을 거치면 동적 페이지도 확인할 수 있는 특징)이다.


PRG(Post/Redirect/Get)
POST 방식으로 온 요청에 대해 GET 방식의 웹 페이지로 리다이렉트 시키는 패턴
PRG를 사용하지 않았을 때의 문제점
1. 새로고침으로 인한 동일한 요청이 연속적으로 보내지는 이슈
2. Form형식의 POST 요청을 보낼 경우 파라미터 값들이 URL에는 포함되지 않기 때문에 URL을 공유해도 에러페이지가 발생






