# Point Account

Point account represent an account for your customer that can be used to store arbitrary currency which we call points. The value of these points is completely up to you. Point account can be used to implement store credit, loyalty points or any other use case that require storing value for your customer for later use.

A customer may have up to 10 point accounts, for example one for keeping track of their store credit one for their loyalty points. We really no use cases for creating more than 10 point accounts but if you think you have a valid use case for more than 10 point accounts, please contact us. You can configure each point account to represent value differently. Once customer have points in their account you can allow them to use it through your client side application.

There are two ways to create point account for a customer.

1. Create point account templates in the Dashboard, then whenever a customer is created a point account will be created for each existing template with status `active`. Whenever you make change the point account template the associated point account will also be changed, for example you can change the name of the point account template then all point account created using that template will automatically have its name changed as well.
2. Create a point account directly for the customer in the Dashboard. Creating a point account directly means that the point account is not associated with any template, which means you will not be able to have the bulk edit functionality as when using a point account template.

## Deposit to Point Account

There are mainly 3 ways to deposit into a point account.

1. Manually create a committed point transaction for the point account through the Dashboard.
2. Use a depositable to allow customer to deposit by paying for a depositable product. For details please see our guide on [using a depositable](untitled-1.md#using-a-depositable).
3. _\(coming soon\)_ Specify the points on the price of a product and whenever customer checkout an order with that price a point transaction matching those point will be automatically created and committed.

## Spend Points

Using the Dashboard you can commit point transaction with negative amount which will spend the existing point in a customer's point account. You can also allow the customer to spend their own points through your client side application. You can do this in 2 steps.

1. Create a point transaction, customer created point transaction will always be in `pending` status and the amount is required to be less than zero meaning their are spending the point.
2. Create a line item for the customer's cart targeting that the pending point transaction. The order line item will have its amount fields automatically calculated according to the point account's `exchangeRate`.

Once customer checkout their cart, an auto fulfillment item will be created for the point transaction which will in term committed it automatically and their point balance will updated as well.

```javascript
import freshcom from 'freshcom-sdk'

// Assuming you have a existing cart
let cart = '...'

// In order to create point transaction you must use a user access token.
freshcom.createAndSetAccessToken({
  username: 'customer@example.com',
  password: 'supersecurepassword',
  scope: 'aid:aid-test-74b901b2-32f9-4e3b-8d4d-4eca2360550c',
  grant_type: 'password'
}).then(function () {
  return freshcom.retrieveCustomer({
    // Include the point account relationship of the customer.
    include: 'pointAccounts'    
  })
}).then(function (response) {    
  let customer = response.data
  
  // Find the correct point account to use.
  let storeCreditAccount = customer.pointAccounts.find(function (pointAccount) {
    return pointAccount.kind === 'store_credit'
  })
  
  // 1. Create a pending point transaction.
  return freshcom.createPointTransaction({
    pointAccount: storeCreditAccount,
    amount: -3000,
  })
}).then(function (response) {
  let pointTransaction = response.data
  
  // 2. Creat point transaction line item for the cart.
  return freshcom.createOrderLineItem({
    target: pointTransaction,
    order: cart
  })
})
```

## Refund Points

When a customer used their point an order but later returned that order and wanted to refund, you should probably refund their point as well. Since point transaction on a order line item is fulfilled using a auto fulfilment item you can simply mark the fulfillment item as returned through the Dashboard and Freshcom will automatically create and commit a new point transaction that reverse the previous one. For more details see our guide on [fulfillment](fulfillment.md).

