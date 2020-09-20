---
title: Spring Security revisit
date: 2020-09-19T06:46:40.077Z
---
# Authentication

`Authentication` represents the token of an authentication request.

By default, these authentication token implementation types:
- AnonymousAuthenticationToken
- UsernamePasswordAuthenticationToken
- PreAuthenticatedAuthenticationToken
- RememberMeAuthenticationToken
- JaasAuthenticationToken
- RunAsUserToken

# Authentication provider
A provider to handle the authentication when authentication token type is matched:


| Authentication providers                                                                         | Supported authentication            |
|--------------------------------------------------------------------------------------------------|-------------------------------------|
| AnonymousAuthenticationProvider                                                                  | AnonymousAuthenticationToken        |
| AbstractUserDetailsAuthenticationProvider DaoAuthenticationProvider RemoteAuthenticationProvider | UsernamePasswordAuthenticationToken |
| AbstractJaasAuthenticationProvider DefaultJaasAuthenticationProvider JaasAuthenticationProvider  | JaasAuthenticationToken             |
| PreAuthenticatedAuthenticationProvider                                                           | PreAuthenticatedAuthenticationToken |
| RememberMeAuthenticationProvider                                                                 | RememberMeAuthenticationToken       |
| RunAsImplAuthenticationProvider                                                                  | RunAsUserToken                      |


Here, AbstractUserDetailsAuthenticationProvider and its subclasses expect a `UserDetailsService` provided by us.
RememberMeAuthenticationProvider expects a `RememberMeServices` provided by us.

# SecurityFilterChain and FilterChainProxy
A filter chain is a list of filters with the order to perform the filtering task. They are defined as Spring FilterBean.

![](https://github.com/spring-guides/top-spring-security-architecture/raw/master/images/security-filters.png)

And the FilterChainProxy can include multiple filter chains,

![](https://github.com/spring-guides/top-spring-security-architecture/raw/master/images/security-filters-dispatch.png)

FilterChainProxy is a servlet filter to perform the filter chains.

Irrespective of which filters you are actually using, the order should be as follows:

- ChannelProcessingFilter, because it might need to redirect to a different protocol

- SecurityContextPersistenceFilter, so a SecurityContext can be set up in the SecurityContextHolder at the beginning of a web request, and any changes to the SecurityContext can be copied to the HttpSession when the web request ends (ready for use with the next web request)

- ConcurrentSessionFilter, because it uses the SecurityContextHolder functionality and needs to update the SessionRegistry to reflect ongoing requests from the principal

- Authentication processing mechanisms - UsernamePasswordAuthenticationFilter, CasAuthenticationFilter, BasicAuthenticationFilter etc - so that the SecurityContextHolder can be modified to contain a valid Authentication request token

- The SecurityContextHolderAwareRequestFilter, if you are using it to install a Spring Security aware HttpServletRequestWrapper into your servlet container

- The JaasApiIntegrationFilter, if a JaasAuthenticationToken is in the SecurityContextHolder this will process the FilterChain as the Subject in the JaasAuthenticationToken

- RememberMeAuthenticationFilter, so that if no earlier authentication processing mechanism updated the SecurityContextHolder, and the request presents a cookie that enables remember-me services to take place, a suitable remembered Authentication object will be put there

- AnonymousAuthenticationFilter, so that if no earlier authentication processing mechanism updated the SecurityContextHolder, an anonymous Authentication object will be put there

- ExceptionTranslationFilter, to catch any Spring Security exceptions so that either an HTTP error response can be returned or an appropriate AuthenticationEntryPoint can be launched

- FilterSecurityInterceptor, to protect web URIs and raise exceptions when access is denied

For a minimal http security config, these are the filters:
```text
Security filter chain: [
  WebAsyncManagerIntegrationFilter
  SecurityContextPersistenceFilter
  HeaderWriterFilter
  CsrfFilter
  LogoutFilter
  RequestCacheAwareFilter
  SecurityContextHolderAwareRequestFilter
  AnonymousAuthenticationFilter
  SessionManagementFilter
  ExceptionTranslationFilter
  FilterSecurityInterceptor
]
```

Let's see how authentication mechanism is supported by the required filters:

- form login

use UsernamePasswordAuthenticationToken

```text
Security filter chain: [
  WebAsyncManagerIntegrationFilter
  SecurityContextPersistenceFilter
  HeaderWriterFilter
  CsrfFilter
  LogoutFilter
  UsernamePasswordAuthenticationFilter ⇦
  DefaultLoginPageGeneratingFilter     ⇦
  DefaultLogoutPageGeneratingFilter    ⇦
  RequestCacheAwareFilter
  SecurityContextHolderAwareRequestFilter
  AnonymousAuthenticationFilter
  SessionManagementFilter
  ExceptionTranslationFilter
  FilterSecurityInterceptor
]
```

- http basic auth

use UsernamePasswordAuthenticationToken
```
Security filter chain: [
  WebAsyncManagerIntegrationFilter
  SecurityContextPersistenceFilter
  HeaderWriterFilter
  CsrfFilter
  LogoutFilter
  BasicAuthenticationFilter   ⇦
  RequestCacheAwareFilter
  SecurityContextHolderAwareRequestFilter
  AnonymousAuthenticationFilter
  SessionManagementFilter
  ExceptionTranslationFilter
  FilterSecurityInterceptor
]
```
- remember me

use RememberMeAuthenticationToken
```
Security filter chain: [
  WebAsyncManagerIntegrationFilter
  SecurityContextPersistenceFilter
  HeaderWriterFilter
  CsrfFilter
  LogoutFilter
  RequestCacheAwareFilter
  SecurityContextHolderAwareRequestFilter
  RememberMeAuthenticationFilter     ⇦ 
  AnonymousAuthenticationFilter
  SessionManagementFilter
  ExceptionTranslationFilter
  FilterSecurityInterceptor
]
```
- oauth2 login

OAuth2LoginAuthenticationFilter

- openid login

OpenIDAuthenticationFilter

- JWT 

You define you filter to insert into the filter chain (typically between LogoutFilter and RequestCacheAwareFilter):
- extract JWT  
- verify it
- set SecurtyContext if any

- Custom authentication mechanism

Probably you should define authentication provider and your filters (maybe multiple) to support the flow.

# Important Filters

## ExceptionTranslationFilter

This filter is normally registered at the very bottom. And what it is doing to catch errors thrown in filters after it (FilterSecurityInterceptor), and handling them. By default, it is using `Http403ForbiddenEntryPoint` and `AccessDeniedHandlerImpl` where it calls `sendError()` which is a redirection to WhiteLabel error page `/error` and show generic JSON/HTML page.

You can define `AuthenticationEntryPoint` and `AccessDeniedHandler` to customise the error handling.

In general, this filter is not that useful, as its order is too high, so that the errors thrown before it cannot be caught and by default directly handled by WhiteLabel error page.

## FilterSecurityInterceptor

This filter is the last gate to check Authentication and apply authorizeRequest rules.

For example, if you config `.anyRequest().authenticated()`, it will check if `Authentication` is null and `authenticated` is true, etc.

From the design of Spring Security, normally it doesn't hope you throw exception in your authentication mechanism, instead you only need to provide `Authentication` object and populate its fields.




