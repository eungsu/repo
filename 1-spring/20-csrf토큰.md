# CSRF 토큰
- CSRF(Cross-Site Request Forgery, **사이트 간 요청 위조**)는 사용자가 자신의 의도와는 무관하게 악의적인 요청을 하도록 유도하는 공격이다.
- Spring Security에서는 이러한 공격을 방지하기 위해 **CSRF 보호 기능**을 기본적으로 활성화된다.

## CSRF 토큰이란?
CSRF 토큰은 서버가 클라이언트에게 발급한 1회용 고유 토큰으로, 클라이언트가 요청을 보낼 때마다 함께 전달되어야 한다. 서버는 이 토큰이 유효한지 검증하여 공격 여부를 판단한다.

## Spring Security의 CSRF 보호 동작 원리
1. 사용자가 GET 방식으로 페이지 요청 → 서버는 CSRF 토큰을 세션 기반으로 생성
2. 서버는 이 토큰을 HTML 페이지 내 숨겨진 필드 또는 메타태그를 통해 클라이언트에 전달
3. 사용자가 POST/PUT/DELETE 등 상태 변경 요청을 보낼 때, CSRF 토큰을 함께 전송
4. 서버는 클라이언트가 보낸 토큰과 세션에 저장된 토큰을 비교 → 일치하면 요청 처리

## HTML(JSP, Thymeleaf)에서의 CSRF 토큰 처리

### JSP 기반 : `<sec:csrfInput />`
```jsp
<%@ taglib prefix="sec" uri="http://www.springframework.org/security/tags" %>

<form method="post" action="/post">
    <sec:csrfInput/>
    <input type="text" name="title">
    <button type="submit">등록</button>
</form>
```

### Thymeleaf 기반
```html
<form th:action="@{/post}" method="post" th:object="${post}">
    <input type="hidden" th:name="${_csrf.parameterName}" th:value="${_csrf.token}" />
    <input type="text" th:field="*{title}" />
    <button type="submit">등록</button>
</form>
```
- <form>에 `th:action`만 설정해도 CSRF 토큰이 자동 포함된다. (`Spring Security` + `Thymeleaf` 연동 시).

### AJAX 기반 요청에서 CRRF 토큰 처리
```html
<head>
    <!-- JSP -->
    <sec:csrfMetaTags />

    <!-- JSP 또는 Thymeleaf --->
    <meta name="_csrf" content="${_csrf.token}"/>
    <meta name="_csrf_header" content="${_csrf.headerName}"/>
</head>
```

#### jQuery 기반 AJAX 요청 시 CSRF 토큰 처리
```html
<script>
    const token = $('meta[name="_csrf"]').attr('content');
    const header = $('meta[name="_csrf_header"]').attr('content');

    // 모든 AJAX 요청에 CSRF 요청 헤더가 자동으로 추가된다.
    $(document).on('ajaxSend', function(e, xhr, options) {
        xhr.setRequestHeader(header, token)
    });
</script>
```

#### fetchA API 기반 AJAX 요청 시 CSRF 토큰 처리리 
```html
<script>
    const token = $('meta[name="_csrf"]').attr('content');
    const header = $('meta[name="_csrf_header"]').attr('content');

    fetch('/api/todos', {
        method: 'POST',
        headers: {
            'Content-Type': 'application/json',
            [header]: token
        },
        body: JSON.stringify({ title: "일정 제목" })
    })
    .then(response => {
        if (!response.ok) {
            throw new Error("등록 실패");
        }
        return response.json();
    })
    .then(data => {
        console.log("등록 성공:", data);
    })
    .catch(error => {
        console.error("에러:", error);
    });
</script>
```

## 언제 CSRF 보호가 필요한가?
| 사용 시나리오                       | CSRF 보호 필요 여부 | 이유                                       |
| ----------------------------- | ------------- | ---------------------------------------- |
| JSP/Thymeleaf MVC 웹앱 (세션 로그인) | 필요 ✅          | 세션 기반 인증은 브라우저가 자동으로 쿠키를 보내므로 CSRF 공격 가능 |
| REST API + JWT (무상태 인증)       | 불필요 ❌         | Authorization 헤더에 토큰을 명시적으로 담으므로 공격 불가능  |
| GET 요청                        | 보통 불필요 ❌      | 리소스 조회만 수행하며, 상태 변경 없음                   |
| POST/PUT/DELETE 요청            | 필요 ✅          | 서버 자원을 변경하는 요청은 CSRF의 주된 대상              |
