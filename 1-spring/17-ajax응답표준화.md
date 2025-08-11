# AJAX 응답 표준화하기
- AJAX 응답을 표준화하면 프론트엔드와 백엔드 간의 통신이 일관되고, 예외 처리가 명확해지며 유지보수가 쉬워진다.

## 표준 AJAX 응답 포맷
```json
{
    "success": true,
    "code": 200,
    "message": "요청이 성공적으로 처리되었습니다.",
    "data": [
        {"no":1, "title":"연습1"},
        {"no":2, "title":"연습2"}
    ]
}
```

| 필드        | 설명                                |
| --------- | --------------------------------- |
| `success` | 요청 처리 결과 (`true` / `false`)       |
| `code`    | HTTP 또는 내부 응답 코드 (e.g., 200, 400) |
| `message` | 사용자 또는 디버깅용 메시지                   |
| `data`    | 실제 응답 데이터 (null일 수도 있음)           |


## 응답 메세지를 표현하는 `ApiResponse`
```java
@Getter
public class ApiResponse<T> {
    private boolean success;
    private int code;
    private String message;
    private T data;

    public ApiResponse(boolean success, int code, String message, T data) {
        this.success = succes;
        this.code = code;
        this.message = message;
        this.data = data;
    }

    public static ApiResponse<Void> success(String message) {
        return new ApiResponse<Void>(true, 200, message, null);
    }
    
    public static <T> ApiResponse<T> success(T data) {
        return new ApiResponse<Void>(true, 200, "요청이 성공적으로 처리되었습니다.", data);
    }

    public static <T> ApiResponse<T> success(String message, T data) {
        return new ApiResponse<Void>(true, 200, message, data);
    }

    public static ApiResponse<Void> fail(int code, String message) {
        return new ApiResponse<Void>(false, code, message, null);
    }
}
```

## 컨트롤러의 요청 핸들러메소드에서 JSON 응답 보내기
```java
@RestController
@RequestMapping("/api/admin/users")
public class ApiAdminUserController {

    // 일부 코드 생략

    @GetMapping
    public ResponseEntity<ApiResponse<List<User>>> getUsers() {
        List<User> users = userService.getUsers();
        return ResponseEntity
            .ok()
            .body(ApiResponse.success(users));
    }

    @GetMapping("/{no}")
    public ResponseEntity<ApiResponse<User>> getUser(@PathVariable("no") int userNo) {
        User user = userService.getUsers(userNo);
        return ResponseEntity
            .ok()
            .body(ApiResponse.success(user));
    }
}
```

### ResponseEntity<T>
- `ResponseEntity`는 Spring MVC에서 HTTP 응답(상태 코드, 헤더, 본문)을 조작할 수 있게 해주는 **응답 래퍼 클래스**다.
- 주요 구성 요소
    | 구성 요소                 | 설명                                       |
    | --------------------- | ---------------------------------------- |
    | `T body`              | 응답 데이터 (객체 또는 JSON)                      |
    | `HttpStatus status`   | HTTP 상태 코드 (200, 400, 500 등)             |
    | `HttpHeaders headers` | 응답 헤더 (예: Content-Type, Authorization 등) |
- 주요 메소드
    | 메소드                         | 설명                              |
    | --------------------------- | ------------------------------- |
    | `ok()`                      | 200 OK 상태 코드와 함께 응답 반환          |
    | `ok(T body)`                | 200 OK와 응답 본문 포함                |
    | `status(HttpStatus status)` | 지정한 상태 코드 응답 반환 시작              |
    | `status(int statusCode)`    | 숫자 상태 코드로 응답 반환 시작              |
    | `badRequest()`              | 400 Bad Request 응답              |
    | `notFound()`                | 404 Not Found 응답                |
    | `noContent()`               | 204 No Content 응답               |
    | `created(URI location)`     | 201 Created 응답과 Location 헤더 포함  |
    | `build()`                   | body 없이 상태만 포함된 응답 생성           |
    | `body(T body)`              | body 포함 응답 생성 시 사용 (chaining 시) |

