---
title: Using Webhooks
id: webhooks-intro
---

Webhooks can be configured on the Safepay merchant dashboard fairly easily. Webhooks are extremely useful in notifying you of certain events that take place asynchronously. Your integration can use webhooks to listen to payment confirmations along with other supported events in order to programmatically react and make updates on your system.

## Enabling webhooks

To enable webhooks for your account, log in to your dashboard, navigate to the Webhooks page and click on the `Enable` button. You can access this from the Account dropdown menu on the top right.

<p align="center">
  <img
    src="https://storage.googleapis.com/safepay-docs/webhooks-enable.gif"
    alt="Enable webhooks"
    width="75%"
  />
</p>

## Adding and Updating an endpoint

To configure an endpoint that will receive notifications, you can add it to your account using the interface. However, in order for you to receive notifications on this endpoint, it should be live on your server.


If you ever change the endpoint URL on your server you can update it using the interface.

<p align="center">
  <img
    src="https://storage.googleapis.com/safepay-docs/webhooks-create-endpoint.gif"
    alt="Adding and Updating endpoint"
    width="75%"
  />
</p>

## Subscribe to events

Currently, Safepay supports the following events:

- `payment:created`. This event is fired when a payment is captured
- `refund:created`. This event is fired when a refund is made
- `error:occurred`. This event is fired when an error occurs during the payment process

Your endpoint can subscribe to both events or a single event depending on how you have configured your server to react to events. Some developers prefer to handle all events in a single endpoint while others prefer to keep separate endpoints for each event.

<p align="center">
  <img
    src="https://storage.googleapis.com/safepay-docs/webhooks-subscribe.gif"
    alt="Subscribe to an event"
    width="75%"
  />
</p>

## View your shared secret

Safepay signs the webhook events it sends to your endpoints by including a signature in each event’s X-SFPY-Signature header. This allows you to verify that the events were sent by Safepay, not by a third party. You can verify signatures either using our official libraries, or manually using your own solution.

To verify the signature you need to store your shared secret on your server. You can access the shared secret using the interface

<p align="center">
  <img
    src="https://storage.googleapis.com/safepay-docs/webhooks-view-shared-secret.gif"
    alt="View shared secret"
    width="75%"
  />
</p>

## Delete endpoint

If you ever need to stop receiving events you can either unsubscribe from all events for that endpoint or delete the endpoint entirely. To delete the endpoint, navigate to the webhooks page on the interface, find the endpoint to delete and confirm deletion.

<p align="center">
  <img
    src="https://storage.googleapis.com/safepay-docs/webhooks-delete-endpoint.gif"
    alt="Delete endpoint"
    width="75%"
  />
</p>
