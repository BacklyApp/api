# Table of contents

1. [About the API](#about)
2. [Authentication](#authorization)
3. [API](#api)
  1. [Links](#api-links)
    1. [Create](#api-links-create)
  2. [Adverts](#api-adverts)
    1. [Recent](#api-adverts-get)

<div id="#about"></div>

# About the API

Backly provides a stateless HTTP JSON API (`api.back.ly`) for the use of third-parties to interact with the service programatically on behalf of its users.

An example request for creating a Backly link:
```
POST /links HTTP/1.1
Host: api.back.ly
Authorization: Bearer ...
Accept: application/json
Content-Type: application/json
...

{
  "advert": "abc123",
  "url": "http://example.com?page=123#section"
}
```

## Authentication (OAuth)

The Backly API uses OAuth authorization to allow third-party apps access to the API on behalf of Backly users once authorized to do so.

## Encryption

It's strongly recommended that you use the API over a secure connection to protect the secrecy of your connection and your authorization tokens.

<div id="authorization"></div>

# Authorization

To authenticate an app's requets to the API it uses long lived Access Tokens granted by your app's users to access the API on their behalf. To obtain an access token you must be authorized by the user.

## Getting Started

You will need a few things before you will be able to make requests to our OAuth API.

- **App ID** and **App Secret**. These were automatically generated for you when your app was created.

- **Redirect URL**. This is a location on your app that the OAuth API can redirect a user back to once they have performed a successful authorization request with Backly. This must match the redirect URL that your app was created with and it must be updated if your app's redirect URL is changed.

## Requesting Authorization

First your app should direct a users browser to `http://app.back.ly/oauth/authorize` with the following query string parameters.

- `client_id`: Your App ID.
- `redirect_uri`: Your Redirect URL.
- `response_type`: Set to `code`.

For example:

```
http://app.back.ly/oauth/authorize?client_id=123&redirect_uri=http://my.app/oauth/callback&response_type=code
```

On this page users will be shown a prompt to authorize your app. If they accept they will be redirected back to the Redirect URL with the **Authorization Code** set in the query string parameter `code`.

For example:

```
http://my.app/callback?code=...
```

## Obtaining an Access Token

When a user is redirected back to your Redirect URL after a successful authorization request your app should then issue a `POST` request to `http://app.back.ly/oauth/token` with the Authorization Code, along with these other parameters in the body of the request.

- `client_id`: Your App ID.
- `client_secret`: Your App Secret.
- `code`: The Authorization Code returned by the successful authorization request.
- `grant_type`: Set to `authorization_code`.
- `redirect_uri`: Your Redirect URL.

For example:

```
POST /oauth/token HTTP/1.1
Host: app.back.ly.localhost
Content-Type: application/x-www-form-urlencoded
...

code=...
```

This request will return a JSON response containing an `access_token` token attribute. This **Access Token** can then be used to make API requests.

<div id="post-requets"></div>

### POST Requests

When making a `POST` request two formats are supported in the request body. Set your `Content-Type` header according to the format of the data you are sending.

- **URL-encoded** (`application/x-www-form-urlencoded`)
- **JSON** (`application/json`)

<div id="api"></div>

### Using the Access Token

To access the API with the Access Token you have been given you must use it as a bearer token in the `Authorization` header of every request you make.

For example:

```
POST /links HTTP/1.1
Authorization: Bearer <access-token>
...
```

# API

The Backly API returns data in JSON format and accepts either JSON or URL-encoded input for [`POST`ed data](#post-requets).

You must include an `Accept: application/json` header to ensure the JSON formatting of any error responses because the response will be formatted in HTML by default.

<div id="api-links"></div>

## Links

<div id="api-links-create"></div>

### Create `POST /links`

Create a new link.

#### Parameters

| Name | Type | Description |
|---|---|---|
| `advert` | String | The identifier code of the advert you wish to display with this link. |
| `url` | URL | The URL you wish to share. |

#### Example Request

```
POST /links HTTP/1.1
Host: api.back.ly
Authorization: Bearer ...
Accept: application/json
Content-Type: application/json
...

{
  "advert": "abc123",
  "url": "http://example.com?page=123#section"
}
```

#### Example Response

```json
{
  "created_at": "2015-01-01 12:00:00",
  "url": "http://example.com",
  "title": "Example Domain",
  "description": "This is an example website.",
  "image": "http://example.com/image.jpg",
  "code": "abc123",
  "short_url": "http://back.ly/abc123",
  "url_host": "example.com"
}
```

<div id="api-adverts"></div>

## Adverts

<div id="api-adverts-get"></div>

### Recent `GET /adverts/recent`

Retrieve a list of the most recently used adverts.

#### Parameters

| Name | Type | Description |
|---|---|---|
| `limit` | Integer | The number of adverts to limit the result to. The default value is 20. |
| `offset` | Integer | The number of adverts to offset the result by. The default value is 0. |

#### Example Request

```
GET /adverts/recent HTTP/1.1
Host: api.back.ly
Authorization: Bearer ...
Accept: text/json
...
```

#### Example Response

```json
[
  {
    "created_at": "2015-01-01 12:00:00",
      "archived_at": null,
      "data": {
        "colors": {
          "background": "ffffff",
          "button": {
            "background": "2196f3",
            "text": "e2f1fe"
          },
          "text": "333333"
        },
        "content": {
          "body": "<p>This area is the <b>body</b> of your message. It gives you more space to expand on what you have to say. 📣</p><p>You can use this place to tell your story 💕, describe yourself 😄 and what you do 👔 and <i>persuade people to click your <b>call-to-action button</b> and</i> <a>your links</a>! 🖱✨💰</p>",
          "button": {
            "text": "Click Here",
            "url": "http://back.ly"
          },
          "description": "I'm a ready-made message! Click to expand me."
        },
        "entry": {
          "animation": "fade"
        },
        "position": "bottom_left",
        "scripts": "",
        "size": "md",
        "style": "social_v2",
        "target": "blank",
        "wiggle": false
      },
      "clicks": 0,
      "code": "EGzvJyP5gG5MjABa",
      "ctr": 0,
      "impressions": 1,
      "brand": {
        "name": "Backly Dev",
        "image": "data:image/jpeg;base64,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",
        "code": "L7pQkYz46R4jNwqJ"
      }
  }
]
```
