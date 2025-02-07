---
title: Integrate With Beyond Identity
sidebar_position: 1
---

This guide describes how to use Beyond Identity for authentication during an OAuth2 authorization flow.

## Prerequisites

 - [Using Beyond Identity for Authentication](../../using-bi-for-auth)
 - [Flutter SDK](overview)

Before calling [`EmbeddedSdk.authenticate()`](overview#authentication), we must authorize using Beyond Identity.

## Authorize With Beyond Identity

### Using the Web

The library follows the best practices set out in [RFC 8252 - OAuth 2.0 for Native Apps](https://tools.ietf.org/html/rfc8252).

This guide uses the [Web Auth for Flutter](https://github.com/LinusU/flutter_web_auth) SDK.

 - Step 1: Configuring the Authenticator Config

Make sure the [Authenticator Config](/docs/v1/platform-overview/authenticator-config#embedded) in the Beyond Identity Console is set to type `Embedded` and that the Invoke URL points to your application with either an App Scheme or a Universal Link.

 - Step 2: Beyond Identity Authorize URL

To start the authorization flow, make a call to `FlutterWebAuth.authenticate()` with the Oauth2 Authorization Request URL you built in the pre-requisite step and a callback url scheme.

```javascript
var result = await FlutterWebAuth.authenticate(
    url: BI_AUTH_URL,
    callbackUrlScheme: CALLBACK_URL_SCHEME,
);
```

 - Step 3: Invoke URL

The result will be a URL with the Invoke URL scheme. You can call [`EmbeddedSdk.authenticate()`](overview#authentication), using the result. You can confirm the validity of the URL with [`EmbeddedSdk.isAuthenticateUrl()`](overview#authenticate-url-validation).

```javascript
var authenticateResponse = await Embeddedsdk.authenticate(result, selectedCredentialId);
```

 - Step 4: Redirect URL

A `redirectURL` is returned from a successful `AuthenticateResponse`. The authorization code and the state parameter are attached to this URL. You can exchange the code for an id token using your Beyond Identity Token Endpoint.

```javascript
// This URL contains authorization code and state parameters
// Exchange the authorization code for an id_token using Beyond Identity's token endpoint.
var code = parseCode(authenticateResponse.redirectUrl)
var token = exchangeForToken(code)
```

#### Full Example

```javascript
selectCredentialId((selectedCredentialId) async {
    var result = await FlutterWebAuth.authenticate(
        url: BI_AUTH_URL,
        callbackUrlScheme: CALLBACK_URL_SCHEME,
    );

    var authenticateResponse = await Embeddedsdk.authenticate(result, selectedCredentialId);

    // This URL contains authorization code and state parameters
    // Exchange the authorization code for an id_token using Beyond Identity's token endpoint.
    var code = parseCode(authenticateResponse.redirectUrl)
    var token = exchangeForToken(code)
});

Future<void> selectCredentialId(Function(String) callback) async {
  // Where you can perform some logic here to select a credential, or
  // present UI to a user to enable them to select a credential.
}
```
