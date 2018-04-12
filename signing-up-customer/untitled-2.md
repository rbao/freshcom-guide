# Line Item Structure

In Freshcom, order line item can be created with or without a product.

## Line Item with Product

In most use case you will use order line item with a product and a associated price. When creating a order line item with a product you do not have control over the sub total of the order line item. The sub total is automatically calculated by Freshcom base on the price and the order quantity. You can change order quantity or charge quantity, but not the sub total itself.

Order line item with a product is not auto fulfilled by default, if you want to enable auto fulfill you must set it through the product, or explicitly set auto fulfill of the order line item to true. Please see our guide on [fulfillment](../fulfillment.md) for details on auto fulfillment.

## Line Item without Product

Freshcom also allows you to create order line item without any product. Order line item without any product will also not have any price because price depends on product, however you can set your own sub total and tax for order line items without any product. Order line item without product can be useful for many use cases, for example if you want to add a discount to an order you can simply add a line item to the order with a negative sub total without any product.

By default line item without any product is auto fulfilled, if you do not want to auto fulfill a line item without product then you will have to explicitly set auto fulfill to false. Please see our guide on [fulfillment](../fulfillment.md) for details on auto fulfillment.

## Nested Line Item

In Freshcom, order line item can be nested, and depending on whether the order line item has a product or not the nested structure have different behaviours.

For order Line Item with a product then the nested order line item is merely for informational purpose only, the sub total of the children order line item will not effect the sub total of the parent. 

For order line item without a product if it has one or more children then you will no longer be able to set the sub total, instead the sub total will always be calculated using the sum of all its children's sub total. You can only set the sub total for order line item without a product that does not have any children.

## Diagram

For you to better understand how order line item is structured below is a diagram of a example order with a few order line item.

