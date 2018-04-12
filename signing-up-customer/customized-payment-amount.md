# Customize Payment Amount

By default Freshcom requires a cart to be paid in full in order to transition it to an open order, however this can be changed through the Dashboard or by updating the `requiredCartToBeFullyPaid` attributes of the storefront settings resource through the API. Once you have updated the settings you can now customize the payment amount required in order to checkout a cart. Below is a few examples of customization.

* Allow customer to checkout without paying
* Allow customer to checkout by paying a partial amount

## Checkout without Paying

Once you have updated the corresponding setting you can checkout a cart without requiring a payment by simply updating the status of the cart.

```javascript
import freshcom from 'freshcom-sdk'

// Assuming you have a existing cart.
let cart = {...}

freshcom.createAndSetAccessToken({
  fresh_token: 'prt-test-aba1d92c-defb-478e-a031-40b5c27171c7',
  grant_type: 'refresh_token'
}).then(function () {
  return freshcom.updateOrder({
    id: cart.id
  }, {
    status: 'opened'
  })
}).then(function(response) {
  let order = response.data
  // Update the UI accordingly...
  // ...
})
```

After the order is opened, it is now up to you to collect the payment from the customer. Once you received the payment you can then create a payment with gateway set to `custom` to mark the order as paid, this can be done through the Dashboard.

## Checkout by Paying Partial Amount

Once you have updated the corresponding setting you can allow customer to checkout a cart by paying a partial amount. To do this simply create a payment for the cart with the desired amount. If you want the customer to pay a % of the total simply do the math yourself and supply the resulting value to the create payment request.

```javascript
import freshcom from 'freshcom-sdk'

// Assuming you have obtained a card token from stripe.
let token = '...'

// Assuming you have an existing cart.
let cart = {...}
let depositAmount = Math.round(cart.grandTotalCents * 0.3)

freshcom.createAndSetAccessToken({
  fresh_token: 'prt-test-aba1d92c-defb-478e-a031-40b5c27171c7',
  grant_type: 'refresh_token'
}).then(function () {
  return freshcom.createPayment({
    source: token,
    amountCents: depositAmount,
    target: cart
  })
}).then(function(response) {
  let order = response.data
  // Update the UI accordingly...
  // ...
})
```

After the order is opened it is now up to you to collect the remaining amount from the customer when its due. One way to do this is to implement a paylink for customer to pay directly through Freshcom, please see our guide on setting up paylink.

## Next Steps

{% page-ref page="../fulfillment.md" %}



