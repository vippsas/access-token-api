# Vipps Invoice API

This is the API documentation for obtaining a Vipps Access Token.

Please use GitHub's built-in functionality for
[issues](https://github.com/vippsas/vipps-invoice-api/issues),
[pull requests](https://github.com/vippsas/vipps-invoice-api/pulls),
or contact us at integration@vipps.no.

Document version: 0.1.0.

# Getting an access token

A valid access token is required in order to call Vipps APIs.
The Vipps APIs are provided by Azure API Management - think of it as the gateway to the API.

Shortly summarized, you will have to make the following request
(`client_id`, `client_secret` and `Ocp-Apim-Subscription-Key` placeholders must be replaced with real values):

```http
POST https://apitest.vipps.no/api/access-token/jwt-token HTTP/1.1
Host: apitest.vipps.no
Content-Type: application/json
Ocp-Apim-Subscription-Key: <Ocp-Apim-Subscription-Key>

{
	"client_id":"<client_id>",
	"client_secret":"<client_secret>",
	"resource":"https://testapivipps.no/vippsas/invoice-isp-service
}

```

The `client_id`, `client_secret` and `Ocp-Apim-Subscription-Key` are available in the
Vipps Developer Portal. See
[the getting started guide](https://github.com/vippsas/vipps-developers/blob/master/vipps-developer-portal-getting-started.md).

The `resource` must be either `https://testapivipps.no/vippsas/invoice-isp-service` or `https://testapivipps.no/vippsas/invoice-ipp-service`.

The request above will return a response similar to this, with the `access_token`:

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

Every request to the API needs to have an `Authorization` header with the generated token.

The header in the request to this API should look like this:

```http
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1Ni <continued>
```

# Questions or comments

Please use GitHub's built-in functionality for
[issues](https://github.com/vippsas/vipps-recurring-api/issues),
[pull requests](https://github.com/vippsas/vipps-recurring-api/pulls),
or contact us at integration@vipps.no.
