---
layout: post
title:  "컨테이너의 역할"
date:   2016-08-08 20:50:00 +0900
tags: [jsp]
---

컨테이너의 대표적인 예는 톰캣을 들 수가 있다. 사용자로부터 요청을 받으면 웹서버(아파치나 nginx와 같은)는 서블릿을 관리하고 있는 컨테이너에게 이 요청을 넘긴다. 요청을 받는 컨테이너는 HTTP Request와 HTTP Response 객체를 만들어, 이를 인자로 서블릿 doPost()나 doGet() 메소드 중 하나를 호출한다.

## 컨테이너가 주는 혜택

### 통신 지원

컨테이너는 서블릿과 웹 서버가 서로 통신할 수 있는 방법을 제공한다. 이는 개발자가 직접 ServerSocket을 만든다거나 특정 포트에 리스닝하고 연결 요청 처리를 하는 등의 복잡한 작업을 할 필요가 없다는 의미이다. 컨테이너는 이러한 기능들을 API로 제공하여 개발자가 서블릿 구현(비즈니스 로직)에만 전념할 수 있도록 도와준다.

### 라이프사이클 관리

컨테이너는 서블릿 클래스를 로딩하여 인스턴스화하고, 초기화 메소드를 호출하고, 요청이 들어오면 적절한 서블릿 메소드를 호출하는 작업을 한다. 서블릿이 생명을 다한 순간에는 적절하게 가비지 컬렉션을 진행하므로 개발자가 서블릿 자원을 관리할 필요가 없다.

### 멀티스레딩 지원

컨테이너는 요청이 들어올 때마다 새로운 자바 스레드를 하나 만든다. 다중 요청에 대한 스레드 생성 및 운영을 알아서 해주므로 개발자가 스레드 안전성에 대해 신경쓸 필요가 없다.

### 선언적인 보안 관리

컨테이너가 있는 환경에서는 보안 관리에 대한 것은 XML 배포 서술자에다가 기록하면 된다. 뭔가 수정할 것이 생기더라도 자바 소스 코드를 수정할 필요 없이 설정파일(DD)만 수정하면 되므로 재컴파일이 필요 없다.

* DD는 배포 서술자(Deployment Descriptor)를 의미하는데, web.xml 파일을 생각하면 된다. 이 DD를 사용하면 작성한 코드를 수정하지 않아도 웹 애플리케이션을 수정할 수 있는 선언적인 매커니즘을 제공하게 된다.
  * DD 파일에 서블릿의 매핑 정보를 입력하는 경우 서블릿의 정확한 경로 없이 패키지만 명시되어 있는데, 이는 컨테이너가 정해진 위치에서만 서블릿을 찾기 때문이다.
  * web.xml에 기록된 서블릿 정보로 해당 서블릿을 탐색하는 과정
  {% highlight xml %}
    <!--
    요청이 들어오면, 해당 요청의 url이 url-pattern에 매칭 되는지 체크한다.
     /SelectBeer.do로 요청했다면 해당 url-pattern이 매칭되어 서블릿 이름을 컨테이너가 인식하게 된다.
     서블릿 중에 해당 서블릿 이름을 가진 것이 있는지 탐색한 후에
     매칭되는 서블릿의 클래스 정보를 읽어 해당 클래스를 로드하고 초기화 한다.
    -->
    <servlet>
      <servlet-name>Ch3 Beer</servlet-name>
      <servlet-class>io.github.yonghochoi.BeerSelect</servlet-class>
    </servlet>

    <servlet-mapping>
      <servlet-name>Ch3 Beer</servlet-name>
      <url-pattern>/SelectBeer.do</url-pattern>
    </servlet-mapping>
  {% endhighlight %}

### JSP 지원

JSP를 통해 비즈니스 로직과 프리젠테이션 로직을 분리할 수 있다.

* MVC : 비즈니스 로직과 프리젠테이션 로직의 분리를 위한 패턴. 이는 비즈니스 로직이 재사용 가능한 자바 클래스로 독립적으로 존재 가능하기 때문에 뷰가 어떤 것이 되어도 관계 없다는 의미이다.
  * 비즈니스 로직과 프리젠테이션 로직의 분리는 JSP나 서블릿에 국한된 패턴이 아니라 모든 소프트웨어 개발에서 해결해야 하는 아주 중요한 문제이다.
  * 서블릿, JSP 환경에서의 MVC
    * 모델(Model) : 비즈니스 로직이 여기에 들어가고, MVC 패턴에서 데이터베이스와 통신하는 유일한 곳이다. (비즈니스 로직)
    * 뷰(View) : 프리젠테이션에 대한 책임을 지고, 컨트롤러로부터 모델 정보를 읽어온다. 또한 사용자가 입력한 정보를 컨트롤러에게 넘겨줘야 한다. (JSP)
    * 컨트롤러(Controller) : Request 객체에서 사용자가 입력한 정보를 뽑아내어 모델에 대해 어떤 작업(모델 정보 수정, 뷰에 넘길 모델 생성 등)을 해야 하는지 알아낸다. (서블릿)

## 참고

* [Head First Servlets & JSP]

[Head First Servlets & JSP]: http://goo.gl/bMCLzV
