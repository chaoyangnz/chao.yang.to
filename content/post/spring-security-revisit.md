---
title: Spring Security revisit
date: 2020-09-19T04:33:55.233Z
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
| Authentication providers                                                                         | Supported authentication            |   |   |
|--------------------------------------------------------------------------------------------------|-------------------------------------|---|---|
| AnonymousAuthenticationProvider                                                                  | AnonymousAuthenticationToken        |   |   |
| AbstractUserDetailsAuthenticationProvider DaoAuthenticationProvider RemoteAuthenticationProvider | UsernamePasswordAuthenticationToken |   |   |
| AbstractJaasAuthenticationProvider DefaultJaasAuthenticationProvider JaasAuthenticationProvider  | JaasAuthenticationToken             |   |   |
| PreAuthenticatedAuthenticationProvider                                                           | PreAuthenticatedAuthenticationToken |   |   |
| RememberMeAuthenticationProvider                                                                 | RememberMeAuthenticationToken       |   |   |
| RunAsImplAuthenticationProvider                                                                  | RunAsUserToken                      |   |   |


Here, AbstractUserDetailsAuthenticationProvider and its subclasses expect a `UserDetailsService` provided by us.
RememberMeAuthenticationProvider expects a `RememberMeServices` provided by us.

# SecurityFilterChain and FilterChainProxy
A filter chain is a list of filters with the order to perform the filtering task. They are defined as Spring FilterBean.

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




