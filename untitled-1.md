# Goods

Goods is different than product, goods is something you have that you want to keep track of but may or may not be something you want to sell whereas product always represents goods for sell. Freshcom provides three types of goods you can use: stockable, unlockable and depositable.

**Stockable **represents goods that can have stock. This is usually used to represent physical goods and used together with inventory. 

**Unlockable** represents goods that can only be bought once by a customer \(unlocking the unlockable\) and will have access to it for a long time. This is usually used to represent digital product, for example a game, a photo, or an audiobook.

**Depositable** represents goods that can be deposited. This is usually used to represent gift card, loyalty points, store credit or any other currency like goods. You can use this goods to trigger deposit in to a customer's point account. Please also see our guide on [point account](point-account.md).

## Using a Stockable

Coming soon.

## Using a Unlockable

To make a unlockable available for purchase to your customer through your client side application you must first setup a few resources.

1. Create a unlockable.
2. Create a product for the unlockable and set auto fulfill to be enabled.
3. Create a price for the product.
4. Activate the product.

You can use the Dashboard to setup those resources.

### Unlock a Unlockable

Once you have the required resource setup you can then access the unlockable product in your client side application on behalf of a customer. You can implement your checkout as normal following the guide [from cart to checkout](signing-up-customer/) with one important difference: in order to checkout a cart with unlockable product you must use a user access token. If you use a publishable access token you will get an error because Freshcom does not know which customer to unlock the unlockable for.

Once a customer checkout their cart with a unlockable product, a auto fulfillment will be created for that specific order line item which will in turn create a unlock for the customer.

### Listing Unlocked Unlockable

Once a unlock resource is created for the customer, your client side application can now list all the unlocked unlockable on the customer's behalf for the customer view the unlocked unlockables.

```javascript
import freshcom from 'freshcom-sdk'
 
// Use the customer's username and password to obtain a user access token.
freshcom.createAndSetAccessToken({
  username: 'joe@example.com',
  password: 'supersecurepassword',
  scope: 'aid:aid-test-74b901b2-32f9-4e3b-8d4d-4eca2360550c',
  grant_type: 'password'
}).then(function () {
  return freshcom.listUnlock({
    // Include the unlockable relationship of unlock.
    include: 'unlockable'    
  })
}).then(function(response) {    
  let unlocks = response.data
  // You can now call unlocks[0].unlockable to view the unlockable
  // or unlocks[0].unlockable.file to allow customer to view the
  // unlockable's file.
})
```

Note that only specific role can retrieve an unlockable directly by its ID, all other roles including customer must retrieve an unlockable through a unlock resource.

### Manually Unlock a Unlockable

In some cases you may not want to unlock the unlockable right after a customer checkout their cart. To achieve this you can simply disable the auto fulfill of the product, then all order line item with that unlockable product will not attempt to auto fulfill which means no unlock will be created even if a customer checkout their cart. You can then manually unlock the unlockable at a later time through the Dashboard by creating a fulfillment item for the order or creating a unlock directly for the customer.

You can also enable or disable the auto fulfill on a per order basis by simply setting the auto fulfill fields of the order line item with the unlockable product. The auto fulfill field of order line item will overwrite the product's auto fulfill value.

If you have your own backend that have custom logic to process the unlock then you can also create a notification trigger to trigger a webhook request. Please see our guide on [notification](notification.md) for how to setup webhook.

### Listing Product with Unlocked Unlockable

When listing product with unlockable you may want differentiate which product has been unlocked and which is not. The product resource does not have an attribute to differentiate this, so you will need to list all the corresponding unlock of the customer and check against the goods of the product.

```javascript
import freshcom from 'freshcom-sdk'
 
// Use the customer's username and password to obtain a user access token.
freshcom.createAndSetAccessToken({
  username: 'joe@example.com',
  password: 'supersecurepassword',
  scope: 'aid:aid-test-74b901b2-32f9-4e3b-8d4d-4eca2360550c',
  grant_type: 'password'
}).then(function () {
  return freshcom.listProduct()
}).then(function (response) {
  let products = response.data
  
  // Filter out the unlockable products.
  let unlockableProducts = products.filter(function (product) {
    return product.goods.type === 'Unlockable'
  })
  
  // Map to an array of unlockable ids.
  let unlockableIds = unlockableProducts.map(function (product) {
    return product.goods.id
  })
  
  // List all unlock matching the unlockable ids.
  return Promise.all([
    freshcom.listUnlock({
      filter: { unlockableId: unlockableIds },
      include: 'unlockable'    
    },
    response
  ])
}).then(function(responses) {    
  let unlocks = responses[0].data
  
  let products = responses[1].data.map(function (product) {
    if (product.goods.type !== 'Unlockable') { return }
    
    // Check if an unlock exist for this product.
    let unlock = this.unlocks.find(function (unlock) {
      return product.goods.id === unlock.unlockable.id
    })
    
    // Add a new 'unlock' property for the product if an unlock exist.
    if (unlock) {
      product.unlock = unlock
    }
    
    return product
  })
  
  // You can now check for products[0].unlock to see if the product
  // for an already unlocked unlockable.
})
```

## Using a Depositable

To make a depositable available for purchase to your customer through your client side application you must first setup a few resources.

1. Create a depositable with gateway set to **Freshcom**.
2. Create a product for the depositable and set auto fulfill to be enabled.
3. Create a price for the product.
4. Activate the product.

You can use the Dashboard to setup those resources.

### Deposit a Depositable

Once you have the required resource setup you can then access the depositable product in your client side application on behalf of a customer. You can implement your checkout as normal following the guide [from cart to checkout](https://freshcom.gitbook.io/freshcom/~/edit/drafts/-L9SaXWG0h7oNs9qllTZ/signing-up-customer) with one important difference: in order to checkout a cart with depositable product you must use a user access token. If you use a publishable access token you will get an error because Freshcom does not know which customer to deposit the depositable for.

Once a customer checkout their cart with a unlockable product, a auto fulfillment will be created for that specific order line item which will in turn commit a point transaction with the depositable's amount in the customer's matching point account.

### Manually Deposit a Depositable

In some cases you may not want to deposit the depositable right after a customer checkout their cart. To achieve this you can simply disable the auto fulfill of the product, then all order line item with that depositable product will not attempt to auto fulfill which means no point transaction will be committed even if a customer checkout their cart. You can then manually commit a point transaction at a later time through the Dashboard by creating a fulfillment item for that order line item or creating a committed point transaction directly for the customer.

You can also enable or disable the auto fulfill on a per order basis by simply setting the auto fulfill fields of the order line item with the unlockable product. The auto fulfill field of order line item will overwrite the product's auto fulfill value.

### Custom Deposit

If you have your own backend that have custom logic to process the deposit then you can set the gateway of the depositable to **custom**, this way Freshcom will not attempt to deposit anything to the customer's point account. You should set up a notification trigger to trigger a webhook request to your backend. Please see our guide on notification.

When you use custom gateway you can still enable auto fulfill for the depositable product, in this auto fulfill will only create a auto fulfillment for the purpose of marking the order line item as fulfilled even though its to you to actually fulfill the deposit.

## Nest Steps

{% page-ref page="product/" %}

{% page-ref page="inventory.md" %}

{% page-ref page="fulfillment.md" %}



