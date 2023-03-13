### Chapter 13: Implementing the authorization server

In the previous chapter we used GitHub as Authorization server and resource server. But in this chapter we will build similar authorization server so that it can issue `access_token` based on grants.

- Authorization code
- password
- client credentials

With the token, client can access the resouces. That authorization server will also provide `refresh_token` so that after access_token's expiry it can ask the authorization server to provide a brand new access_token with a new refresh_token.

#### Writing your own authorization server implementation

Let's add library in build.gradle file

```java

```

Now, we need users to be connected to our authorization server. But it is same as before. Add the code below - 

```java
@Configuration
public class WebSecurityConfig {
 @Bean
    @Order(1)
    public SecurityFilterChain asSecurityFilterChain(HttpSecurity http) throws Exception {
        OAuth2AuthorizationServerConfiguration.applyDefaultSecurity(http);

        http.getConfigurer(OAuth2AuthorizationServerConfigurer.class).oidc(Customizer.withDefaults());

        http.exceptionHandling(e -> e.authenticationEntryPoint(new LoginUrlAuthenticationEntryPoint("/login")));

        return http.build();
    }

    @Bean
    @Order(2)
    public SecurityFilterChain appSecurityFilterChain(HttpSecurity http) throws Exception {
        http.formLogin().and().authorizeHttpRequests().anyRequest().authenticated();

        return http.build();
    }


    @Bean
    public UserDetailsService uds() {
        var uds = new InMemoryUserDetailsManager();
        var u = User.withUsername("john").password("12345").authorities("read").build();
        uds.createUser(u);
        return uds;
    }

    @Bean
    public PasswordEncoder passwordEncoder() {
        return NoOpPasswordEncoder.getInstance();
    }
}
```
Now, we have setup Authorization server to authenticate our users.

But remember? we needed to register a client (our application) to GitHub authorization server in chapter 12. Now, we are setting up our own authorization server. So, we also need to setup a client with client_id, client_secret. This client will get the protected resource on behalf of the user.
