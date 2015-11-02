# The Meta-pipe Authorization service

## Introduction

In order to limit which users are authorized to access and modify datasets and job results we need a mechanism for
authentication and authorization. Future goals are to integrate with Elixir AAI and possibly also Feide. We also
needed a form of ad-hoc authentication for our own services.

We were looking fore something simple and developer friendly that could be easily incorporated in any framework/programming
language combination as we have developers that enjoy programming in Scala and Go and sometimes also use Java.

The ideal solution seemed to be an external application that is completely decoupled from the rest of 
our services that could contain all the integration code needed to integrate with these external auth services
and that could be developed at its own pace. It would also be desireable if the knowledge required by applications
was small enough that the integration could be implemented from scratch with minimal efforts. It's also good to follow standards
since this means that we can re-use existing libraries and use proven, stable interfaces.


Key challenges: user agent interaction, session invalidation (FEIDE). Remove burden from frameworks or developer knowledge.


Future integration:
- Elixir AAI
- Feide (maybe)

### Key ideas

- KISS: Keep it Simple and Stupid. Especially wrt. creating new services.
- Keep authentication and authorization separate from the services
- Stable interface: We don't want to do the same work multiple times. This motivates the use of existing standards in the
    interaction between services as these are assumed to be reasonably defined and stable.


## Background
In this section we will explain the different standards and specifications that are used by the authorization service.
Since each of these standards by themselves can be tens of pages we have chosen to only summarize the parts that are
most relevant to understand our implementation and future work.

### Definitions
*Token Introspection* will refer to the protocol defined in *OAuth 2.0 Token Introspection (draft-ietf-oatuh-introspection-10)* 

The following terms are defined as in RFC-6749 and is used with capital first letter where ever they appear in this document:

- Token Endpoint
- Resource Server
- Access Token
- Refresh Token

From RFC-7521:
- Issuer


### The OAuth 2.0 Authorization Framework (RFC-6749)
The OAuth 2.0 Authorization Framework is a standard (and relates to a set of standards) for performing authorization.
A key abstraction in OAuth 2 is the *Acces Token*, which provides an "abstraction layer, replacing different
authorization constructs (e.g., username and password) with a single token understood by the resource server."[rfc6749]

An Access Token is obtained by the Client by contacting the *Token Endpoint* while providing an *Authorization Grant*
or a Refresh Token. An example of an Authorization Grant is the *Resource Owner Password Credentials Grant*.
In this case the Client will contact the Token Endpoint and provide the Resource Owner's username and password.
The Token Endpoint will then, upon success, reply with an Access Token and (optionally) a refresh token.


todo: authorization code grant 
 
#### Scopes
A key property of OAuth 2.0 is that an Authorization Server can issue Access Tokens that are "more restrictive than the
authorization grant used to obtain them" [rfc6749] This can be achieved by using a *Scope*. An Access Token Scope can
be requested when obtaining an Access Token and is represented as a text string that is defined by the Authorization Server.


### OAuth 2.0 Bearer token usage (RFC-6750)
In the OAuth 2.0 standard (RFC-6740) the Access Token is defined as a string that is "usually opaque to the client"[rfc6749].
It does not, however, define the format of the Access Token or how it should be used to access a resource. This is defined by related standards.
*The Oauth2.0 Authorization Framework: Bearer Token Usage* (RFC-6750) defines the Bearer Token which is a kind of access
token that is passed directly to the Resource Server when performing an authorized request. Resource Servers that support
RFC-6750 must support an HTTP Authorization header with the *Bearer* HTTP authorization scheme. RFC-6750 also optionally
supports the use of an "access_token" query parameter that contains an OAuth 2.0 Bearer token to be used by the Client
instead of the Authorization header. While this is discuraged for security reasons as it may end up in log files we
have found a case that justifies its use (download links for datasets).

### JSON Web token (JWT) (RFC-7519)
JSON Web token is "a compact, URL-safe means of representing claims to be transferred between two parties" [rfc7519].
From the standard: "JWTs represent a set of claims as a JSON object that is encoded in a JWS and/or JWE structure. This
JSON object is the JWT Claims Set" [rfc7519]. JWT defines a set of standard claims but can be extended.

todo: example

### OAuth 2.0 Token Introspection (draft-ietf-oatuh-introspection-10)
While OAuth 2.0 Bearer Token Usage (RFC-6750) defines how a Client can make authorized requests to a Resource Server
on behalf of a Resource Owner using a Bearer token, it does not define how the Resource Server should validate the
Bearer token and decide if the request is authorized. The *OAuth 2.0 Token Introspection* (IETF working draft) defines
a method for inspecting a token.

When a Resource server introspects a Bearer Token it will issue a request to the Token Introspection Endpoint. The request
will contain the token to be introspected and the Token Endpoint will return a JWT Claim. The JWT Claim will, in
addition to the standard JWT claims, contain some more extra fields. Some of these fields are:
 - *valid* (required): A boolean value indicating if the token is valid
 - *scope* (optional): The OAuth 2.0 scope associated with the token.

### Security Assertion Markup Language (SAML) 2.0 Profile for OAuth 2.0 Client Authentication and Authorization Grants (RFC-7522)


### Nels/Galaxy/Feide integration
At UiT we have a Galaxy instance that is integrated with Feide for authentication. The integration consists of an
Apache HTTP reverse proxy with AuthMemcookie and SimpleSAMLPHP. The Apache HTTPD proxies authenticated requests to
Galaxy with an HTTP header used to indicate who is logged in.


### Galaxy Tool invocation
A Galaxy tool is defined by an XML file that describes how to launch a process and how the process' arguments should
be presented in the Galaxy GUI. The tool developer maps every relevant parameter to a command line that will be
executed by Galaxy when the user runs the tool. Included parameters are those that the user can select in the web GUI 
as well as a few contextual parameters, like the user's email address. In our case this email address would correspond
to the one that comes from Feide when the user authenticates.

## Libraries and frameworks

### Apache Oltu
Apache Oltu is a library for implementing OAuth 2.0 servers in Java. It manages the OAuth 2.0 protocol and helps serializing
responses and parsing/validating requests.

### mulesoft/js-client-oauth2
Client Oauth 2 is a JavaScript library for obtaining an authorization from an OAuth 2.0 Authorization server and
for making authorized requests to a Resource Server. Client OAuth 2 supports several Authorization Grants including the
Resource Owner Password Credentials grant and it also supports Bearer Token usage (RFC-6750). Client OAuth 2 is usable
both from a web browser as well as from within NodeJS.

This library also supports the 
Authorization Code Grant.

### Dropwizard + Hibernate
Dropwizard is a light weight Java web framework aimed at creating micro services.
Hibernate is an ORM (Object Relational Mapper). Dropwizard is easy to integrate with Hibernate and Apache Oltu.

## Design

The authorization service need to fit into the context of Meta-pipe. In this context there are the following
Clients: the web application and the Galaxy tool. In addition there are several services including storage and
job management services. In addition to this we have a future goal of integrating with external IDPs such as the
Elixir AAI and possibly also Feide, the Norwegian federated IDP for educational institutions.

Since the Elixir AAI was not ready at the time that we began our implementation we also required an ad-hoc way of
registering and signing in users. Because of this we maintain our own user database.

Since Feide requires single sign-out we needed a way to revoke tokens on a short notice. This motivates a central
service for validating tokens and is one of the reasons we chose to use Token Introspection over JWT Bearer Tokens (RFC-7523).


The authorization server implements the *Resource Owner Password Credentials Grant* flow of the OAuth 2.0 Authorization
Framework (RFC-6749) as this was the easies flow to implement in both the authorization server and our Meta-pipe web
application. It uses OAuth 2.0 Bearer Tokens (RFC-6750) as the access tokens since the Bearer Tokens are well supported
by software libraries and are also required by the Token Introspection specification. In addition the server implements
an OAuth 2.0 Token Introspection (draft-ietf-oatuh-introspection-10) endpoint that the Resource Servers
can use to introspect the tokens, whereby getting information about what can be accessed and if the token is still valid.

Our Meta-pipe web application stores the Access Tokens and Refresh Tokens in the browser's local storage.

In OAuth 2.0 the Access Token Scope is defined by the Authorization server. Since we are using a REST architecture for
our services each resource has its own URI. We define the Access Token Scope such that it's possible for a Resource
Server to determine if a request is authorized by comparing the Access Token Scope with the requested URI and method.
An example scope would be `GET,PUT|storage/USERNAME/` or `GET|storage/USERNAME/uploaded_dataset.fastq`.


### Request flows for specific use cases

This section describes how the authorization server are used in different scenarios. Some of these are already
implemented while others are not. The login flows may need to change when integrating with other IDPs (such as AAI or Feide) as some IDPs,
including Feide, require direct interaction with the end user's web browser. These changes are intended to be contained
to the authorization server and the Clients, whereas the interactions between a Client and a Resource Server is intended
to be stable throughout these changes.

#### User login via Meta-pipe web application

As mentioned above this flow may need to change when integrating with other IDPs. See *Future work* for details.

- The end user (Resource Owner) enters a valid username/password in the Meta-pipe web application (Client)
- A POST request is made by the web app to the Token Endpoint which returns an Access Token and a Refresh Token
 (as defined in *Resource Owner Password Credentials Grant* in RFC-6749).
- The web app will persist this information to the browser's local storage.


#### Galaxy tool "login" flow (not yet implemented)

When the Meta-pipe Galaxy tool (Client) is called by Galaxy the end user will already be authenticated using the Galaxy/Feide integration.

- The Galaxy tool will get the user's email address as a command line parameter.
- The Galaxy tool will be trusted to access all users data and will obtain an Access Token and a Refresh Token using the
*Client Credentials Grant* (RFC-6749) with a Scope that is limited to the requesting user's resources
that are nessesary to run a job.
- The Galaxy Tool will hold the Access Token and Refresh Token in memory for subsequent API requests until the job has completed
and the tool terminates. No authorization state will be persisted between multiple tool invocations.

#### API requests to a Resource Server

- The Client will provide the Bearer Access Token in the Authorization header to the API endpoint (RFC-6750).
- When the Resource Server receives the request it will query the Introspection Endpoint to get the Scope of the
Access Token and verify that it's still valid.
- If the Access Token is valid the Resource Server will compare the Access Token' Scope (as returned by the Introspection
Endpoint) to the scope required to process the request. If the request is authorized it will process the request,
otherwise it will respond with an appropriate error code (RFC-6750).

#### Browser downloading of datasets (not fully implemented - WIP)

When downloading a data set (result) from our storage service via the browser it is nesessary to provide a direct link
to the downloadable content while at the same time provide an Access Token in order to access the data.
RFC-6750 allows for the use of an "access_token" URI query parameter containing a Bearer Token for authorization. A
potential issue with this approach is that if a user shares a download link with other users they will get the user's
access token. A solution to this is to limit the access token's Scope to only have read access to that particular
resource, thus mitigating the risk of users inadvertently giving access to their account.

### Software components coupling and limitations

#### Authorization Server
The Authorization Server must know about all the IDPs and their protocols and policies.

The Authorization server must also have knowledge about which users are authorized to use which resources.


#### Client
The Client must support OAuth 2.0 (RFC-6749) and Bearer Token Usage (RFC-6750).

#### Resource Server
The Resource server must support Bearer Token Usage (RFC-6750) and OAuth 2.0 Token Introspection (draft-ietf-oatuh-introspection-10).
In addition to this it must also know how to compare a Scope to a URI/method using the scheme described in this document.


## Implementation

The authorization service is implemented in the Drowpwizard web framework and it uses Apache Oltu for handling the OAuth protocol.
It stores all its state in a PostgreSQL database using Hibernate. The code is currently 2039 lines of Java, including 562 lines of tests.

The Meta-pipe web application (Client) is implemented in JavaScript and uses the Client OAuth 2.0 library for interfacing
with the authorization server and the Resource Servers.

OAuth 2.0 Token Introspection is not yet widely supported by software libraries and so we had to implement this from
scratch. The specification is only 17 pages and it's very easy to implement. Our library implementation for Resource
Servers is only 176 lines of Go.

## Future work

### Integrating with IDPs that require end user web browser interaction

While there are standards like RFC-7522 *(Security Assertion Markup Language (SAML) 2.0 Profile for OAuth2.0 Client Authentication and Authorization Grants)*
and the more general RFC-7521 *(Assertion Framework for OAuth 2.0 Client Authentication and Authorization Grants)* these require the Client
to directly interact with the Issuer which means that both the Client and the Authorization server will need to know
about protocols like SAML and also know how to interact with the concrete IDP. For Meta-pipe we wish to encapsulate these
integrations in the authorization server so that we can implement our Clients without them having to specifically know about
these integrations.

We believe that the OAuth 2.0 *Authorization Code Grant* is flexible enough to be suitable for this purpose.

The Authorization Code Grant works by having the client redirect the User Agent to the Authorization Endpoint and then, after the Resource
Owner has authorized the request, the User Agent is redirected to a URI defined by the Client where an Authorization Code
is included in the URI query parameter. The Client can then exchange this Authorization Code for an Access Token and a
Refresh Token.

While RFC-6749 specifies how the Client interacts with the Authorization Server it does not specify how
the Authorization Endpoint obtains an authorization from the Resource Owner - this is left to the implementer of the
Authorization Endpoint. This means
that once the User Agent has been redirected to the Authorization Endpoint we can perform the authentication process that
is required by the specific IDP (including any Use Agent interactions/redirects) so long as we are able to redirect the
User Agent to the URI specified by the Client after the user has been authenticated.

A benefit of the Authorization Code Grant is that it has very good software library support.


Related: RFC-7522 (based on RFC-7521).


## Related work
OpenID Connect, no out-of-the-box solution that fit the bill, large standard with lots of mandatory stuff.
JWT Bearer tokens (and why not (Feide single sign-out))


## References

- [rfc6749]  The OAuth 2.0 Authorization Framework https://tools.ietf.org/html/rfc6749
- [rfc6750] The OAuth 2.0 Authorization Framework: Bearer Token Usage
- [introspection] OAuth 2.0 Token Introspection
- [rfc7522] Security Assertion Markup Language (SAML) 2.0 Profile for OAuth2.0 Client Authentication and Authorization Grants
Assertion Framework for OAuth 2.0 Client Authentication and Authorization Grants
- [rfc7519] JSON Web Token
- [openid] OpenID Connect Core 1.0 incorporating errata set 1
- [feideint] Feide Integration Guide
- [feidetech] Feide Technical Guide
- [rfc7523] JSON Web Token (JWT) Profile for OAuth 2.0 Client Authentication and Authorization Grants https://tools.ietf.org/html/rfc7523