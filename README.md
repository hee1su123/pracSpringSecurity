# pracSpringSecurity / JWT 와 REST API 를 이용한스프링 시큐리티 연습

## 용어 정리

### Authentication
### Authorization
### JWT
### 

## 스프링 시큐리티 내부 작동 원리

### Spring Security Filter Chain

- 스프링 시큐리티 프레임워크를 추가하는 즉시 모든 request 를 가로채는 Filter Chain 을 등록한다.
  - 요청된 URL 이 접근 가능한지 검증
  - Session-based authentication의 경우 이미 인증된 사용자인지 검증
  - 사용자 하는 행위가 인가된 행위인지 검증
```spring.security.filter.order=10``` 를 application.properties 에 선언 시 스프링 시큐리티 필터를 거치기 전에 10개의 커스텀 필터를 먼저 통과하게 된다.

### AuthenticatorManager

- 여러 사용자의 요청에 따라 알맞는 인증을 보내주는 역할 담당

### AuthenticationProvider

- 두가 종류의 인증 절차를 담당한다
  - Authenticate : 요청에 대해 인증절차 수행
  - Supports : 해당 provider가 해당 인증을 수행하는지 확인

- 해당 인터페이스의 구현체의 중요한 역할 중 하나는 UserDetailsService 에서 사용자의 정보를 검색하는 것이다.

### UserDetailsService

- 사용자의 상세 정보를 담고 있는 핵심 인터페이스
- 대부분의 경우 AuthenticationProviders 가 자격증명을 기반으로 유저의 정보를 DB에서 추출한 후 검증 절차를 거친다. 이런 경우가 매우 흔해지면서 하나의 분리된 인터페이스를 사용하기로 했다.
  - loadUserByUsername : username 을 받아 유저 정보를 반환한다

## JWT 와 Spring Security 를 이용한 인증 절차
- ```implementation 'io.jsonwebtoken:jjwt:0.9.1'``` build.gradle 에 추가
<br></br>
- Spring Security 를 커스텀화 하기 위해 @EnableWebSecurity 어노테이션을 포함한 WebSecurityConfigurerAdapter 클래스를 선언한다
  - authentication manager 에게 정확한 provider 를 설정한다
```C
@EnableWebSecurity
public class SecurityConfig extends WebSecurityConfigurerAdapter {
-- 생략 --
}
```
<br></br>
- JwtAuthenticationFilter 생성
```C
public class JwtAuthenticationFilter extends GenericFilterBean {
  @Override
  public void doFilter() {
    --- 생략 ---
  }
}
```
<br></br>
- JwtTokenProvider 생성
```C
public class JwtTokenProvider {
  // 토큰 생성
  // 토큰에서 정보 조회
  // Request Header 에서 토큰 값 가져오기
  // 토큰 유효성 검사
  // UserDetails 생성 (UserDetailsService 의 loadUserByUsername 사용)
}
```
<br></br>
- 인증 완료 후 DB 에서 이에 맞는 정보를 가져오기 위해 UserDetailsService 를 생성
```C
public interface UserDetailsService {
  UserDetails loadUserByUsername(String username) {
    throws UsernameNotFoundException;
  }
}
```
## Reference
https://www.toptal.com/spring/spring-security-tutorial
https://webfirewood.tistory.com/115
