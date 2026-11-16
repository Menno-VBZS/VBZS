# VBZS_ResponseCode

## Description

`VBZS_ResponseCode` extracts the HTTP response code from a response header returned by FileMaker's `Insert from URL` using the cURL `-D` option.

The function creates two FileMaker variables:

```text
$responseCode
$responseMessage
```

`$responseCode` contains the numeric HTTP response code.

`$responseMessage` contains the standard response message associated with that code.

For example, an HTTP response code of `200` results in:

```text
$responseCode    = 200
$responseMessage = OK
```

The two variables have fixed names. `VBZS_ResponseCode` does not provide a namespace option.

## Syntax

```text
VBZS_ResponseCode ( header )
```

## Parameters

### `header`

The response header returned by an `Insert from URL` operation using the cURL `-D` option.

The function looks for an HTTP response beginning with:

```text
HTTP/
```

followed by a numeric response code after the first space.

## Result

When a valid and recognized HTTP response is found, the function creates:

```text
$responseCode
$responseMessage
```

For example:

```text
HTTP/1.1 200 OK
```

produces:

```text
$responseCode    = 200
$responseMessage = OK
```

The response message is determined by `VBZS_ResponseCode` from its internal list of known HTTP response codes. It does not depend on the server supplying exactly the same response text in the header.

## Multiple HTTP responses

A response header may contain more than one HTTP response.

This can occur, for example, when a request is redirected and the collected headers contain responses from more than one stage of the request.

When multiple responses beginning with:

```text
HTTP/
```

are present, `VBZS_ResponseCode` processes the last response.

For example, a header containing responses such as:

```text
HTTP/1.1 301 Moved Permanently
...

HTTP/1.1 200 OK
...
```

is processed as:

```text
$responseCode    = 200
$responseMessage = OK
```

The final HTTP response is therefore the one represented by the generated variables.

## Invalid response header

If the supplied value does not contain a valid HTTP response header, the function returns:

```text
Missing HTTP response header
```

A header is considered invalid when the required HTTP response cannot be identified, for example when:

- no response starts with `HTTP/`;
- no numeric response code can be found after the first space.

When an invalid header is processed, both variables are cleared:

```text
$responseCode    = ""
$responseMessage = ""
```

The variables are written on every call. Values left by an earlier call are therefore not retained when the current response header is invalid.

## Unknown response code

If a valid HTTP response header is found but its numeric response code is not known by `VBZS_ResponseCode`, the function returns:

```text
Undefined error
```

This distinguishes an unknown HTTP response code from a missing or malformed HTTP response header.

## Supported response messages

`VBZS_ResponseCode` contains predefined messages for the following HTTP response codes.

### Informational responses

| Code | Message |
|---:|---|
| 100 | Continue |
| 101 | Switching Protocols |

### Successful responses

| Code | Message |
|---:|---|
| 200 | OK |
| 201 | Created |
| 202 | Accepted |
| 203 | Non-Authoritative Information |
| 204 | No Content |
| 205 | Reset Content |
| 206 | Partial Content |

### Redirection responses

| Code | Message |
|---:|---|
| 300 | Multiple Choices |
| 301 | Moved Permanently |
| 302 | Found |
| 303 | See Other |
| 304 | Not Modified |
| 305 | Use Proxy |
| 306 | Unused |
| 307 | Temporary Redirect |

### Client error responses

| Code | Message |
|---:|---|
| 400 | Bad Request |
| 401 | Unauthorized |
| 402 | Payment Required |
| 403 | Forbidden |
| 404 | Not Found |
| 405 | Method Not Allowed |
| 406 | Not Acceptable |
| 407 | Proxy Authentication Required |
| 408 | Request Timeout |
| 409 | Conflict |
| 410 | Gone |
| 411 | Length Required |
| 412 | Precondition Failed |
| 413 | Request Entity Too Large |
| 414 | Request-URI Too Long |
| 415 | Unsupported Media Type |
| 416 | Requested Range Not Satisfiable |
| 417 | Expectation Failed |

### Server error responses

| Code | Message |
|---:|---|
| 500 | Internal Server Error |
| 501 | Not Implemented |
| 502 | Bad Gateway |
| 503 | Service Unavailable |
| 504 | Gateway Timeout |
| 505 | HTTP Version Not Supported |

## Example

After an `Insert from URL` operation using the cURL `-D` option, suppose `header` contains a response ending in:

```text
HTTP/1.1 200 OK
Content-Type: application/json
...
```

The calculation:

```filemaker
VBZS_ResponseCode ( header )
```

creates:

```text
$responseCode    = 200
$responseMessage = OK
```

These variables can subsequently be used directly by the FileMaker script.

For example:

```filemaker
If [ $responseCode = 200 ]
    # Process successful response
End If
```

## Response messages

`$responseMessage` is based on the numeric response code rather than relying on the response message supplied by the remote server.

This provides a consistent message for the HTTP codes recognized by the function.

For example, response code:

```text
404
```

produces:

```text
$responseMessage = Not Found
```

and:

```text
503
```

produces:

```text
$responseMessage = Service Unavailable
```

## Notes

- The function is intended for response headers obtained using the cURL `-D` option with FileMaker's `Insert from URL`.
- The response must contain a line beginning with `HTTP/`.
- The numeric response code is read from the HTTP response line.
- `$responseCode` contains the numeric HTTP response code.
- `$responseMessage` contains the predefined message corresponding to a recognized response code.
- The variables always have the names `$responseCode` and `$responseMessage`.
- Namespaces are not supported.
- When several HTTP responses are present, the last response beginning with `HTTP/` is used.
- Both variables are written on every invocation.
- An invalid response header clears both variables.
- An invalid or missing HTTP response returns `Missing HTTP response header`.
- An unknown response code returns `Undefined error`.

## Design philosophy

FileMaker can return response headers from `Insert from URL` using the cURL `-D` option, but using the HTTP status in subsequent script logic normally requires extracting and interpreting the response line.

`VBZS_ResponseCode` reduces this to two immediately usable FileMaker variables:

```text
$responseCode
$responseMessage
```

The function also handles response headers containing multiple HTTP responses by using the final response, which represents the result relevant to the completed request.

The response message is derived from a predefined mapping rather than depending on the text supplied by the remote server.

This keeps subsequent FileMaker script logic simple: the numeric response code can be used for decisions while the corresponding message is immediately available when a human-readable description is useful.