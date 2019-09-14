---
title: "JSP 2일차 : Request, Include, Forward"
date: 2019-04-24 18:00:00 -0400
categories: JSP
comments: true


---

## Request 내장객체

### request.setAttribute(String name, Object o)

- request에 컬렉션 타입을 포함한 모든 데이터를 저장가능

### request.getAttribute(String name)

- name으로 저장된 데이타를 읽어옴 : Object o 반환

```php
<%@page import="java.util.ArrayList"%>
<%@page import="java.util.List"%>
<%@ page language="java" contentType="text/html; charset=UTF-8"
        pageEncoding="UTF-8"%>
<!DOCTYPE html>
<html>
  <head>
    <meta charset="UTF-8">
    <title>Insert title here</title>
  </head>
  <body>

    <% 
	List<String> list = new ArrayList<String>();
	list.add("rose");
	list.add("tulip");
	list.add("sunflower");
	list.add("daisy");
	list.add("orchard");

	//리퀘트에 리스트 컬렉션 저장하기 
	request.setAttribute("data", list);
	//리퀘스트에 문자열 타입 저장하기
	request.setAttribute("msg", "Happy day");
    %>

    <h2>리퀘스트에 저장된 데이타 읽어오기</h2>

    <%
       	//반환값이 오브젝트이므로 꼭 형변환을 해줘야 한다.
       	List<String> datas = (ArrayList<String>)request.getAttribute("data");

    	String msg = (String)request.getAttribute("msg");

    	for(String s: datas){
    %>
    <%=s %><br>
      <%
         }
         %>
        <%=msg %>

          </body>
        </html>

```



## JSP 액션태그

### include

- page에서 지정한 파일의 결과가 현재 페이지로 포함됨

  ```html
  	<jsp:include page="">
  		<jsp:param .....> 생략가능
  	<jsp:include>
  ```

### forward

- 파일이동(request, response 그대로 유지됨), url주소가 안바뀜

  ```html
  	<jsp: forward page="">
  		<jsp:param .....> 생략가능
  	<jsp:forward>
  ```

예시

```html
<%@page import="java.util.Date"%>
<%@ page language="java" contentType="text/html; charset=UTF-8"
      pageEncoding="UTF-8"%>
<!DOCTYPE html>
<html>
  <head>
    <meta charset="UTF-8">
    <title>Insert title here</title>
  </head>
  <body>

    <h2>여러분.. 안녕하세요. 이 글자가 보일까요? </h2>

    <% 
       request.setAttribute("msg", "오늘은 즐거운 수요일 입니다");
       request.setAttribute("day", new Date().toString());

       %>

      <!-- forward action tag -->


      <jsp:forward page="Ex3_Forward_two.jsp"/> <!-- 반드시 닫아줄것  -->
      </body>
    </html>

```

```html
	<%@ page language="java" contentType="text/html; charset=UTF-8"
	    pageEncoding="UTF-8"%>
	<!DOCTYPE html>
	<html>
	<head>
	<meta charset="UTF-8">
	<title>Insert title here</title>
	</head>
	<body>
	
	
	<h2>현재 파일명은 Ex3_Forward_two.jsp 입니다.</h2>
	<h3>one 에서 리퀘스트에 저장한 데이타를 불러오자!</h3>
	
	<%
	String msg = (String)request.getAttribute("msg");
	String day = (String)request.getAttribute("day");
	
	%>
	
	<b> msg: <%=msg %><br> day: <%=day %></b>
	</body>
	</html>

```

### 실습문제

> 1번파일에서 2번으로 액션 실행 후 2번에서 3번으로 포워딩  
>
> 이 때 1번파일의 폼태그를 3번에서 읽을 수 있는지 확인하기

```html
<%@ page language="java" contentType="text/html; charset=UTF-8"
    pageEncoding="UTF-8"%>
<!DOCTYPE html>
<html>
  <head>
    <meta charset="UTF-8">
    <title>one</title>
  </head>
  <body>

    <!-- 여기서는 폼태그로 값을 입력받고 two 로 값을 넘겨준다. -->
    <form action="Ex4_Forward_two.jsp" method="post">
      <p>이름입력: </p><input type="text" name="name">
      <input type="submit" name="submit">

    </form>

  </body>
</html>
```

```html
<%@ page language="java" contentType="text/html; charset=UTF-8"
	    pageEncoding="UTF-8"%>
<!DOCTYPE html>
<html>
  <head>
    <meta charset="UTF-8">
    <title>two</title>
  </head>
  <body>

    <!-- 폼태그에서 입력받은 값을 리퀘스트에 저장한다. 그리고 3번으로 보내줌. -->
    <% 
       String name = request.getParameter("name");
       request.setAttribute("username", name);

       %>
      <jsp:forward page="Ex4_Forward_three.jsp"/>


      </body>
    </html>
```

```html
<%@ page language="java" contentType="text/html; charset=UTF-8"
	    pageEncoding="UTF-8"%>
<!DOCTYPE html>
<html>
  <head>
    <meta charset="UTF-8">
    <title>three</title>
  </head>
  <body>

    <!-- 리퀘스트에 저장된 데이타를 꺼내보자! -->
    <%
       String username = (String)request.getAttribute("username");

       %>

      <p>유저이름은 <%=username %> 입니다.</p>

      </body>
    </html>
```

> param 사용해보기

```html
<%@ page language="java" contentType="text/html; charset=UTF-8"
	    pageEncoding="UTF-8"%>
<!DOCTYPE html>
<html>
  <head>
    <meta charset="UTF-8">
    <title>ex5_1</title>
  </head>
  <body>
    <% request.setCharacterEncoding("UTF-8"); %>
      <jsp:forward page="Ex5_ForwardParam_2.jsp">
        <jsp:param value="../image/chr07.gif" name="img1"/>
        <jsp:param value="happy day~ㅎㅎㅎ" name="msg"/> 
      </jsp:forward>

      </body>
    </html>

```

```html
<%@ page language="java" contentType="text/html; charset=UTF-8"
  pageEncoding="UTF-8"%>
<!DOCTYPE html>
<html>
  <head>
    <meta charset="UTF-8">
    <title>ex5_2</title>
  </head>
  <body>

    <h2>2번파일입니당~ 받아온 param을 꺼내봅시다.</h2>

    <%
    request.setCharacterEncoding("UTF-8");
    String img1 = request.getParameter("img1");
    String msg = request.getParameter("msg");

    %>
    <p>msg: <%=msg %><br><img src="<%=img1 %>"></p>

  </body>
</html>
```

> include 사용해보기

```html
<%@ page language="java" contentType="text/html; charset=UTF-8"
  pageEncoding="UTF-8"%>
<!DOCTYPE html>
<html>
  <head>
    <meta charset="UTF-8">
    <title>Insert title here</title>
  </head>
  <body>

    <h2>액션태그 include 테스트</h2><br>
    <h3>구구단 예제페이지 가져와보기~</h3><br>
    <hr>
    <jsp:include page="../day0423/Ex4_Gugudan.jsp"/>
    <hr>
    <h3>이미지 예제 페이지 가져와보기~</h3>
    <jsp:include page="../day0423/Ex5_ImageLoop.jsp"/>

  </body>
</html>
```

> include & param 사용

```html
<%@ page language="java" contentType="text/html; charset=UTF-8"
  pageEncoding="UTF-8"%>
<!DOCTYPE html>
<html>
  <head>
    <meta charset="UTF-8">
    <title>Insert title here</title>
  </head>
  <body>

    <h2>include연습하기</h2>

    <jsp:include page="Ex7_IncludeParam_2.jsp">
      <jsp:param value="../image/ani13.gif" name="img1"/>
      <jsp:param value="have a nice day~! " name="msg"/>
    </jsp:include>
    <hr>
    <img src="../image/19.JPG">

  </body>
</html>
```

```html
<%@ page language="java" contentType="text/html; charset=UTF-8"
  pageEncoding="UTF-8"%>
<!DOCTYPE html>
<html>
  <head>
    <meta charset="UTF-8">
    <title>Insert title here</title>
  </head>
  <body>

    <h3>2번파일 입니당</h3>

    <%
    String img1 = (String)request.getParameter("img1");//param으로 줬으니까 param으로 받아야 한다.
    String msg = (String)request.getParameter("msg");

    %>

    <p>msg = <%=msg %> </p><br>
    <img src="<%=img1 %>">

  </body>
</html>
```



------

## 파일이동

- response.sendRedirect(url 또는 파일명) : 해당 url 또는 파일로 이동

- forward  와의 차이점 : request 와 response 가 새로 생성, url 주소가 바뀐다

- 파일이동시 데이타 넘기기 

  ```txt
  response.sendRedirect(“ex.jsp?a=lee&b=100”);
  ```

  - ex.jsp로 이동된다는 의미

  - 이 때 ex.jsp에서는 getParameter를 이용해서 a, b를 읽을 수 있다(get방식) : get 방식으로 데이타를 넘길시 톰켓 8버전 이전엔 한글깨짐 있음

```html
<%@ page language="java" contentType="text/html; charset=UTF-8"
  pageEncoding="UTF-8"%>
<!DOCTYPE html>
<html>
  <head>
    <meta charset="UTF-8">
    <title>Insert title here</title>
  </head>
  <body>
    <%
    String name= "alice"; //한글주면 깨짐 
    int age = 23; //int로 보내줘도 어차피 getParameter가 문자열로밖에 못읽는다... 
    response.sendRedirect("Ex9_redirect_2.jsp?name=" + name + "&age=" + age); //url주소 자체가 바뀐다.

    %>

  </body>
</html>

```

```html
<%@ page language="java" contentType="text/html; charset=UTF-8"
  pageEncoding="UTF-8"%>
<!DOCTYPE html>
<html>
  <head>
    <meta charset="UTF-8">
    <title>Insert title here</title>
  </head>
  <body>

    <h2>여기는 redirect 2번 파일~</h2>

    <%

    String name = request.getParameter("name");
    String age = request.getParameter("age");

    %>

    <p>이름은: <%=name %><br> 나이는: <%=age %> </p>

  </body>
</html>
```

### 실습문제

> a 태그나 버튼 사용하여 파일 이동하기

```html
<%@ page language="java" contentType="text/html; charset=UTF-8"
  pageEncoding="UTF-8"%>
<!DOCTYPE html>
<html>
  <head>
    <meta charset="UTF-8">
    <title>Insert title here</title>
  </head>
  <body>


    <h2>a태그를 이용해서 파일 이동해보기 </h2>

    <%

    String name="bob";
    int age= 20;

    %>

    <a href="Ex10_move2.jsp?name=<%=name %>&age=<%=age %>">2번파일로 이동~~</a>
  </body>
</html>
```

```html
<%@ page language="java" contentType="text/html; charset=UTF-8"
  pageEncoding="UTF-8"%>
<!DOCTYPE html>
<html>
  <head>
    <meta charset="UTF-8">
    <title>Insert title here</title>
  </head>
  <body>

    <h2>1번 파일에서 보낸 데이타 읽어보깅~</h2>

    <% 
    String name= request.getParameter("name");
    String age = request.getParameter("age");

    %>

    <pre>
	이름 : <%=name %>
	나이 : <%=age %>
	</pre>

    <input type="button" value="3번 파일로 이동하기" onclick="location.href='Ex10_move3.jsp?msg=happy&day=2019-04-24'">

  </body>
</html>
```

```html
<%@ page language="java" contentType="text/html; charset=UTF-8"
  pageEncoding="UTF-8"%>
<!DOCTYPE html>
<html>
  <head>
    <meta charset="UTF-8">
    <title>Insert title here</title>
  </head>
  <body>

    <h3>3번 파일입니당</h3>

    <%
    String msg = request.getParameter("msg");
    String day = request.getParameter("day");

    %>

    <pre>
	2번 파일에서 받은 데이타 출력하기
	msg = <%=msg %>
	day = <%=day %>
	</pre>

    <input type="button" value="1번 파일로 이동하기" onclick="location.href='Ex10_move1.jsp'">
  </body>
</html>
```

> Ex12_test.jsp: 하나의 파일에서 모두 처리하세요
>
> - key 가 널값이면 폼을 만들고(이름,핸드폰,주소)
>
> - key 가result 면 폼의 데이타를 읽어서 출력 (출력 끝에 버튼(종료)을 통해서key에exit 전달)
>
> - key 가exit 면 예쁜 이미지와 함께Good Bye!! 라는 글자 출력
>
> - 그 아래에는 폼으로 가는 버튼 추가

```html
<%@ page language="java" contentType="text/html; charset=UTF-8"
  pageEncoding="UTF-8"%>
<!DOCTYPE html>
<html>
  <head>
    <meta charset="UTF-8">
    <title>Insert title here</title>
  </head>
  <body>


    <%
    String key = request.getParameter("key");

    if(key==null){
      %> 
    <form action="Ex12_test.jsp" method="post">
      이름 : <input type="text" name="name" size="10"><br>
      핸드폰 : <input type="text" name="phone" size="10"><br>
      주소 : <input type="text" name="addr" size="10"><br>
      <input type="hidden" name="key" value="submitted">
      <input type="submit" value="submit">
    </form>
    <%
    }else if(key.equals("submitted")){
      String name= request.getParameter("name");
      String phone= request.getParameter("phone");
      String addr = request.getParameter("addr");
      %>
    <pre>
	name: <%=name %>
	phone: <%=phone %>
	address: <%=addr %>
	</pre>
    <form action="Ex12_test.jsp" method="post">
      <input type="hidden" name="key" value="exit">
      <input type="submit" value="exit">
    </form>
    <%
    }else if(key.equals("exit")){
      %>
    <img src="../image/ani10.gif">
    <br>
    <form action="Ex12_test.jsp" method="post">
      <input type="submit" value="to the front page">
    </form>
    <% 
    }
    %>

  </body>
</html>

```

