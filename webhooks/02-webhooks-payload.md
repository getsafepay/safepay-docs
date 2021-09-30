---
title: Consuming Webhooks
id: webhooks-payload
---

This guide goes into further detail on how to process a webhook payload for an event.

## How to verify the signature

Safepay will attach the signature to the request via the `X-SFPY-Signature` header. In order to verify that the request is indeed from Safepay, you should reconstruct the signature using your webhook secret. This should match the signature in the request. A demonstration is outlined in the gist [here](https://gist.github.com/hmzaidi/d325a328cf1a7259e239cdb1ec5665c6).


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
      "created_at":"2021-09-20T07:12:21Z",
      "updated_at":"2021-09-20T07:12:21Z"
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
      "created_at":"2021-09-20T07:12:21Z",
      "updated_at":"2021-09-20T07:12:21Z"
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
      "created_at":"2021-09-28T17:23:34Z",
      "updated_at":"2021-09-28T17:23:34Z"
   }
}
```
