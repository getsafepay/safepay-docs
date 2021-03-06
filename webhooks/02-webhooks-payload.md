---
title: Consuming Webhooks
id: webhooks-payload
---

This guide goes into further detail on how to process a webhook payload for an event.

## How to verify the signature

Safepay will attach the signature to the request via the `X-SFPY-Signature` header. In order to verify that the request is indeed from Safepay, you should reconstruct the signature using your webhook secret. This should match the signature in the request. Samples are presented below:

### node

```
const crypto = require('crypto');

var payload = {
    token: "C5A5APSBCV41R2QF2MHG",
    client_id: "sec_55ca6960-e2ea-4643-b0cf-7cd65a4d3112",
    type: "payment:created",
    endpoint: "http://127.0.0.1:9000",
    notification: {
      tracker: "tracker_c5a5apsbcv41om3fg0u0",
      reference: "514087",
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
### php

```
<?php

//Laravel implementation for Safepay webhooks
function safepay_notification(Request $request) {

   $request_all = $request->all();
   $request_data = $request_all['data'];

   $data['payment']  = 'SafePay';
   $data['body']     = json_encode($request_data);

   \DB::table('payment_logs')->insert($data);

   $event_type = $request_data['type']; //payment:created, error:occurred
   $x_sfpy_signature = $request->header('x-sfpy-signature');
   $web_hook_share_secret = config('safepay.webhook_share_secret_key');
   $signature_2 = hash_hmac('sha512',json_encode($request_data,JSON_UNESCAPED_SLASHES), $web_hook_share_secret);

   if ( $signature_2  !== $x_sfpy_signature ) {
         return  $this->response([
            'status' => false,
            'dataSet' => [],
               ], 
            self::HTTP_OK // HTTP_UNAUTHORIZED
      );
   }

   $order_metadata = $request_data['notification']['metadata']['order_id']??'';
   if( empty($order_metadata) ) {
         return  $this->response([
            'status' => false,
            'dataSet' => [],
               ], 
            self::HTTP_OK // HTTP_UNAUTHORIZED
      );
   }

   if( $event_type == 'payment:created') {
      $safepay_reference_code = $request_data['notification']['reference'];
   }else { //error:occured
      //Add Note.
   }
   
   return  $this->response([
      'status' => TRUE,
      'dataSet' => [],
            ], 
      self::HTTP_OK
   );
}
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
         "reference": "514087",
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
         "message":"55 : Incorrect OTP",
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
