# Spring Boot와 mybatis 연동

## 1. 라이브러리 의존성 추가
```xml
<!-- mybatis 지원 라이브러리 -->
<dependency>
    <groupId>org.mybatis.spring.boot</groupId>
    <artifactId>mybatis-spring-boot-starter</artifactId>
    <version>3.0.4</version>
</dependency>
<!-- 오라클 JDBC 라이브러리 -->
<dependency>
    <groupId>com.oracle.database.jdbc</groupId>
    <artifactId>ojdbc11</artifactId>
    <scope>runtime</scope>
</dependency>
```

## 2. 테이블 생성하기
```sql
CREATE TABLE APP_USERS (
    USER_NO NUMBER(5) PRIMARY KEY,
    USERNAME VARCHAR2(50) UNIQUE,   -- 아이디
    PASSWORD VARCHAR2(100),
    NAME VARCHAR2(100),             -- 이름
    EMAIL VARCHAR2(100) UNIQUE,
    DELETED CHAR(1) DEFAULT 'N',
    CREATED_DATE DATE DEFAULT SYSDATE,
    UPDATED_DATE DATE DEFAULT SYSDATE
);

CREATE TABLE APP_POSTS (
    POST_NO NUMBER(5) PRIMARY KEY,
    USER_NO NUMBER(5) NOT NULL,
    TITLE VARCHAR2(200) NOT NULL,
    CONTENT VARCHAR2(2000) NOT NULL,
    DELETED CHAR(1) DEFAULT 'N',
    CREATED_DATE DATE DEFAULT SYSDATE,
    UPDATED_DATE DATE DEFAULT SYSDATE,
    
    CONSTRAINT APP_POSTS_USER_NO_FK FOREIGN KEY (USER_NO)
        REFERENCES APP_USERS (USER_NO)
);
```

## 3. Value Object 작성하기
```java
@Getter
@Setter
@NoArgsConstructor
@Alias("User")
public class User {

	private int userNo;
	private String username;
	private String password;
	private String name;
	private String email;
	private String deleted;
	private Date createdDate;
	private Date updatedDate;
}
```

```java
@Getter
@Setter
@NoArgsConstructor
@Alias("Post")
public class Post {

	private int postNo;
	private int userNo;
	private String title;
	private String content;
	private String deleted;
	private Date createdDate;
	private Date updatedDate;
	
	// 게시글의 작성자 정보
	private String username;
	private String name;
}
```

## 4. Mapper 인터페이스 작성하기
```java
@Mapper
public interface UserMapper {

}
```

```java
@Mapper
public interface PostMapper {

	/**
	 * 모든 게시글 정보를 조회해서 반환한다.
	 * @return 전체 게시글 목록
	 */
	List<Post> getAllPosts();
}
```

## 5. Mapper 인터페이스를 스캔해서 구현체를 스프링 컨테이너의 빈으로 등록시키기
```java
@SpringBootApplication
@MapperScan("com.example.demo.mapper")
public class App1Application {

	public static void main(String[] args) {
		SpringApplication.run(App1Application.class, args);
	}

}
```

## 6. Mapper XML 정의하기
```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN" "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="com.example.demo.mapper.UserMapper">


</mapper>
```

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN" "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="com.example.demo.mapper.PostMapper">

	<!-- 
		List<Post> getAllPosts()
	 -->
	<select id="getAllPosts" resultType="Post">
		select
			P.post_no		postNo
		  , P.user_no		userNo
		  , P.title
		  , P.created_date	createdDate
		  , U.username
		  , U.name			
		from
			app_posts P, app_users U
		where
			P.user_no = U.user_no
		order by
			P.post_no desc
	</select>
</mapper>
```

## 7. `application.properties` 설정 추가하기
```properties
# Spring Boot 애플리케이션의 이름을 지정한다.
spring.application.name=app1

### 로깅 레벨 설정하기
# 이 애플리케이션의 기본 로그 출력레벨을 info로 지정한다.
logging.level.root=info
# com.example.demo.mapper 패키지의 로그 출력레벨을 debug로 지정한다.
logging.level.com.example.demo.mapper=trace

### mybatis 설정
# 매퍼 xml 파일의 경로를 지정한다.
mybatis.mapper-locations=classpath:mapper/*.xml
# 타입 별칭이 지정된 클래스가 정의된 패키지를 지정한다.
mybatis.type-aliases-package=com.example.demo.vo
# null값이 허용된 컬럼에 입력되는 null값은 NULL 타입으로 지정한다.
mybatis.configuration.jdbc-type-for-null=NULL
```

## 8. Service 클래스에 Mapper 인터페이스 구현객체 주입하기
```java
@Service
public class PostService {

	@Autowired
	private PostMapper postMapper;
	
	/**
	 * 전체 게시글 정보를 조회해서 반환하는 업무로직을 수행한다.
	 * @return 전체 게시글 목록
	 */
	public List<Post> getAllPosts() {
		return postMapper.getAllPosts();
	} 
}
```