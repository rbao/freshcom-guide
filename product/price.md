# Pricing Product

Freshcom allows you to price your product in many different ways by providing you with a few attributes that are not normally provided by most other eCommerce system.

* **Order Unit** - the unit that the customer use to order the product, the order quantity attribute of order line item should be in this unit.
* **Charge Unit** - the unit that you charge the customer with, the charge quantity attribute of order line item should be in this unit.
* **Charge Amount - **the amount to charge per charge unit.
* **Minimum Order Quantity** - the minimum order quantity needed to allow customer to use this price.
* **Estimate by Default** - Indicate whether the price is a estimate by default, if it is then the when a order line item is created with this price, it will be marked as a estimate.
* **Estimate Average Percentage** - If the price is a estimate by default, the sub total of a order line item using this price will be calculated using this percentage times the charge amount times the order quantity. This attribute indicate the average percentage difference between the order unit and the charge unit. See below example for detail. This is usually only used if your order unit and charge unit is different.
* **Estimate Maximum Percentage** - If the price is a estimate by default, the authorization total of a order line item will be calculated using this percentage times the charge amount times the order quantity plus all the taxes. This attribute indicate the maximum percentage difference between the order unit and the charge unit. See below example for detail. This is usually only used if your order unit and charge unit is different.

There are 3 commonly used pricing strategy you can implement using there attributes.

## Fixed Pricing

In simpler uses cases the unit that the customer order at and unit you charge at will be the same. This is what we call fixed pricing, where each product you sell is at a fixed price and the customer know exactly how much to pay when they checkout their cart. 

An example fixed pricing is if you are selling a t-shirt and each for $20. In this case both the order unit and charge unit is EA because the customer order each individual t-shirt and pays for each individual t-shirt. You should then set charge amount to $20 this means you will be charging the customer $20/EA for each t-shirt. You will set estimate by default to false because the $20 is a exact price not a estimate.

## Variable Pricing

In more complex use cases the unit that the customer order at and the unit you charge at will be different. This is what we call variable pricing, where each product you sell is at a different price even though its the exact same product. In variable pricing the customer does not know exactly how much they will pay when they checkout their cart, instead they are given a estimate. If you require your customer to pay online then instead of charging the customer for the estimate amount we recommend you authorize using the authorization total of the order and then when the exact price is known you capture the exact amount refund the rest back to the customer's card.

An example of variable pricing is if you are selling selling fish for $20 per pound. In this case the order unit will be EA because you cannot make each fish the exact same weight so customer actually order by each individual fish and each fish is at a different price depending on their weight. The charge unit will be LB because you will charge base on the weight of each fish.

You will also set estimate by default to true because when customer checkout their cart you do not yet know which fish you will pick for them so you don't know the exact weight and you can only provide the customer with a estimate when they checkout.

You will also need to set estimate average percentage so that Freshcom can know how to estimate the correct price for your customer. In this example if on average each fish is 2 LB then you will set estimate average percentage to 200% so that the estimated sub total of the order line item with the fish product will be $20 \* 200% = $40.

You will also need to set estimate maximum percentage so that Freshcom can calculated the authorization total for the order and you use this total to authorize against a customer's card. In this example if the largest fish you ever sold is 3 LB then its safe to set the estimate maximum percentage to 300% so that you can authorize the customer's card for $20 \* 300% = $60. Once you know the exact price of the fish you can then capture that exact amount and release the reset. In this example if the actual weight is 2.5 LB then you will capture $50 out of the authorized $60, and the $10 will be refunded to the customer. Please see our guide on [authorize payment](../from-cart-to-checkout/authorize-payment.md) for detail on how authorization works.

## Wholesale Pricing

In some cases you may want to give your customer discount if they buy a lot of product at once. Freshcom provides you a way to do this by setting the minimum order quantity on the price.

An example of wholesale pricing is if you sell your t-shirt at $20 each, but want to cut the price to $15 when a customer buys 10 or more. In this case you can create two prices for the t-shirt product:

1. One price with charge amount set to $20 and minimum order quantity set to 1.
2. Second price with charge amount set to $15 and minimum order quantity set to 10.

Now when you create a order line item with order quantity 10 for the t-shirt product Freshcom will automatically use the second price instead of the first price. If the order quantity is less than 10 then the first price will be used.

## Next Steps

{% page-ref page="../from-cart-to-checkout/" %}



