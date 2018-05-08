# Authorize Payment

Freshcom allows you to authorize a payment against a card without capturing the payment. Once a payment is successfully authorized you are guaranteed to be able to capture the entire or partial amount. Authorizing a payment is useful when you do not know the exact amount to charge at the time of authorizing, this is often used when you use variable pricing for a product.

Payment can only be authorized for a maximum of 7 days, if you did not capture the payment within 7 days then the full amount will be refunded to the customer's card. If you require authorization for longer than 7 days then the only way is to refund the original one and create a new authorized payment manually. This can be done through the Dashboard.

Creating a authorized payment in you client side application is as simple as create a payment with captured set to `false`.

```javascript
import freshcom from 'freshcom-sdk'

// Assuming you have obtained a card token from stripe.
let token = '...'

// Assuming you have an existing cart.
let cart = {...}

freshcom.createAndSetAccessToken({
  fresh_token: 'prt-test-aba1d92c-defb-478e-a031-40b5c27171c7',
  grant_type: 'refresh_token'
}).then(function () {
  return freshcom.createPayment({
    source: token,
    amountCents: cart.authorizationTotalCents,
    capture: false,
    target: cart
  })
}).then(function(response) {
  let order = response.data
  // Update the UI accordingly...
  // ...
})
```

## Next Steps

{% page-ref page="../fulfillment.md" %}



