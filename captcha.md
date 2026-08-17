# Captcha Endpoints
[Back to the list of all defined endpoints](endpoints.md)

## Main Endpoint
**/api/captcha**

Not implemented. At this time, only the `challenge` endpoint is available.

## Retrieve a challenge from the (ALTCHA) captcha service
**GET /api/captcha/challenge**

This endpoint is public and does not require authentication (see `@PreAuthorize("permitAll()")` on the
[AltchaCaptchaRestController](https://github.com/DSpace/DSpace/blob/master/dspace-server-webapp/src/main/java/org/dspace/app/rest/AltchaCaptchaRestController.java)).

It returns a JSON proof-of-work challenge that the browser must complete before submitting a captcha-protected
form (e.g. request-a-copy). The proof-of-work makes spam uneconomic without requiring
annoying puzzle tests or 3rd party services.

The response is not a HATEOAS resource with links, but a plain JSON object meant to be consumed and processed in the browser.

The JSON body will contain the algorithm, salt, signature, and challenge. An example is below:

```json
{
  "algorithm": "SHA-256",
  "salt": "dcf5eba26e",
  "challenge": "0d8dd34089fdd610bd9a8857ea1fa4a5f9fe4b53f5df0c4e1eff6dc987c4d2bf",
  "signature": "dfe4ec56f3d61e3a021b1c3b3ea4c7d6aea9812ab719ffe130fd386ce0b4158c"
}
```

An example curl call:
```
curl -i https://demo.dspace.org/server/api/captcha/challenge
```

The response also includes the `Cache-Control` header set to `private,no-cache` and the expiry of the challenge (default 1 hour) in the `Expires` header.

The endpoint behavior is driven by the [altcha.cfg](https://github.com/DSpace/DSpace/blob/master/dspace/config/modules/altcha.cfg)
configuration module:
* `altcha.algorithm` - the algorithm used to calculate the challenge. Default: `SHA-256`
* `altcha.hmac.key` - a secret key used to sign the challenge. It must be configured (e.g. in `local.cfg` or via an
  environment variable) before the challenge can be generated

For more information about ALTCHA challenge creation, see: https://altcha.org/api/operations/createchallenge/

The final result of the work is not submitted to this endpoint, but instead included with form data submitted in the captcha-protected form, for final verification and validation.

This endpoint does not relate to Google ReCaptcha, which is selected via the separate `captcha.provider` property (default `google`) and whose responses are validated server-side against Google's services. In this distribution the ALTCHA provider currently protects the request-a-copy form only, and only when `request.item.create.captcha` is enabled (registration is still protected by Google ReCaptcha, gated by `registration.verification.enabled`).

Return codes:
* 200 OK - if the operation succeeded and the JSON body is returned
* 400 Bad Request - if the `altcha.hmac.key` is not configured, or the algorithm is not supported
* 500 Internal Server Error - if the challenge or hmac hash cannot be calculated