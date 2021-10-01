---
title: Consuming Webhooks
id: webhooks-payload
---

This guide goes into further detail on how to process a webhook payload for an event.

## How to verify the signature

Safepay will attach the signature to the request via the `X-SFPY-Signature` header. In order to verify that the request is indeed from Safepay, you should reconstruct the signature using your webhook secret. This should match the signature in the request. A demonstration in `node.js` is outlined below:

```
const crypto = require('crypto');

var payload = {
    token: "C5A5APSBCV41R2QF2MHG",
    client_id: "sec_55ca6960-e2ea-4643-b0cf-7cd65a4d3112",
    type: "payment:created",
    endpoint: "http://127.0.0.1:9000",
    notification: {
      tracker: "tracker_c5a5apsbcv41om3fg0u0",
      intent: "PAYFAST",
      fee: "4.92",
      net: "145.08",
      user: "johndoe@gmail.com",
      state: "PAID",
      amount: "150",
      currency: "PKR",
      metadata: {
        order_id: "XG102312",
        source: "shopify"
      }
    },
    delivery_attempts: 1,
    resource: "notification",
    next_attempt_at: "2021-10-01T09:05:33Z",
    created_at: "2021-09-29T12:00:40Z"
};

const data = Buffer.from(JSON.stringify(payload));
const secret = '86f63866a6e9b27f8f48a2d0a2946171b03639ea2e5000213e641da781a87d6e';

const hash = crypto.createHmac('sha512', secret)
  .update(data)
  .digest('hex');


console.log(hash);
```


## Events

Currently, Safepay supports subscribing to the following events:

- `payment:created`. This event is fired when a payment is captured
- `refund:created`. This event is fired when a refund is made
- `error:occurred`. This event is fired when an error occurs during the payment process

Sample payloads for each of these events are shown below.

### Payments

```
{
   "data":{
      "token":"C5438LD948188B0GPSH0",
      "client_id":"sec_55ca6960-e2ea-4643-b0cf-7cd65a4d3112",
      "type":"payment:created",
      "endpoint":"https://example.com/listener",
      "notification":{
         "tracker":"track_gfdkjg80-4knkjnlhknjn9-klnk9knn",
         "intent":"PAYFAST",
         "fee":"4.92",
         "net":"145.08",
         "user":"johndoe@gmail.com",
         "state":"PAID",
         "amount":"150",
         "currency":"PKR",
         "metadata":{
            "order_id":"XG102312",
            "source":"shopify"
         }
      },
      "delivery_attempts":1,
      "resource":"notification",
      "next_attempt_at":"2021-10-01T09:05:33Z",
      "created_at":"2021-10-01T09:05:34Z"
   }
}
```
### Refunds

```
{
   "data":{
      "token":"C243APO942228CP0L5GF",
      "client_id":"sec_55ca6960-e2ea-4643-b0cf-7cd65a4d3112",
      "type":"payment:created",
      "endpoint":"https://example.com/listener",
      "notification":{
         "tracker":"track_gfdkjg80-4knkjnlhknjn9-klnk9knn",
         "intent": "PAYFAST",
         "user": "johndoe@gmail.com",
         "state": "PARTIALLY REFUNDED",
         "amount": "25",
         "balance": "125",
         "currency": "PKR",
         "metadata": {
            "order_id": "XG102312",
            "source": "shopify"
         }
      },
      "delivery_attempts":1,
      "resource":"notification",
      "next_attempt_at":"2021-10-01T09:05:33Z",
      "created_at":"2021-10-01T09:05:34Z"
   }
}
```

### Errors

```
{
   "data":{
      "token":"C59KV5CBCV49VMOLCMVG",
      "client_id":"sec_55ca6960-e2ea-4643-b0cf-7cd65a4d3112",
      "type":"error:occurred",
      "endpoint":"https://example.com/listener",
      "notification":{
         "tracker":"track_5b694027-7947-4187-bbaa-8585a07c0cbc",
         "intent":"PAYFAST",
         "message":"55 : Incorrect OTP"
      },
      "delivery_attempts":1,
      "resource":"notification",
      "next_attempt_at":"2021-10-01T09:05:33Z",
      "created_at":"2021-10-01T09:05:34Z"
   }
}
```
