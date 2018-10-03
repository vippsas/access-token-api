# Vipps Access Token API

This is the API documentation for obtaining a Vipps Access Token.

The Swagger documentation is available at https://vippsas.github.io/vipps-accesstoken-api/.

Please use GitHub's built-in functionality for
[issues](https://github.com/vippsas/vipps-invoice-api/issues),
[pull requests](https://github.com/vippsas/vipps-invoice-api/pulls),
or contact us at integration@vipps.no.

Document version: 0.1.4.

# Obtaining an access token

A valid access token is required in order to call all Vipps APIs.
The Vipps APIs are provided by
[Azure API Management](https://docs.microsoft.com/en-us/azure/api-management/api-management-key-concepts) - think of it as the gateway to the API.

In order to obtain an access token, you will need to visit the Vipps Developer Portal and
retrieve your `client_id`, `client_secret` and `Ocp-Apim-Subscription-Key`:

| Name                        | Description                                 |
| --------------------------- | ------------------------------------------- |
| `client_id`                 | Client ID is a guid formatted string and is received when merchant registered the application. Sometime Vipps system register the application on behalf of merchant to ease the process. |
| `client_secret`             | Client Secret is a base 64 string and is received when merchant registered the application. Sometime Vipps system register the application on behalf of merchant to ease the process. |        
| `Ocp-Apim-Subscription-Key` | Subscription key. This is _the same key_ for the access token and the product.  |

See the Vipps Developer Portal
[getting started guide](https://github.com/vippsas/vipps-developers/blob/master/vipps-developer-portal-getting-started.md)
for more details.

## Request

Shortly summarized, you will have to make a request similar to the one below, with the placeholders replaced with real values:

```http
POST https://apitest.vipps.no/api/v2/accesstoken/jwt HTTP/1.1
Host: apitest.vipps.no
Content-Type: application/json
Ocp-Apim-Subscription-Key: <Ocp-Apim-Subscription-Key>

{
	"client_id":"<client_id>",
	"client_secret":"<client_secret>",
	"resource":"https://testapivipps.no/vippsas/invoice-isp-service
}

```

The `Ocp-Apim-Subscription-Key` is a required header, and it is the same subscription key as for the product itself.

The explicit `jwt` allows us to, at some point, offer different types of access tokens.

### Resource

This `resource` field indicates what resource your token should be generated for,
and this must be a valid Vipps product resource.

The following resources are currently available:

| Resource                    | Description                                 |
| --------------------------- | ------------------------------------------- |
| Recurring  |  https://testapivipps.no/vippsas/recurring-payment-service |
| Invoice ISP  | https://testapivipps.no/vippsas/invoice-isp-service  |
| Invoice IPP   |  https://testapivipps.no/vippsas/invoice-ipp-service |

## Response

The request above will return a response similar to this, with the generated `access_token`:

```http
HTTP 200 OK
{
  "token_type": "Bearer",
  "expires_in": "86398",
  "ext_expires_in": "0",
  "expires_on": "1495271273",
  "not_before": "1495184574",
  "resource": "00000002-0000-0000-c000-000000000000",
  "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1Ni <continued>"
}
```

### Details

| Name                        | Description                                 |
| --------------------------- | ------------------------------------------- |
| `Bearer`                    | It’s a `Bearer` token. The word `Bearer` should be added before the token, but this is optional and case insensitive in Vipps. |
| `expires_in`                | Token expiry duration in seconds. |
| `ext_expires_in`            | Extra expiry time. This is always zero. |
| `expires_on`                | Token expiry time in epoch time format. |
| `not_before`                | Token creation time in epoch time format. |
| `resource`                  | For the product for which token has been issued. |
| `access_token`              | The actual access token that needs to be used in `Authorization` request header. |

*Please note:* The access token is valid for 24 hours.

## Subsequent API calls

Every request to the API needs to have an `Authorization` header with the generated access token.

The header in all API requests should look like this:

```http
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1Ni <continued>
```

# HTTP responses

This API returns the following HTTP statuses in the responses:

| HTTP status         | Description                                 |
| ------------------- | ------------------------------------------- |
| `200 OK`            | Request successful                          |
| `400 Bad Request`   | Invalid request, see the error for details  |
| `401 Unauthorized`  | Invalid credentials                         |
| `403 Forbidden`     | Authentication ok, but credentials lacks authorization  |
| `500 Server Error`  | An internal Vipps problem.                  |

All error responses contains an `error` object in the body, with details of the problem.

# Questions or comments

Please use GitHub's built-in functionality for
[issues](https://github.com/vippsas/vipps-recurring-api/issues),
[pull requests](https://github.com/vippsas/vipps-recurring-api/pulls),
or contact us at integration@vipps.no.
