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

All Vipps API requests must include an `Authorization` header with
a JSON Web Token (JWT), which we call the _access token_.
The Access token API allows you to get this token.

To make requests to the Vipps APIs you need to:

1. First make a request to
   [`POST: /accesstoken/get`](https://vippsas.github.io/vipps-developer-docs/api/access-token#tag/Authorization-Service/operation/fetchAuthorizationTokenUsingPost)
   to get an access token.
2. Use the access token from (1) in the HTTP header of the other API requests,
   together with the Vipps subscription key.

See
[Getting Started](https://vippsas.github.io/vipps-developer-docs/docs/vipps-developers/vipps-getting-started)
for information about API keys, product activation, how to make API calls, etc.

## Get an access token

The access token is obtained by calling
[`POST:/accesstoken/get`](https://vippsas.github.io/vipps-developer-docs/api/access-token#tag/Authorization-Service/operation/fetchAuthorizationTokenUsingPost)
and providing these values in the HTTP header:
* `client_id` (think of it as the username)
* `client_secret` (think of it as the password)
* `Ocp-Apim-Subscription-Key` (specifies which API products you can access)

Unfortunately, this is a `POST` without a body to an endpoint with
`get` in the URL. Too late to change it now, sorry.

## Request

A sample request to
[`POST:/accesstoken/get`](https://vippsas.github.io/vipps-developer-docs/api/access-token#tag/Authorization-Service/operation/fetchAuthorizationTokenUsingPost)
(including the
[Vipps HTTP headers](https://vippsas.github.io/vipps-developer-docs/docs/vipps-developers/common-topics/http-headers)
):

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
MSN (Merchant Serial Number, i.e. the unique id of the sale unit).

**Please note:** Partners should use
[partner keys](https://vippsas.github.io/vipps-developer-docs/docs/vipps-partner/partner-keys).

**Please note:** You can have multiple access tokens, and they can used at the
same time as long as they are valid.

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
  "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1Ni <truncated>"
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

You now have the access token and can make subsequent API calls with the following HTTP headers:
```
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

### Example of a decoded `access_token`

For the extra curious only.

This is an actual example `access_token` from the request payload described above:

```
eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ii1LSTNROW5OUjdiUm9meG1lWm9YcWJIWkdldyIsImtpZCI6Ii1LSTNROW5OUjdiUm9meG1lWm9YcWJIWkdldyJ9.eyJhdWQiOiIwMDAwMDAwMi0wMDAwLTAwMDAtYzAwMC0wMDAwMDAwMDAwMDAiLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC9lNTExNjUyNi01MWRjLTRjMTQtYjA4Ni1hNWNiNDcxNmJjNGIvIiwiaWF0IjoxNjc1NjkwODU2LCJuYmYiOjE2NzU2OTA4NTYsImV4cCI6MTY3NTY5NDc1NiwiYWlvIjoiRTJaZ1lOQi91THN5MlBXbjYxdjlQUjFuSC9zRUFRQT0iLCJhcHBpZCI6IjNhYTkxMTcyLTQwZDEtNDc1Yi1hMTFjLWU5YjhhOGY4MDFjMyIsImFwcGlkYWNyIjoiMSIsImlkcCI6Imh0dHBzOi8vc3RzLndpbmRvd3MubmV0L2U1MTE2NTI2LTUxZGMtNGMxNC1iMDg2LWE1Y2I0NzE2YmM0Yi8iLCJyaCI6IjAuQVNBQUptVVI1ZHhSRkV5d2hxWExSeGE4U3dJQUFBQUFBQUFBd0FBQUFBQUFBQUFnQUFBLiIsInRlbmFudF9yZWdpb25fc2NvcGUiOiJFVSIsInRpZCI6ImU1MTE2NTI2LTUxZGMtNGMxNC1iMDg2LWE1Y2I0NzE2YmM0YiIsInV0aSI6IjZmNk5EaVJ0VkV1SmlldFJUNHRiQUEiLCJ2ZXIiOiIxLjAiLCJ4bXNfdGRiciI6IkVVIn0.Ew0AnqoVClxz3nrvdGygGMP1VScsNfvitHwpxNF6qR9nvj5Ib_im2CuN2uKW6loQi40ZHzadfR-VGCgtmrU9nDl-qGuhHgauKa-PNOrF0nFpVtOM_WCF1jkal9u_0RsTtsorULOexxo-g_9V1AG9TwyoTBoKbCK6SeLyEAj_3wB2yxrwuXj2DkeL6oftg86wuZ2UvJ5WioMEHOHfGl-MJw-0Y1rkWQ-pHYJ-jcBI1xVvWBNx3sY7x-cDserfUOLByzw4G4v4UIc4LivNU2-0qARoqJ86tfAE0HZjVjVpNpkExpkYZRBe2wKQBCEiULiLICoAqtbbZsVL5zBxC5--ow
```

When decoded, the header contains:
```json
{
  "typ": "JWT",
  "alg": "RS256",
  "x5t": "-KI3Q9nNR7bRofxmeZoXqbHZGew",
  "kid": "-KI3Q9nNR7bRofxmeZoXqbHZGew"
}
```

And the body contains:
```json
{
  "aud": "00000002-0000-0000-c000-000000000000",
  "iss": "https://sts.windows.net/e5116526-51dc-4c14-b086-a5cb4716bc4b/",
  "iat": 1675690856,
  "nbf": 1675690856,
  "exp": 1675694756,
  "aio": "E2ZgYNB/uLsy2PWn61v9PR1nH/sEAQA=",
  "appid": "3aa91172-40d1-475b-a11c-e9b8a8f801c3",
  "appidacr": "1",
  "idp": "https://sts.windows.net/e5116526-51dc-4c14-b086-a5cb4716bc4b/",
  "rh": "0.ASAAJmUR5dxRFEywhqXLRxa8SwIAAAAAAAAAwAAAAAAAAAAgAAA.",
  "tenant_region_scope": "EU",
  "tid": "e5116526-51dc-4c14-b086-a5cb4716bc4b",
  "uti": "6f6NDiRtVEuJietRT4tbAA",
  "ver": "1.0",
  "xms_tdbr": "EU"
}
```

Problems? See:
[FAQ: Common errors](https://vippsas.github.io/vipps-developer-docs/docs/vipps-developers/faqs/common-errors-faq)
