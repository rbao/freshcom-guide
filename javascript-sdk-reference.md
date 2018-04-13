# Javascript SDK Reference

## Introduction

## Commonly Used Object

### Fields

The Freshcom API serializes data to conform with JSON API format, however the serialized format is not the most convenient format to use for your client side application so the Freshcom SDK accept a unserialized object and will attempt to serialize it following a few conventions. 

The fields object is a plain javascript object with one requirement: it must have a `type` property and the value of it must be a `String`. 

### Response

### Options

## Authentication

### .createAccessToken\(payload\)

Create a access token using the provided username and password, or refresh token.

{% tabs %}
{% tab title="First Tab" %}
* `payload` `Object` - The payload, see below for details.
* `payload.username` `String` - The username to use.
* `payload.password` `String` - The password to use.
* `payload.refreshToken` `String` - The refresh token to use.
* `payload.grantType` `String` - Can be either `password` or `refresh_token`.
* `payload.otp` `String` - The one time password to use if using two factor authentication.
{% endtab %}

{% tab title="Returns" %}
Returns a `Promise` that resolved to a `Response` object if successful.
{% endtab %}
{% endtabs %}

### .setAccessToken\(accessToken\)

Sets the access token to use for all subsequent request.

{% tabs %}
{% tab title="Arguments" %}
* `accessToken` `String` - The access token to set.
{% endtab %}

{% tab title="Returns" %}
Returns `undefined`.
{% endtab %}

{% tab title="Usage" %}
```javascript
import freshcom from 'freshcom-sdk'

freshcom.setAccessToken('{access_token}')
```
{% endtab %}
{% endtabs %}

### .setRefreshToken\(refreshToken\)

Sets the refresh token to use when attempting to refresh the access token. If a refresh token is set then whenever a 401 is received from the API, the SDK will try to refresh the access token and and re-attempt the request. This will only happen once, if the re-attempt failed or if refreshing the access token failed then it will not be retried again.

{% tabs %}
{% tab title="Arguments" %}
* `refreshToken` `String` - The refresh token to set
{% endtab %}

{% tab title="Returns" %}
Returns `undefined`.
{% endtab %}

{% tab title="Usage" %}
```javascript
import freshcom from 'freshcom-sdk'

freshcom.setAccessToken('prt-test-827ae785-1502-4489-8a97-609c4840168')
```
{% endtab %}
{% endtabs %}



