<!-- START_METADATA
---
title: Access token API guide
sidebar_label: API guide
sidebar_position: 1
hide_table_of_contents: true
description: Use the Access Token API to get an authorization token that can be used with Vipps MobilePay API requests.
pagination_next: null
pagination_prev: null
---
END_METADATA -->

# Access token API guide

Use the Access Token API to get an authorization token that can be used with Vipps MobilePay API requests.

All API requests must include an `Authorization` header with
a JSON Web Token (JWT), which we call the _access token_.
The Access token API allows you to get this token.

To make requests to the Vipps MobilePay APIs you need to:

1. First make a request to
   [`POST: /accesstoken/get`](https://vippsas.github.io/vipps-developer-docs/api/access-token#tag/Authorization-Service/operation/fetchAuthorizationTokenUsingPost)
   to get an access token.
2. Use the access token from (1) in the HTTP header of the other API requests.

## Get an access token

The access token is obtained by calling
[`POST:/accesstoken/get`](https://vippsas.github.io/vipps-developer-docs/api/access-token#tag/Authorization-Service/operation/fetchAuthorizationTokenUsingPost)
and providing these values in the HTTP header:

* `client_id` (think of it as the username)
* `client_secret` (think of it as the password)
* `Ocp-Apim-Subscription-Key` (specifies which API products you can access)

See
[Getting Started](https://vippsas.github.io/vipps-developer-docs/docs/vipps-developers/getting-started)
for information about API keys, product activation, how to make API calls, etc.

(Unfortunately,
[`POST:/accesstoken/get`](https://vippsas.github.io/vipps-developer-docs/api/access-token#tag/Authorization-Service/operation/fetchAuthorizationTokenUsingPost)
is a `POST` without a body, to an endpoint with
`get` in the URL. Too late to change it now, sorry.)

## Request

A sample request to
[`POST:/accesstoken/get`](https://vippsas.github.io/vipps-developer-docs/api/access-token#tag/Authorization-Service/operation/fetchAuthorizationTokenUsingPost)
(including the
[HTTP headers](https://vippsas.github.io/vipps-developer-docs/docs/vipps-developers/common-topics/http-headers)
):

```http
client_id: fb492b5e-7907-4d83-ba20-c7fb60ca35de
client_secret: Y8Kteew6GE2ZmeycEt6egg==
Ocp-Apim-Subscription-Key: 0f14ebcab0ec4b29ae0cb90d91b4a84a
Merchant-Serial-Number: 123456
Vipps-System-Name: Acme Enterprises Ecommerce DeLuxe
Vipps-System-Version: 3.1.2
Vipps-System-Plugin-Name: acme-webshop
Vipps-System-Plugin-Version: 4.5.6
```

**Please note:** You can have multiple access tokens, and they can used at the
same time as long as they are valid.

**Please note:** Partners should use
[partner keys](https://vippsas.github.io/vipps-developer-docs/docs/vipps-partner/partner-keys)
if possible.

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
  "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1Ni <truncated>"
}
```

The `access_token` is the most important part.
An explanation of the contents of the access token (the JWT properties):

| Name             | Description                                                                      |
|------------------|----------------------------------------------------------------------------------|
| `token_type`     | It’s a `Bearer` token. The word `Bearer` must be added before the token          |
| `expires_in`     | Token expiry duration in seconds.                                                |
| `ext_expires_in` | Extra expiry time. Not used.                                                     |
| `expires_on`     | Token expiry time in epoch time format.                                          |
| `not_before`     | Token creation time in epoch time format.                                        |
| `resource`       | For the product for which token has been issued.                                 |
| `access_token`   | The actual access token that needs to be used in `Authorization` request header. |

You now have the access token and can make subsequent API calls with the following HTTP headers:

```json
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1Ni <truncated>
Ocp-Apim-Subscription-Key: 0f14ebcab0ec4b29ae0cb90d91b4a84a
Merchant-Serial-Number: 123456
Vipps-System-Name: Acme Enterprises Ecommerce DeLuxe
Vipps-System-Version: 3.1.2
Vipps-System-Plugin-Name: acme-webshop
Vipps-System-Plugin-Version: 4.5.6
```

**Please note:** The access token is valid for 1 hour in the test environment
and 24 hours in the production environment. To be sure that you are using
correct time please use `expires_in` or `expires_on`.
The access token is a JWT (JSON Web Token), and uses UTC time.

Problems? See:
[FAQ: Common errors](https://vippsas.github.io/vipps-developer-docs/docs/vipps-developers/faqs/common-errors-faq)
