<!-- START_METADATA
---
title: API guide
sidebar_position: 1
hide_table_of_contents: true
pagination_next: null
pagination_prev: null
---
END_METADATA -->

# Access token API guide

The Access token API allows you to get a bearer token which can be used for authorizing all of your Vipps API requests.

To make requests to the Vipps APIs you need to:

1. First make a request to [POST: /accesstoken/get](https://vippsas.github.io/vipps-developer-docs/api/access-token#tag/Authorization-Service/operation/fetchAuthorizationTokenUsingPost) to get an access token, which is a JWT (JSON Web Token).
2. Use the access token in the HTTP header of the other API requests,
   together with the subscription key.

See
[Getting Started](https://vippsas.github.io/vipps-developer-docs/docs/vipps-developers/vipps-getting-started)
for information about API keys, product activation, how to make API calls, etc.



## Get an access token

All Vipps API calls are authenticated and authorized with an access token
(JWT bearer token) and an API subscription key:

| Header Name                 | Header Value                | Description      |
|:----------------------------|:----------------------------|:-----------------|
| `Authorization`             | `Bearer <JWT access token>` | Type: Authorization token. |
| `Ocp-Apim-Subscription-Key` | Base 64 encoded string      | The subscription key for this API. This is available on [portal.vipps.no](https://portal.vipps.no). |

All Vipps API requests must include an `Authorization` header with
a JSON Web Token (JWT), which we call the _access token_.

The access token is obtained by calling
[`POST:/accesstoken/get`](https://vippsas.github.io/vipps-developer-docs/api/access-token#tag/Authorization-Service/operation/fetchAuthorizationTokenUsingPost)
and passing the `client_id`, `client_secret` and `Ocp-Apim-Subscription-Key`.

Unfortunately, this is a `POST` without a body to an endpoint with
`get` in the URL. We hope to fix it in a later version of the API.

## Request

Request to
[`POST:/accesstoken/get`](https://vippsas.github.io/vipps-developer-docs/api/access-token#tag/Authorization-Service/operation/fetchAuthorizationTokenUsingPost)
(including the Vipps HTTP headers):

```json
client_id: fb492b5e-7907-4d83-ba20-c7fb60ca35de
client_secret: Y8Kteew6GE2ZmeycEt6egg==
Ocp-Apim-Subscription-Key: 0f14ebcab0ec4b29ae0cb90d91b4a84a
Merchant-Serial-Number: 123456
Vipps-System-Name: Acme Enterprises Ecommerce DeLuxe
Vipps-System-Version: 3.1.2
Vipps-System-Plugin-Name: Point Of Sale Excellence
Vipps-System-Plugin-Version 4.5.6
```

The `client_id`, `client_secret` and `Ocp-Apim-Subscription-Key` are unique per
`merchantSerialNumber` (MSN, i.e. the number of the sale unit).

See the
[Vipps HTTP headers](https://vippsas.github.io/vipps-developer-docs/docs/vipps-developers/common-topics/http-headers)
section for a description of the headers.

Please note: Partners should use
[partner keys](https://vippsas.github.io/vipps-developer-docs/docs/vipps-partner/partner-keys).

**Please note:** You can have multiple access tokens being used at the same time.

**Please note:** We are in process of changing the name of the header
`Ocp-Apim-Subscription-Key` to `Vipps-Subscription-Key`. We will at some point
phase out the old name completely, but it is not trivial and will take some time.
You may encounter both in the developer documentation, and the actual header
name to send is `Ocp-Apim-Subscription-Key`.

## Response

The response from
[`POST:/accesstoken/get`](https://vippsas.github.io/vipps-developer-docs/api/access-token#tag/Authorization-Service/operation/fetchAuthorizationTokenUsingPost)
is like this:

```json
{
  "token_type": "Bearer",
  "expires_in": "86398",
  "ext_expires_in": "0",
  "expires_on": "1495271273",
  "not_before": "1495184574",
  "resource": "00000002-0000-0000-c000-000000000000",
  "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1Ni <snip>"
}
```

The `access_token` is the most important part.
An explanation of the contents of the access token (the JWT properties):

| Name             | Description                                                                      |
|------------------|----------------------------------------------------------------------------------|
| `Bearer`         | It’s a `Bearer` token. The word `Bearer` must be added before the token          |
| `expires_in`     | Token expiry duration in seconds.                                                |
| `ext_expires_in` | Extra expiry time. Not used.                                                     |
| `expires_on`     | Token expiry time in epoch time format.                                          |
| `not_before`     | Token creation time in epoch time format.                                        |
| `resource`       | For the product for which token has been issued.                                 |
| `access_token`   | The actual access token that needs to be used in `Authorization` request header. |

**Please note:** The access token is valid for 1 hour in the test environment
and 24 hours in the production environment. To be sure that you are using
correct time please use `expires_in` or `expires_on`.
The access token is a JWT (JSON Web Token), and uses UTC time.

Problems? See:
[FAQ: Common errors](https://vippsas.github.io/vipps-developer-docs/docs/vipps-developers/faqs/common-errors-faq)
