# Notification

Freshcom's notification module give you maximum flexibility on how you want to notify your application or customer about the change of resources happened on Freshcom.

All emails, sms and webhook request send by Freshcom is due to a notification trigger. Each notification trigger listens for a specific event emitted by Freshcom and handles that event by triggering a action. There are currently three actions that can be triggered by a notification trigger.

* Send a email.
* Send a SMS \(text message\).
* Send a webhook request.

When you first sign up for Freshcom, your account is setup with 3 pre-created notification triggers, you can login to the Dashboard to view how they are setup and create more as needed.

Each event emitted from Freshcom also comes with some event data, for example if you updated an order then a `storefront.order.update.success` event will be emitted and the updated order and its related resources will be part of the event data. You can use the event data for the action you want to trigger. For example if you want to trigger a send email action, then you can use event data in the email template to render the email differently depending on the event data.

For details on which events are available and the event data associated with them please see each individual endpoint in your API references. In general event is only emitted when you use the API or the Dashboard to create, update and delete a resource.



