# Gradle

**Gradle**은 2007년 JetBrains회사의 Hans Dockter가 개발한 **오픈 소스 빌드 자동화 도구**로 Apache Ant와 Apache Maven의 장점을 결합하여 만들어졌다.

**Gradle은 Groovy기반 스크립트 방식을 사용**하여 **build.gradle**이라는 빌드 파일을 만들기 때문에 **가독성이 좋고, 또 스크립트를 통해 여러가지 상황에 적합한 빌드를 만들어 낼 수 있어 유연**하다.

**Gradle은 빌드 과정에서 병렬 처리와 캐싱을 활용**하여 **Maven보다 10배~100배까지 향상된 성능**을 구현할 수 있다.

- Java, Kotlin, Groovy, Scala, Android 등 다양한 언어와 플랫폼을 지원한다.
- Maven, Ant 같은 기존 빌드 도구의 장점을 계승하면서, **스크립트 기반 유연성 + 고성능 빌드 시스템**을 제공한다.

## 특징

### 1. DSL 기반의 유연한 빌드 스크립트

Gradle은 **Groovy 또는 Kotlin DSL(Domain Specific Language)** 을 사용하여 빌드 스크립트를 작성한다.
- Maven처럼 XML 기반이 아니라 코드처럼 빌드 로직을 작성할 수 있어 유연성과 가독성이 뛰어나다.
- **조건문, 반복문, 함수 정의가 가능**하여 **동적인 빌드 구성이 가능**하다.

### 2. 빠른 빌드 성능 (Incremental Build + Build Cache)

Gradle은 성능에 최적화된 구조를 가지고 있다.
- **Incremental Build**: 변경된 파일만 다시 빌드하여 전체 시간을 절감
- Build Cache: 이전 빌드 결과를 저장하여 재사용 가능
- 병렬 빌드 및 Daemon 프로세스 활용으로 Maven보다 빠름

### 3. 플러그인 기반 아키텍처 (Plugin-based Architecture)

Gradle은 모든 기능이 플러그인 기반으로 동작한다.
기본 제공 플러그인 외에도 사용자 정의 플러그인을 만들거나 외부 플러그인을 추가할 수 있다.

```groovy
plugins {
    id 'java'
    id 'org.springframework.boot' version '3.2.0'
}
// java : Java 컴파일 테스트 지원 플러그인
// org.springframework.boot : Spring Boot 애플리케이션 지원 플러그인인
```

### 4. 의존성 관리의 유연성

Maven Central, JCenter, Google 등 다양한 리포지터리를 통해 라이브러리 의존성 관리가 가능합니다.

```groovy
dependencies {
    implementation 'org.springframework.boot:spring-boot-starter-web:3.2.0'
    testImplementation 'org.junit.jupiter:junit-jupiter:5.9.0'
}
```

### 5. 멀티 프로젝트(모듈) 빌드 지원

Gradle은 멀티모듈 프로젝트 지원에 최적화되어 있어, 대규모 프로젝트의 하위 모듈을 독립적이면서도 효율적으로 관리할 수 있다.
- 모듈 간 의존성도 쉽게 설정 가능
- 빌드 시간 단축 가능 (모듈 변경 감지)
- settings.gradle
    ```groovy
    rootProject.name = 'my-enterprise-app'
    include 'common', 'api', 'web'
    ```

### 6. Wrapper 기능 (Gradle Wrapper)

Gradle Wrapper를 사용하면 팀원 모두가 동일한 Gradle 버전을 사용할 수 있고,
Gradle을 따로 설치할 필요 없이, 프로젝트에 포함된 gradlew 스크립트만으로 빌드 가능하다.

## Gradle 기본 구조

Gradle은 기본적으로 다음과 같은 파일 구조를 가진다.

```plaintext
project/
 ├── build.gradle (빌드 스크립트)
 ├── settings.gradle (멀티 프로젝트 설정)
 ├── gradle/
 ├── gradlew (Unix용 Wrapper 실행 파일)
 ├── gradlew.bat (Windows용 Wrapper 실행 파일)
 └── src/
      ├── main/
      │    └── java/
      │    └── resources/
      └── test/
           └── java/
           └── resources/
```

- `build.gradle` : 빌드와 관련된 모든 설정 작성
- `settings.gradle` : 다중 모듈 프로젝트 시 루트 설정정
- `gradlew`,`gradlew.bat`  : 프로젝트별 Gradle Wrapper 실행 파일일
- `gradle/` : Wrapper 관련 설정 폴더

## Gradle 빌드 스크립트 예시

```groovy
// 사용할 플러그인을 명시한다.
plugins {
    id 'java'
    id 'org.springframework.boot' version '2.7.0'
    id 'io.spring.dependency-management' version '1.0.15.RELEASE'
}

// 기본 프로젝트 정보를 명시한다.
group = 'com.example'
version = '0.0.1-SNAPSHOT'
sourceCompatibility = '17'

// 의존성 다운로드 위치를 지정한다.
repositories {
    mavenCentral()
}

// 라이브러리 의존성을 관리한다.
dependencies {
    implementation 'org.springframework.boot:spring-boot-starter-web'
    testImplementation 'org.springframework.boot:spring-boot-starter-test'
}

// 테스트 설정
test {
    useJUnitPlatform()
}

```

## Spring Boot 프로젝트를 Maven을 이용해서 생성, 빌드, 테스트, 실행, 배포하기

1. Spring Boot 프로젝트 생성하기
   - Spring Boot 프로젝트는 **Spring Initializr**로 생성한다.
  
2. 빌드하기
    ```bash
    gradlew build
    # gradlew build - 소스 컴파일하고
    #                 테스트 실행하고
    #                 실행 파일(JAR, WAR) 생성
    #                 build/libs 폴더에 demo-0.0.1-SNAPSHOT.jar 생성
    ```

3. 애플리케이션 실행 (Spring Boot Run)
    ```bash
    gradlew bootrun
    # gradlew bootrun - Spring Boot 애플리케이션을 실행한다.
    #                   개발 중 빠른 실행 테스트에 주로 사용한다.

    # 옵션 추가 예시
    gradlew bootRun --args='--spring.profiles.active=dev --server.port=8080'
    # 특정 프로파일 적용 및 포트 설정
    ```

4. 프로젝트 클린
    ```bash
    gradlew clean
    # gradlew clean - 이전 빌드 결과물 (build/ 폴더)을 모드 삭제한다.
    #                 보통 빌드 전에 초기화하기 위해서 사용한다.

    gradlew clean build
    ```

5. 실행 파일 생성
    ```bash
    gradlew bootJar
    # gradlew bootJar - 실행 가능한 Spring Boot용 fat JAR 파일을 생성한다.
    ```

    ```bash
    gradlew bootWar
    # gradlew bootWar - Srping Boot 애플리케이션을 외부 Tocat에 배포하기 위한 WAR를 생성한다.
    #                 - build.gradle에 apply plugin: 'war' 또는
    #                                  plugin {id 'war'} 설정이 필요한다.
    ```
