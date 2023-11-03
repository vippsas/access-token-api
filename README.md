<!-- START_METADATA
---
sidebar_label: API guide
sidebar_position: 1
hide_table_of_contents: false
description: Use the Access Token API to get an authorization token that can be used with Vipps MobilePay API requests.
pagination_next: null
pagination_prev: null
---
END_METADATA -->

# Access token API guide

![Vipps](./images/vipps.png) *Available for Vipps now.*

![MobilePay](./images/mp.png) *Available for MobilePay in selected markets at the [Vipps MobilePay joint platform launch](https://www.vippsmobilepay.com/about).*

<!-- START_COMMENT -->
ℹ️ Please use the website:
[Vipps MobilePay Technical Documentation](https://developer.vippsmobilepay.com/docs/APIs/access-token-api/).
<!-- END_COMMENT -->

## Two endpoints

If you are a merchant: Use
[`POST:/accesstoken/get`](https://developer.vippsmobilepay.com/api/access-token#tag/Authorization-Service/operation/fetchAuthorizationTokenUsingPost) 
and skip to
[The Access token API: POST:/accesstoken/get](#the-access-token-api-postaccesstokenget).

If you are a partner: Continue reading this section.

**Important:** Some of the new APIs, including the
[Management API](https://developer.vippsmobilepay.com/docs/APIs/management-api/)
and
[Report API](https://developer.vippsmobilepay.com/docs/APIs/report-api/)
use a new token endpoint, _for some roles_:

* Access token endpoint: [`POST:/accesstoken/get`](https://developer.vippsmobilepay.com/api/access-token#tag/Authorization-Service/operation/fetchAuthorizationTokenUsingPost) 
* Token endpoint _for some APIs and roles_: 
[`POST:/miami/v1/token`](https://developer.vippsmobilepay.com/api/access-token/#tag/Authorization-Service/operation/fetchAuthorizationTokenUsingPost)


| API                       | Merchant: Normal API keys     | Partner: Partner keys     | Partner: Management keys     | Partner: Accounting keys |
| ------------------------- | ------------- | ------------------------- | ---------------------------- | ------------------------ |
| [Main APIs](https://developer.vippsmobilepay.com/docs/APIs/#main-apis) |  [`POST:/accesstoken/get`](https://developer.vippsmobilepay.com/api/access-token#tag/Authorization-Service/operation/fetchAuthorizationTokenUsingPost) | [`POST:/accesstoken/get`](https://developer.vippsmobilepay.com/api/access-token#tag/Authorization-Service/operation/fetchAuthorizationTokenUsingPost)  |[`POST:/accesstoken/get`](https://developer.vippsmobilepay.com/api/access-token#tag/Authorization-Service/operation/fetchAuthorizationTokenUsingPost)| N/A |
| [Management API](https://developer.vippsmobilepay.com/docs/APIs/management-api/)  |[`POST:/accesstoken/get`](https://developer.vippsmobilepay.com/api/access-token#tag/Authorization-Service/operation/fetchAuthorizationTokenUsingPost)  | N/A | [`POST:/miami/v1/token`](https://developer.vippsmobilepay.com/api/access-token/#tag/Authorization-Service/operation/fetchAuthorizationTokenUsingPost) | N/A |
| [Report API](https://developer.vippsmobilepay.com/docs/APIs/report-api/) |[`POST:/accesstoken/get`](https://developer.vippsmobilepay.com/api/access-token#tag/Authorization-Service/operation/fetchAuthorizationTokenUsingPost)  | N/A | N/A | [`POST:/miami/v1/token`](https://developer.vippsmobilepay.com/api/access-token/#tag/Authorization-Service/operation/fetchAuthorizationTokenUsingPost) |

**Please note:** 
* For partners: Please see [Partner keys](https://developer.vippsmobilepay.com/docs/partner/partner-keys/).
  * If you are a partner and have _partner keys_, you use those.
  * If you are a partner and have _management keys_, you use those.
  * If you are a partner and has _accounting keys_, you use those.
  * If you are a merchant you use your normal API keys.
* The 
  [`POST:/miami/v1/token`](https://developer.vippsmobilepay.com/api/access-token/#tag/Authorization-Service/operation/fetchAuthorizationTokenUsingPost)
  will be used for all new APIs, but we will not change the
  authentication for existing APIs. A working integration that uses 
  [`POST:/accesstoken/get`](https://developer.vippsmobilepay.com/api/access-token#tag/Authorization-Service/operation/fetchAuthorizationTokenUsingPost) 
   will continue to work as before. 
* There are technical reasons for the two endpoints and the different roles,
  and it's unfortunately no easy way to "streamline" this for all APIs and roles at once.
* You can have multiple access tokens, and they can be used at the
  same time as long as they are valid.
* Partners should always use
  [partner keys](https://developer.vippsmobilepay.com/docs/partner/partner-keys)
  when possible.

## The Access token API: POST:/accesstoken/get

Use the Access Token API to get an authorization token that can be used with Vipps MobilePay API requests.

All API requests must include an `Authorization` header with
a JSON Web Token (JWT), which we call the *access token*.
The Access token API allows you to get this token.

To make requests to the Vipps MobilePay APIs you need to:

1. First make a request to
   [`POST:/accesstoken/get`](https://developer.vippsmobilepay.com/api/access-token#tag/Authorization-Service/operation/fetchAuthorizationTokenUsingPost)
   to get an access token.
2. Use the access token from (1) in the HTTP header of the other API requests.

## Get an access token

The access token is obtained by calling
[`POST:/accesstoken/get`](https://developer.vippsmobilepay.com/api/access-token#tag/Authorization-Service/operation/fetchAuthorizationTokenUsingPost)
and providing these values in the HTTP header:

* `client_id` - Client_id for a sales unit.
* `client_secret` - Client_id for a sales unit.
* `Ocp-Apim-Subscription-Key` - Subscription key for a sales unit.

See
[Getting Started](https://developer.vippsmobilepay.com/docs/getting-started)
for information about API keys, product activation, how to make API calls, etc.

(Unfortunately,
[`POST:/accesstoken/get`](https://developer.vippsmobilepay.com/api/access-token#tag/Authorization-Service/operation/fetchAuthorizationTokenUsingPost)
is a `POST` without a body, to an endpoint with
`get` in the URL. Too late to change it now, sorry.)

## Request

A sample request to
[`POST:/accesstoken/get`](https://developer.vippsmobilepay.com/api/access-token#tag/Authorization-Service/operation/fetchAuthorizationTokenUsingPost),
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

## Response

The response from
[`POST:/accesstoken/get`](https://developer.vippsmobilepay.com/api/access-token#tag/Authorization-Service/operation/fetchAuthorizationTokenUsingPost)
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

## Token endpoint: POST:/miami/v1/token

Some of the new APIs, including the
[Management API](https://developer.vippsmobilepay.com/docs/APIs/management-api/)
and
[Report API](https://developer.vippsmobilepay.com/docs/APIs/report-api/)
use a _new_ token endpoint:
[`POST:/miami/v1/token`](https://developer.vippsmobilepay.com/api/access-token/#tag/Authorization-Service/operation/fetchAuthorizationTokenUsingPost).

**Important:* This endpoint will be renamed to
`POST:/authentication/v1/token`
later, when the internal technical dependencies are resolved.

Authenticating with this endpoint is quite similar to the above mentioned flow, but this new endpoint
uses a completely standard OAuth client credentials flow, allowing use of standardized libraries.
We strongly recommend this approach, using one of the
[trusted libraries](https://oauth.net/code/)
to perform the flow. There should be no reason to implement this from scratch.

Example request to
[`POST:/miami/v1/token`](https://developer.vippsmobilepay.com/api/access-token/#tag/Authorization-Service/operation/fetchAuthorizationTokenUsingPost):

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

**Please note:** The `Ocp-Apim-Subscription-Key` HTTP header should _not_ be sent (unlike with the
[`POST:/accesstoken/get`](https://developer.vippsmobilepay.com/api/access-token#tag/Authorization-Service/operation/fetchAuthorizationTokenUsingPost)
endpoint).

Example (JSON) response from
[`POST:/miami/v1/token`](https://developer.vippsmobilepay.com/api/access-token/#tag/Authorization-Service/operation/fetchAuthorizationTokenUsingPost):

```json
{
  "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1Ni <truncated>",
  "token_type": "Bearer",
  "expires_in": 900
}

```