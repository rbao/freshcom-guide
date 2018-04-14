# JavaScript SDK Reference

## Introduction

## Commonly Used Object

### Fields

The Freshcom API only accepts request body in JSON API format, however this format may not be the most convenient format to use for your client side application. This is why by default most method in the JavaScript SDK accept a unserialized object containing all the fields of a resource and will attempt to serialize it to JSON API format and then send the request using the serialized version of it. This unserialized object that the Javascript SDK accept is what we call the fields object. The fields object is a plain JavaScript object containing all the fields of a resource.

If the property of the fields object matches the following condition then those property will be serialized as the relationships of the resource:

* the property's value is an `Object` with a `type` property; or
* the property's value is an `Array` 

All other property will be serialized as the attributes of the resource. Below is an example of a valid fields object and its the JSON object after serialization.

{% tabs %}
{% tab title="Fields Object" %}
```javascript
{
  type: 'Product',
  kind: 'simple',
  name: 'Red Starship',
  goods: {
    id: '9fc8ee53-906f-48bd-a392-8b0709301699',
    type: 'Stockable'
  }
}
```
{% endtab %}

{% tab title="Serialized" %}
```javascript
{
  "data": {
    "type": "Product",
    "attributes": {
      "kind": "simple",
      "name": "Red Starship"
    },
    "relationships": {
      "goods": {
        "data": {
          "id": "9fc8ee53-906f-48bd-a392-8b0709301699",
          "type": "Stockable"
        }
      }
    }
  }
}
```
{% endtab %}
{% endtabs %}

By default, all SDK methods that accepts a fields object will automatically serialize it before sending the request to the API, if you do not want the SDK to serialize the data for you and want to handle the serialization yourself then you can set `serializeFields` to `false` in the options object, in this case the SDK will treat the fields object as if it is already serialized.

### Response

The Freshcom API only return response body in JSON API format, however this format may not be the most convenient format to use for your client side application. This is why by default response returned from the JavaScript SDK will automatically deserialize the data. The SDK will only serialize the data and errors section of the response, meta and other sections if any will not be deserialized.

Below is an example of JSON response from the API containing a data section and its corresponding deserialized response.

{% tabs %}
{% tab title="API Response" %}
```javascript
{
  "meta": {
    "locale": "en"
  },
  "data": {
    "id": "74b901b2-32f9-4e3b-8d4d-4eca2360550c",
    "type": "Product",
    "attributes": {
      "kind": "simple",
      "name": "Red Starship"
    },
    "relationships": {
      "goods": {
        "data": {
          "id": "9fc8ee53-906f-48bd-a392-8b0709301699",
          "type": "Stockable"
        }
      }
    }
  }
}
```
{% endtab %}

{% tab title="Deserialized Response" %}
```javascript
{
  meta: {
    locale: 'en'
  },
  data: {
    id: '74b901b2-32f9-4e3b-8d4d-4eca2360550c',
    type: 'Product',
    kind: 'simple',
    name: 'Red Starship',
    goods: {
      id: '9fc8ee53-906f-48bd-a392-8b0709301699',
      type: 'Stockable'
    }
  }  
}
```
{% endtab %}
{% endtabs %}

Below is an example of JSON response from the API containing a errors section and its corresponding deserialized response.

{% tabs %}
{% tab title="API Response" %}
```javascript
{
  "errors": [
    {
      "code": "required",
      "source": {
        "pointer": "/data/attributes/name"
      },
      "title": "Name is required"
    }
  ]
}
```
{% endtab %}

{% tab title="Deserialized Response" %}
```javascript
{
  errors: {
    name: [{code: "required", title: "Name is required"}]
  }
}
```
{% endtab %}
{% endtabs %}

By default, all SDK methods returns response object will automatically deserialize the appropriate sections of the response. If you do not want the SDK to deserialize the data for you and want to handle the deserialization yourself, then you can set `deserializeResponseData` or `deserializeResponseErrors` to `false` in the options object. If you just want to keep to the raw response together with the deserialize response then you can set `keepRawResponse` to `true`, and the raw response will be included in the deserialized response and you can access it using `response.raw`.

### Options

Most SDK methods accepts an optional options object with the following property:

* `serializeFields` `Boolean` \(default: `true`\) - If the methods as a fields object indicate whether to serialize the fields or not.
* `deserializeResponseData` `Boolean` \(default: `true` \) - If the method returns a response or a `Promise` that resolves to a response indicate whether to deserialize the response data or not.
* `deserializeResponseErrors` `Boolean` \(default: `true`\) - If the method returns a response or a `Promise` that resolves to a response indicate whether to deserialize the response errors or not.
* `keepRawResponse` `Boolean` \(default: `false`\) - If the method returns a deserialized response or a `Promise` that resolves to a deserialized response indicate whether to keep the raw response.

## Authentication

### .createAccessToken

`freshcom.createAccessToken(payload)`

Create a access token using the provided username and password, or refresh token.

{% tabs %}
{% tab title="Arguments" %}
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

### .setAccessToken

`freshcom.setAccessToken(accessToken`

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

### .setRefreshToken

`freshcom.setRefreshToken(refreshToken)`

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

## Internationalization

### .setDefaultLocale

`freshcom.setDefaultLocal(defaultLocale)`

Sets the default locale to use for all subsequent request. Locale can still be overwritten using the params arguments for specific methods.

{% tabs %}
{% tab title="Arguments" %}
* `defaultLocale` `String`  - The default locale to use.
{% endtab %}

{% tab title="Returns" %}
Returns `undefined`.
{% endtab %}

{% tab title="Usage" %}
```javascript
import freshcom from 'freshcom-sdk'

freshcom.setDefaultLocale('zh-CN')
```
{% endtab %}
{% endtabs %}

## Identity

### .retrieveAccount

`freshcom.retrieveAccount([params, options])`

### .updateAccount

`freshcom.updateAccount(fields, [params, options])`

### .retrieveCurrentUser

`freshcom.retrieveCurrentUser([params, options])`

### .updateCurrentUser

`freshcom.updateCurrentUser(fields, [params, options])`

### .retrieveUser

`freshcom.retrieveUser(identifiers, [params, options])`

### .updateUser

`freshcom.updateUser(identifiers, fields, [params, options])`

### .createEmailVerificationToken

`freshcom.createEmailVerificationToken(fields, [params, options])`

### .createEmailVerification

`freshcom.createEmailVerification(fields, [params, options])`

### .createPhoneVerificationCode

`freshcom.createPhoneVerificationCode(fields, [params, options])`

### .createPasswordResetToken

`freshcom.createPasswordResetToken(fields, [params, options])`

### .updatePassword

`freshcom.updatePassword(identifiers, fields, [params, options])` 

## Storefront

### .listOrder

`freshcom.listOrder([params, options])`

### .createOrder

`freshcom.createOrder([fields, params, options])`

### .retrieveOrder

`freshcom.retrieveOrder(identifiers, [params, options])`

### .updateOrder

`freshcom.updateOrder(identifiers, fields, [params, options])`

### .deleteOrder

`freshcom.deleteOrder(identifiers, [params, options])`

### .listOrderLineItem

`frescom.listOrderLineItem([params, options])`

### .createOrderLineItem

`freshcom.createOrderLineItem(fields, [params, options])`

### .updateOrderLineItem

`freshcom.updateOrderLineItem(identifiers, fields, [params, options])`

### .deleteOrderLineItem

`freshcom.deleteOrderLineItem(identifiers, [params, options])`

## Catalogue

### .listProduct

`freshcom.listProduct([params, options])`

### .createProduct

`freshcom.createProduct(fields, [params, options])`

### .retrieveProduct

`freshcom.retrieveProduct(identifiers, [params, options])`

### .updateProduct

`freshcom.updateProduct(identifiers, fields, [params, options])`

### .deleteProduct

`freshcom.deleteProduct(identifiers, [params, options])`

### .listProductCollection

`freshcom.listProductCollection([params, options])`

### .createProductCollection

`freshcom.createProductCollection(fields, [params, options])`

### .retrieveProductCollection

`freshcom.retrieveProductCollection(identifiers, [params, options])`

### .updateProductCollection

`freshcom.updateProductCollection(identifiers, fields, [params, options])`

### .deleteProductCollection

`freshcom.deleteProductCollection(identifiers, [params, options])`

### .listProductCollectionMembership

`freshcom.listProductCollectionMembership(identifiers, [params, options])`

### .createProductCollectionMembership

`freshcom.createProductCollectionMembership(fields, [params, options])`

### .updateProductCollectionMembership

`freshcom.updateProductCollectionMembership(identifiers, fields, [params, options])`

