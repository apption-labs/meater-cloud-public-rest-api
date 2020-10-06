# MEATER Cloud REST API

> The MEATER Cloud REST API is in BETA.
>
> There may be bugs and changes.
>
> Watch this repository to keep up to date.
>
> [Issues, Feedback, Questions](#issues-feedback-questions)

## What You'll Need

* MEATER wireless thermometer. Get yours from the [MEATER store](https://meater.com/shop).
* MEATER Cloud account.

## API Basics

* Endpoint: `https://public-api.cloud.meater.com/v1`
* Rate limit:
  * Recommended: 2 requests per 60 seconds.
  * Maximum: 60 requests per 60 seconds.
* Slow Down:
  * To compliment the rate limit, requests are automatically slowed down once 10% of rate limit is reached.
  * Delay: Up to `1.0` second per request.
* Authentication:
  * JSON Web Tokens (JWT) are used.
  * Make a request to the `/login` endpoint to obtain a token.
  * Authenticated requests are made with the header `Authorization: Bearer <JWT>`
  * The JWT don't expire automatically but may be reset.
* Localisation:
  * English (US) is default.
  * Cook names can be translated (unless cook was given a name by the user) using the `Accept-Language` header with the [ISO_639-1](https://en.wikipedia.org/wiki/List_of_ISO_639-1_codes) language code. For example `Accept-Language: fr`.
  * The API supports languages supported by the MEATER app.

## Status Codes and Errors

The API uses standard HTTP status codes and also includes them in the JSON response in the `status` and `statusCode` attributes.

### Standard Response

* 200 OK

Example:

```json
{
    "status": "200 OK",
    "statusCode": 200,
    "data": { ... },
    "meta": { ... }
}
```

### Error Response

* 400 Bad Request
* 404 Not Found
* 429 Too Many Requests
* 500 Internal Server Error

Example:

```json
{
    "status": "404 Not Found",
    "statusCode": 404,
    "help": "https://github.com/apption-labs/meater-cloud-public-rest-api"
}
```

## API Endpoints

### [POST] /login

Headers:

```text
Content-Type: application/json
```

Body:

```json
{
    "email": "<MEATER Cloud Email Address>",
    "password": "<MEATER Cloud Password>"
}
```

Response:

```json
{
    "status": "OK",
    "statusCode": 200,
    "data": {
        "token": "<JWT>",
        "userId": "<User ID>"
    },
    "meta": {}
}
```

Example CURL:

```bash
curl --location --request POST 'https://public-api.cloud.meater.com/v1/login' \
--header 'Content-Type: application/json' \
--data-raw '{
  "email": "<MEATER Cloud Email Address>",
  "password": "<MEATER Cloud Password>"
}'
```

### [GET] /devices

Devices (aka MEATER probes) will only be returned after the following criteria is met. There may be a delay between the MEATER Cloud seeing your device and it being returned in this endpoint.

* Device must be seen by the MEATER Cloud. Ensure you've completed a cook while connected to MEATER Cloud.
* The MEATER app or Block must have an active Bluetooth connection with the device.
* The MEATER app or Block must have an active MEATER Cloud connection.

Additional information:

* All temperatures are Celsius.
* `elapsed` and `remaining` are in seconds.
* `updated_at` is a UNIX timestamp

Headers:

```text
Authorization: Bearer <JWT>
```

Response:

```json
{
    "status": "OK",
    "statusCode": 200,
    "data": {
        "devices": [
            {
                "id": "<Device ID>",
                "temperature": {
                    "internal": 0,
                    "ambient": 0,
                },
                "cook": {
                    "id": "<Cook ID>",
                    "name": "<Cook name>",
                    "state": "<Cook state>",
                    "time": {
                        "elapsed": 0,
                        "remaining": 0
                    },
                    "target": 0,
                    "peak": 0
                },
                "updated_at": 123456789
            },
            {
                "id": "<Device ID>",
                "temperature": {
                    "internal": 0,
                    "ambient": 0,
                },
                "cook": null,
                "updated_at": 123456789
            },
        ]
    },
    "meta": {}
}
```

Example CURL:

```bash
curl  --location --request GET 'https://public-api.cloud.meater.com/v1/devices' \
--header 'Authorization: Bearer <JWT>'
```

### [GET] /devices/{id}

Get info for a specific device. Works like `/devices` but returns a single object rather than an array.

Headers:

```text
Authorization: Bearer <JWT>
```

Response:

```json
{
    "status": "OK",
    "statusCode": 200,
    "data": {
        "id": "<Device ID>",
        "temperature": {
            "internal": 0,
            "ambient": 0,
        },
        "cook": null,
        "updated_at": 123456789
    },
    "meta": {}
}
```

Example CURL:

```bash
curl  --location --request GET 'https://public-api.cloud.meater.com/v1/devices/<ID>' \
--header 'Authorization: Bearer <JWT>'
```

## Issues, Feedback, Questions

Please use [issues](https://github.com/apption-labs/meater-cloud-rest-api/issues) for feedback, questions and issues.

The MEATER Support Team won't be able to provide support for the API.

## Legal

Copyright © 2020 Apption Labs Ltd. [Terms of Use](https://meater.com/terms-of-use).
