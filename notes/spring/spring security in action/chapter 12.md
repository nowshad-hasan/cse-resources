We can learn OAuth 2 in details from this book `OAuth2 In Action` by Justin Richer and Antonio Sanso

### Chapter 12: How Does OAuth 2 work?

OAuth 2 is a framework. It is called `Authorization framework` (Or, a specification framework). Its sole purpose is to give third party applications to give access on my resources. That's why sometimes, people also call it `Delegation Protocol`.

But why do we need to move on to OAuth 2? Are not we happy with Basic Auth? No! Basically for some reasons.

- We need to send username, password for every requests (like in GTech). And client needs to store it in their js/ html/ android application to send this every time. That makes the application vulnerable.
- In every request, username and password is passing. So, if someone sniffs in the network, he will get it. So, we need something more rock solid approach for authentication.
- Let's say we are in a large organization like BRAC, where multiple projects are running. So, if we have different username, password for different applications, that is a mess. We should have a single username, password and that will help us to login in every system. It's called SSO - single sign on. Every applications will get the authentication (session) from a single SSO server. That's how managing the authentication and authorization becomes more simpler.

To understand OAuth, we need to be familiar with some terminology.

**Resource Owner (User)**: Its the user who has (own) the resource.
**Authorization server**: The central server who authenticates a user by his username, password.
**Resource server**: Its the server where user's resource resides.
**Client**: It's an interesting one. It is the third party app which tries to get the resource from resource server authenticating the user by the authorization server.

**Note**: Sometimes, resource and authorization servers are same company/ application.

Actually what happens in OAuth 2 is: Let's say I have a bank account (Resource server) where my transactions are made. But this bank is awful at reporting. But another trusted company (client) can make a beautiful report with transactions. So, what we can do, get the transactions from the bank, then give it to the client, so that client can make the report. But it is tedious for the user to manually make it. To automate the process, we can give the client my username, password - so that they can go to the bank, get the transactions. Sounds stupid? Yes, that's true. We never ever gonna give them our credentials. But then how can the client get the access to my transactions. Actually how they read my transactions, without writing or making a new one. Okay, we can go to the bank and tell the manager my problem, then he can get another idea. The bank can make an *access card* for the client to read my transactions after my successful authentication in their server (Authentication server). They give the access card to me, I can give it to the client. Then, next day client will go the bank with my access card and their credentials. Yes, client needs to have a account in the bank (not like a bank account, that could be a separate types of account for clients like them), so that bank knows they are not fraud or something. After giving my access card to the bank, bank will recognize it and provide my transactions. Peace! Now, they will make a report and give that to me.

#### The choices to implement OAuth

So, in language of OAuth, we can say - with a valid access token, client can access user's resource.
But how can client access the token? There are some options called *grant*. Here are some grants.

- Authorization code
- Password
- Client credentials
- Refresh token

Let's go into details about these.

##### Authorization Code

Here client redirects the user to authentication server, get authorization code access token and afterwards call for the protected resource with access token. High level overview - 

- Authentication request
- Get access token
- Call for protected resource
Let's go to details about these.

**Step 1**: Making authentication request with authorization code grant type

Client redirects the user to the authorization endpoint with below queries - 
- *response type*: let's assume the value is `code`. That means client expects a code in the return value
- *client_id*: client's information registered in the authorization server. (Client needs to register themselves first in the authorization server)
- *redirect_uri*: The redirect url, authorization server needs to redirect after authentication success or failure. But sometimes, at the time of client's registration, they give their default redirect url. So, authorization server already knows where to redirect on that case.
- *scope*: Granted authority, like - read, write
- *state*: a CSRF token which will be verified later after user's authentication

After successful authentication, the authorization server directs to the redirect_uri with `code` and `state`. Code is used to get access token, and state is used to verify that this redirection is not tempted by someone. It's gonna be checked by client again.

**Step 2**: Get access token with authorization code

Now, client will call to authorization server again with their credentials to get the access token.

*Note*: Some might say why client needs to call again for access token. Why not authorization server gives back the access token to the client directly in Step 1? Yes, that flow is called Implicit Grant Type. But many authorization server does not support this. Because it is unsafe and less secure. So, in step 2, client needs to verify itself again and auth_code and then it will be given access token.

So, client will call to authorization server with below queries - 

- code: authorization code, given at step 1
- client_id, client_secret
- redirect_uri: same one used at step 1
- grant_type: the flow used by authorization server. Server may use multiple flows, that's why we need to specify this.
  
As a response, authorization server gives back the `access_token`.

**Step 3**: Call the protected resource with access token

Now, client will set the access token in header, and calls the resource server's endpoint.
*Note:* Let's say someone gets the auth_code at first, and also gets the client id, secret. What happens then? He is not able to call for the protected resource. Though, it is very unlikely, but still it can be happened. To mitigate this, PKCE (Proof Key for Code Exchange) is implemented. Here is the paper link - https://www.rfc-editor.org/rfc/rfc7636.

##### Password Grant Type

This is also called *Resource owner credentials grant type*. In this flow, client gets the user's username, password and calls to authorization server directly for an access token. Sounds stupid, right? Why would a user share his credentials? 
It can be happened if client and authorization server are built and maintained by same organisation. Let's assume, microservices are talking to each other behaving like client. Let's take another example. We have a web application, and user enters their credentials for a protected resource. Assume, web application a client, and the authorization server is build by same org. So, user will be wondered if he goes to another web page and all the redirection as we discussed in auth_code grant type section. But the client (the frontend application) can do all the request to authorization server to get the access token instead of the redirection.

Basically two parts for this flow - 

- Request for an access token
- Use the access token to call the resources

**Step 1**: Requesting an access token with password grant type

Client gets the user's credentials and calls the authorization server to get the access token. Clients sends the below params in the requests.

- grant_type: value - password
- client_id, client_secret
- scope: granted authority, like - read
- username, password: user's credentials

In response, client gets the access token

**Step 2**: Call resources server with access token

Client now requests the resource server with token in the request's authorization header.

*Note:* The password grant type is less secured than authorization grant type. Here, user always trusts the client. So, it is advisable to make authorization grant type as first choice and this password grant type as second option.

##### Client credentials grant type

In this flow, no user is connected. Just - client, authorization server and resource server. It's like `password grant type` but just with client. It is useful when client tries to call for a resource where no user is needed, like - kind of API endpoint.

Steps are similar to password grant type.

- Request for an access token
- Use the access token to call the resources
  
**Step 1**: Requesting an access token with client credentials grant type

Client uses its credentials to call the authorization server to get the access token. Clients sends the below params in the requests.

- grant_type: value - client_credentials
- client_id, client_secret
- scope: granted authority, like - read
- 
In response, client gets the access token

**Step 2**: Call resources server with access token

Client now requests the resource server with token in the request's authorization header.

##### Using refresh token to obtain new access token

We need to use refresh token for both authentication_code grant type or password grant type. Let's say our access token never expires, that means it is as powerful as password. It can be stolen anytime. But also let's assume access token expires in a very short time, that means user needs to re-authenticated again by that flow. Even if for password grant type, it is risky to call authorization server with credentials again and again. So, refresh token is always very useful to get the access token (renewing access token) again without user's involvement. 
`refresh_token` will be given by authorization server with the access token. Then, if it expires client will need to call the authorization server again with the refresh token to get a brand new access token. Here are the steps for new access token request.

- grant_type: value-refresh_token
- refresh_token: the refresh token 
- scope: same or less granted authorities
- client_id, client_secret:

In response, client will get new access_token and refresh_token.

-----------------------------

#### The sins of OAuth 2

OAuth 2 is not bullet proof. It's just a framework. Below are some vulnerabilities that can be happened.

- Using CSRF on the client: A CSRF is possible, if application does not implement defense mechanism.
- Stealing client's credentials
- Replaying tokens - someone stoles the access token, and now accessing the resource over and over 
- Token hijacking

#### Implementation of SSO

Here we will use GitHub as both authentication and resource server. Here we will create our client and make use of the authorization server to access the resource of Github's user details.

We need to register our client as an application in GitHub from this page - https://github.com/settings/applications/new

There we need to give our home address - localhost:5050 as redirect url and home url.
Let's add a basic controller - 

```java
 @GetMapping("/")
    public String hello() {
        return "hello.html";
    }
```
Create a hello.html file in resources/static folder

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
    <h1>Hi there!</h1>
</body>
</html>
```
Let's change the default `formLogin()` to `oauth2Login()`. Let's add the code below - 
```java
@Configuration
public class ProjectConfig extends WebSecurityConfigurerAdapter {
    @Override
    protected void configure(HttpSecurity http) throws Exception {
        http.oauth2Login();
        http.authorizeRequests()
                .anyRequest()
                .authenticated();
    }
}
```
What it does, like - httpBasic or formLogin, oauth2Login() just adds another filter named `OAuth2LoginAuthenticationFilter`. Here is the picture below - 

![OAuth2LoginAuthenticationFilter flow](../../images/OAuth2ClientLoginFlow.png)

But, if we start our application, it won't run. Because, user needs to authenticate but we don't specify how to authenticate. We need to establish that GitHub is our Authorization Server. For that, we need to use ClientRegistration contract.
The `ClientRegistration` class needs to be defined below details - 

- client_id, client_secret
- grant type used for authentication
- redirect URI
- scopes

We saw this in theory before. Here is the code - 
```java
 ClientRegistration cr = ClientRegistration.withRegistrationId("github")
                        .clientId("a7553955a0c534ec5e6b")
                        .clientSecret("1795b30b425ebb79e424afa51913f1c724da0dbb")
                        .scope(new String[]{"read:user"})
                        .authorizationUri("https://github.com/login/oauth/authorize")
                        .tokenUri("https://github.com/login/oauth/access_token")
                        .userInfoUri("https://api.github.com/user")
                        .userNameAttributeName("id")
                        .clientName("GitHub")
                        .authorizationGrantType(AuthorizationGrantType.AUTHORIZATION_CODE)
                        .redirectUriTemplate("{baseUrl}/{action}/oauth2/code/{registrationId}")
                        .build();
```
Here lots of things are going on. Below are the details - 

- Authorization URI - The URI, client navigates the user for authentication
- Token URI - The URI, client calls to obtain an access token and a refresh token 
- User info URI - The resources URI, ultimately client wants to access and get details about the user

Here are the details - https://docs.github.com/en/apps/oauth-apps/building-oauth-apps/authorizing-oauth-apps

But, spring-security is smarter than this. It builds a class named `CommonOAuth2Provider` that defines most of the above configurations automatically, like 

- Google
- GitHub
- Facebook
- Okta

So, we can also use that class like below.

```java
ClientRegistration cr =
                CommonOAuth2Provider.GITHUB
                        .getBuilder("github")
                        .clientId("a7553955a0c534ec5e6b")
                        .clientSecret("1795b30b42. . .")
                        .build();
```

But if that class does not have the implementation, we need to manually do this like earlier. And if we need to configure for other values, we also can use this builder.

So, our configuration class now has another method like this.

```java
 private ClientRegistration clientRegistration() {
        return CommonOAuth2Provider.GITHUB
                .getBuilder("github")
                .clientId("a7553955a0c534ec5e6b")
                .clientSecret("1795b30b42. . .")
                .build();
    }
```

No, we are not done yet. It was just a ClientRegistration instance. We basically need a ClientRegistrationRepository as a bean which will handles the ClientRegistration instance and gives back to Spring Context. 

Let's see another diagram below - 

![OAuth2 client registration repository flow](../../images/Oauth2ClientRepository.png)

ClientRegistrationRepository is similar to UserDetailsService. UserDetailsService finds UserDetails by its username, where ClientRegistrationRepository finds ClientRegistration by its registration id. It has in-memory implementation called - InMemoryClientRegistrationRepository similar to InMemoryUserDetailsManager.

So, we can write the code like below -

```java
@Bean
    public ClientRegistrationRepository clientRepository() {
        var c = clientRegistration();
        return new InMemoryClientRegistrationRepository(c);
    }

    private ClientRegistration clientRegistration() {
        return CommonOAuth2Provider.GITHUB.getBuilder("github")
                .clientId("a7553955a0c534ec5e6b")
                .clientSecret("1795b30b425ebb79e424afa51913f1c724da0dbb")
                .build();
    }
```

We need to register the `ClientRegistrationRepository` instance to spring context. 
We can also see another approach.

```java
@Override
    protected void configure(HttpSecurity http) throws Exception {
        http.oauth2Login(c -> {
            c.clientRegistrationRepository(clientRepository());
        });
        http.authorizeRequests()
                .anyRequest()
                .authenticated();
    }
```

Both configurations are okay. But don't mix multiple configuration in a single project.

*Note:* We should not place client_id, client_secret in source code. We should get the values from a vault.


But, apart from this coding style, we can fetch this configuration from a properties file. 

```properties
spring.security.oauth2.client.registration.github.client-id=a7553955a0c534ec5e6b
spring.security.oauth2.client.registration.github.client-secret=1795b30b425ebb79e424afa51913f1c724da0dbb
```
Now, we can only use the below code for running our application. 
```java
 @Override
    protected void configure(HttpSecurity http) throws Exception {
        http.oauth2Login();
        http.authorizeRequests()
                .anyRequest()
                .authenticated();
    }
```

The ClientRegistration and ClientRegistrationRepository will be automatically created by SpringBoot. But, if we use another provider, which is not implemented by Spring Security, we need to specify the configuration like below - 

```properties
spring.security.oauth2.client.provider.myprovider.authorization-uri=<some uri>
spring.security.oauth2.client.provider.myprovider.token-uri=<some uri>
```

This properties file works if we have one or more providers. But let's say we fetch the configuration from DB or other web service, then we have to use the `ClientRegistrationRepository` for customization.

We are almost done configuring our OAuth configuration.
We already know how Spring SecurityContext works. If authentication is successful, it stores the details in Authentication object. For our implementation, it will be `OAuth2AuthenticationToken` instance. So, let's change the controller a little bit.

```java
@Controller
public class MainController {
    private Logger logger = Logger.getLogger(MainController.class.getName());

    @GetMapping("/")
    public String hello(OAuth2AuthenticationToken token) {
        logger.info(String.valueOf(token.getPrincipal()));
        return "hello.html";
    }
}
```

Let's test the application now. At first, let's logout from GitHub. 
after hitting, http://localhost:8080 (our application home page), it will redirect to the GitHub authentication login page. Now, it's gonna ask user to authenticate. After username, password submission, our application below parameters - 

- response_type: code
- client_id
- scope: read:user
- state: CSRF token

And after successful authentication GitHub will ask user if it gives the application to read user's information. If user grants this, then it will redirect the user to our application with this redirect url - 
http://localhost:5050/login/oauth2/code/github?code=e3f4981f9d9c321a39e7&state=-goTYdzxRaC52jLIx7eqScV_lkDQQbzmCR1KHR-it6I%3D

Now, we will see our application's hello page. And other information in application's console like nowshad-hasan.json file.

