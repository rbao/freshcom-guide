# From Cart to Checkout

This guide assumes you have a active product already setup. Please see our guide on [product](../product/) for details on how to setup an active product. From cart to checkout is a 4 step process:

1. Create a cart
2. Add product to cart by creating line items
3. Submit customer's information
4. Pay for the cart by creating a payment

## Step 1:  Create a cart

When a customer visit your store you should create a cart for them so they can start shopping. In Freshcom, cart and order is the same resource, cart is simply an order with status `cart`. When an order is first created it will always have the status `cart`, so creating a initial cart is as simply as creating an empty order.

{% code-tabs %}
{% code-tabs-item title="Create a cart" %}
```javascript
import freshcom from 'freshcom-sdk'

freshcom.createAndSetAccessToken({
  fresh_token: 'prt-test-aba1d92c-defb-478e-a031-40b5c27171c7',
  grant_type: 'refresh_token'
}).then(function () {
  return freshcom.createOrder()
}).then(function(response) {
  let cart = response.data
  // Save the order.id in localStorage or cookies for future use
  // ...
})
```
{% endcode-tabs-item %}
{% endcode-tabs %}

Once a cart is created, we recommend you to save its ID in the browser's locale storage or cookies, so that if they refresh the page or opens a new tab their cart does not get lost. As long as you have the cart ID you can retrieve the cart through Freshcom API.

## Step 2: Add product to cart

Adding product to cart is simply creating a line item for the cart. This step assume you have a active product for customer to purchase. For details on how to setup product please see our guide on [product](../product/).

{% code-tabs %}
{% code-tabs-item title="Create a line item" %}
```javascript
import freshcom from 'freshcom-sdk'

freshcom.createAndSetAccessToken({
  fresh_token: 'prt-test-aba1d92c-defb-478e-a031-40b5c27171c7',
  grant_type: 'refresh_token'
}).then(function () {
  return freshcom.createOrderLineItem({
    orderQuantity: 2
    order: {
      id: '9fc8ee53-906f-48bd-a392-8b0709301699',
      type: 'Order'
    },
    product: {
      id: '74b901b2-32f9-4e3b-8d4d-4eca2360550c',
      type: 'Product'
    }
  }, {
    // Include these relationships from the returned line item.
    // rootLineitems is the top level line items of the order.
    include: 'order.rootLineItems' 
  })
}).then(function(response) {
  let cart = response.data.order
  // Update the UI accordingly...
  // ...
})
```
{% endcode-tabs-item %}
{% endcode-tabs %}

When you create a line item for the cart, the sub total, taxes and grand total related attributes for the cart will be updated automatically.

## Step 3: Submit customer's information

Submit the customer's information by simply updating the cart with the appropriate information.

```javascript
import freshcom from 'freshcom-sdk'

freshcom.createAndSetAccessToken({
  fresh_token: 'prt-test-aba1d92c-defb-478e-a031-40b5c27171c7',
  grant_type: 'refresh_token'
}).then(function () {
  return freshcom.updateOrder({
    id: '9fc8ee53-906f-48bd-a392-8b0709301699'
  }, {
    name: 'Joe Happy',
    email: 'joeishappy@example.com',
    fulfillmentMethod: 'pickup'
  })
}).then(function(response) {
  let cart = response.data
  // Update the UI accordingly...
  // ...
})
```

Note that non of the attributes of the order resource is required when creating, but when updating the name, email and fulfillment method is required.

## Step 4: Paying for the cart

Freshcom does not reinvent payment processing, instead we uses Stripe under the hood to process payments. Each Freshcom account is connected to its own Stripe account, and thus have its own keys for Stripe. Using your Stripe keys you can advantage of many Stripe product for collecting payment information like Stripe Element. At the moment Freshcom do not accept credit number directly you must use [Stripe.js or Stripe Elements](https://stripe.com/docs/stripe-js) to create a token for the credit card first and use the token to create a payment in Freshcom. In short paying for a cart is a two step process:

1. Create a credit card token using [Stripe.js or Stripe Elements](https://stripe.com/docs/stripe-js)
2. Create a payment using the token returned from Stripe

Please see the [Stripe.js & Stripe Elements](https://stripe.com/docs/stripe-js) documentation on Stripe for how to obtain the token for credit card. The code below assume the token is already obtained.

{% code-tabs %}
{% code-tabs-item title="Create a payment" %}
```javascript
import freshcom from 'freshcom-sdk'

// Assuming you already obtained a card token from Stripe.
let token = '...'

// Cart from previous steps.
let cart = {...}

freshcom.createAndSetAccessToken({
  fresh_token: 'prt-test-aba1d92c-defb-478e-a031-40b5c27171c7',
  grant_type: 'refresh_token'
}).then(function () {
  return freshcom.createPayment({
    source: token,
    amountCents: cart.grandTotalCents
    target: cart
  })
}).then(function(response) {
  let order = response.data
  // Update the UI accordingly...
  // ...
})

```
{% endcode-tabs-item %}
{% endcode-tabs %}

Once a payment is created, the status of cart will be automatically be changed to `opened` indicating a successful transition from cart to an open order.

## Next Steps

{% page-ref page="customized-payment-amount.md" %}

{% page-ref page="authorize-payment.md" %}

{% page-ref page="untitled-2.md" %}

{% page-ref page="../fulfillment.md" %}



