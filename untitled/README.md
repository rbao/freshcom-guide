# Customer

A customer resource is used for keeping track of order related resources. It is important to note that customer and user are two different resources in Freshcom each with their own purpose. Unlike a user a customer resource does not have a username or password so it cannot be authenticated through the API itself. Likewise you cannot use a user to keep track of order related resources, the order resource can only be associated with a customer resource not a user resource.

## Status of Customer

A customer can have three different status each with its own purpose. 

### Guest

A guest customer represent a customer that is not yet registered and does not have an associated user resource. Creating a guest customer does not require any information so it can be useful if you want to keep track of the orders for an anonymous person. You can create a guest customer and save the ID in the browser's local storage or cookie so that you can identify a returning customer. If they later on decided to registered, their previous order history will not be lost.

For business that accept order from other channel, customer service personnel can also use guest customer to keep track of returning customer's order.

### Registered

A registered customer represent a customer that have registered and provided at least a email, username and a password. A registered customer will always have an associated user resource so that they can authenticate with the API.

### Suspended

A suspended customer is a registered customer that you want to temporarily disable. A suspended customer will always have an associated user resource with the same status. This means they can no longer authenticate with the API.

Note that a customer with the same username as the suspended customer can no longer be registered, if you want to allow customer to re-registered you should delete the customer instead.

## Register a Customer

When you create a registered customer a associate user resource will be automatically created so that the customer can authenticate as a user through the API.

```javascript
import freshcom from 'freshcom-sdk'

let email = 'joe@example.com'
let password = 'supersecurepassword'

freshcom.createAndSetAccessToken({
  fresh_token: 'prt-test-aba1d92c-defb-478e-a031-40b5c27171c7',
  grant_type: 'refresh_token'
}).then(function () {
  return freshcom.createCustomer({
    status: 'registered',
    username: email,
    email: email,
    password: password
  })
}).then(function(response) {    
  let customer = response.data
  // Keep the customer somewhere for later use...
  // ...
  
  // Log in the customer by its password.
  return freshcom.createAndSetAccessToken({
    username: email,
    password: password,
    scope: 'aid:aid-test-5e9240ff-0885-4327-a1e7-f46f3fdb3f74',
    grant_type: 'password'
  })
})
```

## Login a Customer

Login a customer is simply creating a access token using the associated user's username and password. With the access token set you can then retrieve the customer associated with the user and any customer specific resources.

```javascript
import freshcom from 'freshcom-sdk'

let email = 'joe@example.com'
let password = 'supersecurepassword'

return freshcom.createAndSetAccessToken({
  username: email,
  password: password,
  scope: 'aid:aid-test-5e9240ff-0885-4327-a1e7-f46f3fdb3f74',
  grant_type: 'password'
}).then(function () {
  return freshcom.retrieveCustomer()
}).then(function (response) {
  let customer = response.data
  // Update the UI accordingly...
  // ...
})
```

## Next Steps

{% page-ref page="../point-account.md" %}

{% page-ref page="two-factor-authentication.md" %}



