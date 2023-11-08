<!-- START_METADATA
---
sidebar_label: API guide
title: Access token API guide
sidebar_position: 1
hide_table_of_contents: false
description: Use the Access Token API to get an authorization token that can be used with Vipps MobilePay API requests.
pagination_next: null
pagination_prev: null
---
END_METADATA -->

# API guide

![Vipps](./images/vipps.png) *Available for Vipps now.*

![MobilePay](./images/mp.png) *Available for MobilePay in selected markets at the [Vipps MobilePay joint platform launch](https://www.vippsmobilepay.com/about).*

<!-- START_COMMENT -->
ℹ️ Please use the website:
[Vipps MobilePay Technical Documentation](https://developer.vippsmobilepay.com/docs/APIs/access-token-api/).
<!-- END_COMMENT -->

## Authorization endpoints

The following two endpoints are provided for authorization:

* Access token endpoint (*for merchants*): [`POST:/accesstoken/get`][access-token-endpoint]
* Token endpoint (*for some APIs and some partner roles*): [`POST:/miami/v1/token`][token-endpoint]


If you are a merchant, skip to [Access token endpoint](#access-token-endpoint).
If you are a partner, see [Partner authorization](#partner-authorization).

### Partner authorization


Some new APIs, including the
[Management API](https://developer.vippsmobilepay.com/docs/APIs/management-api/)
and
[Report API](https://developer.vippsmobilepay.com/docs/APIs/report-api/)
use a new token endpoint, *for some roles*.

Here is a description of how you, as a partner, can use the authorization endpoints:

| API                       | Merchant: Normal API keys     | Partner: Partner keys     | Partner: Management keys     | Partner: Accounting keys |
| ------------------------- | ------------- | ------------------------- | ---------------------------- | ------------------------ |
| [Main APIs](https://developer.vippsmobilepay.com/docs/APIs/#main-apis) |  [`POST:/accesstoken/get`][access-token-endpoint] | [`POST:/accesstoken/get`][access-token-endpoint]  |[`POST:/accesstoken/get`][access-token-endpoint]| N/A |
| [Management API](https://developer.vippsmobilepay.com/docs/APIs/management-api/)  |[`POST:/accesstoken/get`][access-token-endpoint]  | N/A | [`POST:/miami/v1/token`][token-endpoint]: | N/A |
| [Report API](https://developer.vippsmobilepay.com/docs/APIs/report-api/) |[`POST:/accesstoken/get`][access-token-endpoint]  | N/A | N/A | [`POST:/miami/v1/token`][token-endpoint]: |

**Please note:**

* Please see [Partner keys](https://developer.vippsmobilepay.com/docs/partner/partner-keys/).
  * If you are a partner and have *partner keys*, you use those.
  * If you are a partner and have *management keys*, you use those.
  * If you are a partner and has *accounting keys*, you use those.
  * If you are a merchant you use your normal API keys.
* The
  [`POST:/miami/v1/token`][token-endpoint]
  will be used for all new APIs, but we will not change the
  authentication for existing APIs. A working integration that uses
  [`POST:/accesstoken/get`][access-token-endpoint]
   will continue to work as before.
* There are technical reasons for the two endpoints and the different roles,
  and it's unfortunately no easy way to "streamline" this for all APIs and roles at once.
* You can have multiple access tokens, and they can be used at the
  same time as long as they are valid.
* Partners should always use
  [partner keys](https://developer.vippsmobilepay.com/docs/partner/partner-keys)
  when possible.

## Access token endpoint

Use [`POST:/accesstoken/get`][access-token-endpoint]
to get an authorization token that can be used with most Vipps MobilePay API requests.

All API requests must include an `Authorization` header with
a JSON Web Token (JWT), which we call the *access token*.
The Access token API allows you to get this token.

### Get an access token

The access token is obtained by calling
[`POST:/accesstoken/get`][access-token-endpoint]
and providing these values in the HTTP header:

* `client_id` - Client_id for a sales unit.
* `client_secret` - Client_id for a sales unit.
* `Ocp-Apim-Subscription-Key` - Subscription key for a sales unit.

See
[Getting started](https://developer.vippsmobilepay.com/docs/getting-started)
for information about API keys, product activation, how to make API calls, etc.


Request:

A sample request to
[`POST:/accesstoken/get`][access-token-endpoint],
including the
[HTTP headers](https://developer.vippsmobilepay.com/docs/knowledge-base/http-headers):

```http
client_id: fb492b5e-7907-4d83-ba20-c7fb60ca35de
client_secret: Y8Kteew6GE2ZmeycEt6egg==
Ocp-Apim-Subscription-Key: 0f14ebcab0ec4b29ae0cb90d91b4a84a
Merchant-Serial-Number: 123456
Vipps-System-Name: acme
Vipps-System-Version: 3.1.2
Vipps-System-Plugin-Name: acme-webshop
Vipps-System-Plugin-Version: 4.5.6
```

*(Unfortunately,
[`POST:/accesstoken/get`][access-token-endpoint]
is a `POST` without a body, to an endpoint with
`get` in the URL. Too late to change it now, sorry.)*

Response:

The response from
[`POST:/accesstoken/get`][access-token-endpoint]
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

**Please note:** The access token is valid for 1 hour in the test environment
and 24 hours in the production environment. To be sure that you are using
correct time please use `expires_in` or `expires_on`.
The access token is a JWT (JSON Web Token), and uses UTC time.

You now have the access token and can make subsequent API calls with the following HTTP headers:

```json
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1Ni <truncated>
Ocp-Apim-Subscription-Key: 0f14ebcab0ec4b29ae0cb90d91b4a84a
Merchant-Serial-Number: 123456
Vipps-System-Name: acme
Vipps-System-Version: 3.1.2
Vipps-System-Plugin-Name: acme-webshop
Vipps-System-Plugin-Version: 4.5.6
```

**Important:** Remember to specify `Bearer`. If not, you may get a
`HTTP 401 Unauthorized` error. See the FAQ:
[Why do I get `HTTP 401 Unauthorized?`](https://developer.vippsmobilepay.com/docs/knowledge-base/errors#why-do-i-get-http-401-unauthorized).

## Token endpoint

The token endpoint is:
[`POST:/miami/v1/token`][token-endpoint].

While most of the older APIs, use the [access token endpoint](#access-token-endpoint), some new APIs, including the
[Management API](https://developer.vippsmobilepay.com/docs/APIs/management-api/)
and
[Report API](https://developer.vippsmobilepay.com/docs/APIs/report-api/)
use this token endpoint.

*Important:* This endpoint will be renamed to
`POST:/authentication/v1/token`
later, when the internal technical dependencies are resolved.

Authenticating with this endpoint is quite similar to the above-mentioned flow, but this new endpoint
uses a completely standard OAuth client credentials flow, allowing use of standardized libraries.
We strongly recommend this approach, using one of the
[trusted libraries](https://oauth.net/code/)
to perform the flow. There should be no reason to implement this from scratch.

Example request to
[`POST:/miami/v1/token`][token-endpoint]:

```http
POST https://api.vipps.no/miami/v1/token
Authorization: Basic <client_id>:<client_secret>
Content-Type: application/x-www-form-urlencoded; charset=utf-8
Merchant-Serial-Number: 123456
Vipps-System-Name: acme
Vipps-System-Version: 3.1.2
Vipps-System-Plugin-Name: acme-webshop
Vipps-System-Plugin-Version: 4.5.6

grant_type=client_credentials
```

**Please note:** The `Ocp-Apim-Subscription-Key` HTTP header should *not* be sent (unlike with the
[`POST:/accesstoken/get`][access-token-endpoint]
endpoint).

Example (JSON) response from
[`POST:/miami/v1/token`][token-endpoint]:

```json
{
  "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1Ni <truncated>",
  "token_type": "Bearer",
  "expires_in": 900
}

```



[access-token-endpoint]: https://developer.vippsmobilepay.com/api/access-token#tag/Authorization-Service/operation/fetchAuthorizationTokenUsingPost
[token-endpoint]: https://developer.vippsmobilepay.com/api/access-token#tag/Token-endpoint/operation/fetchToken
