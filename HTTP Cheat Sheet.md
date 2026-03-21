# 🌐 HTTP Cheat Sheet

A practical reference for **developers and backend engineers**: request/response format, methods, common headers, status codes, and caching—aligned with **RFC 9110** (semantics), **RFC 9111** (caching), and **RFC 9112** (HTTP/1.1 messaging).

---

## 📨 Example Request & Response

```http
GET /hello.txt HTTP/1.1
Host: www.example.com
User-Agent: Mozilla/5.0
Accept-Language: en, nl

```

```http
HTTP/1.1 200 OK
Date: Mon, 27 Jul 2020 12:28:53 GMT
Server: Apache/2.2.22
Content-Length: 12
Content-Type: text/plain

Hello World!
```

> [!NOTE]
> In HTTP/1.1, **Host** is required on requests (except some legacy cases). A blank line separates **headers** from the optional **message body**.

---

## 🧱 Message Structure (Quick)

| Part | Request | Response |
| :--- | :--- | :--- |
| **Start line** | `METHOD path HTTP/version` | `HTTP/version status reason` |
| **Headers** | `Name: value` (case-insensitive names) | Same |
| **Body** | Optional (e.g. POST JSON) | Optional (e.g. HTML, file) |

---

## 🔧 Methods

| Method | Safe¹ | Idempotent² | Typical meaning |
| :--- | :---: | :---: | :--- |
| **GET** | ✅ | ✅ | Retrieve current representation of a resource |
| **HEAD** | ✅ | ✅ | Same as GET but **no** response body |
| **POST** | ❌ | ❌ | Process the enclosed representation (often create / trigger actions) |
| **PUT** | ❌ | ✅ | Replace resource state with the enclosed representation |
| **DELETE** | ❌ | ✅ | Remove the resource (or its current representations) |
| **CONNECT** | ❌ | ❌ | Establish a tunnel (often TLS through HTTP proxy) |
| **OPTIONS** | ✅ | ✅ | Describe communication options for the target resource |
| **TRACE** | ✅ | ✅ | Loop-back diagnostic along the path (often **disabled** in production) |
| **PATCH** | ❌ | ❌³ | Partial modification (semantics depend on media type / API) |

¹ **Safe**: intended not to change server state (read-only semantics).  
² **Idempotent**: multiple identical requests have the same effect as one (from the server’s perspective).  
³ **PATCH** is not universally idempotent; depends on implementation.

> [!NOTE]
> For full normative definitions, see **RFC 9110**. APIs may document stronger guarantees than HTTP alone.

---

## 📤 Request Headers (Common)

| Header | Description |
| :--- | :--- |
| `Host: host[:port]` | Target authority; **required** in HTTP/1.1 for most requests |
| `Referer: <url>` | Where the request was linked from (note: historical spelling **Referer**) |
| `User-Agent: <string>` | Client software / library identity |
| `Authorization: <scheme> <params>` | Credentials (e.g. `Bearer …`, `Basic …`) |
| `Accept: <media-types>` | Acceptable response content types (e.g. `application/json`) |
| `Accept-Encoding: <codings>` | Acceptable compression (e.g. `gzip, br`) |
| `Accept-Language: <tags>` | Preferred natural languages (e.g. `en-US,en;q=0.9`) |
| `Content-Type: <type>` | Media type of the **body** (e.g. `application/json; charset=utf-8`) |
| `Content-Length: <n>` | Body size in bytes (often required when body present) |
| `Cookie: name=value; …` | Stored cookies for the origin |
| `Origin: <scheme>://host` | CORS-related; sent with many cross-origin requests |
| `If-None-Match`, `If-Modified-Since` | Conditional GET validation (caching) |
| `Range: bytes=…` | Request partial content (downloads / resumes) |

---

## 📥 Response Headers (Common)

| Header | Description |
| :--- | :--- |
| `Location: <url>` | Redirect target or URI of a **new** resource (often with `3xx` / `201`) |
| `Allow: GET, POST, …` | Methods supported for the resource (often with `405`) |
| `Server: <product>` | Server software identification (informational) |
| `Retry-After: <seconds \| HTTP-date>` | Suggested wait before retry (`429`, `503`, etc.) |
| `Age: <seconds>` | Proxy/cache-estimated age of the response |
| `Cache-Control: <directives>` | Freshness & storage rules for caches |
| `Expires: <HTTP-date>` | **Legacy** expiration time (prefer `Cache-Control` when possible) |
| `ETag: "<tag>"` | Opaque validator for conditional requests |
| `Last-Modified: <HTTP-date>` | Last modification time of the representation |
| `Content-Type: <type>` | Media type of the response body |
| `Content-Length: <n>` | Body length in bytes |
| `Set-Cookie: …` | Instruct client to store a cookie |
| `WWW-Authenticate: …` | Auth challenge (often with `401`) |

> [!NOTE]
> Caching behavior is defined in **RFC 9111**. Prefer **`Cache-Control`** over **`Expires`** for modern clients and CDNs.

---

## 🔢 Status Codes (Overview)

Detailed semantics: **RFC 9110**. Below: the codes you see most often in APIs and browsers.

---

### ℹ️ Informational (1xx)

| Code | Meaning |
| :--- | :--- |
| **100 Continue** | Initial part of request received; client may send body |
| **101 Switching Protocols** | Server agrees to protocol upgrade (e.g. WebSocket) |

---

### ✅ Success (2xx)

| Code | Meaning |
| :--- | :--- |
| **200 OK** | Request succeeded; response carries a representation (if applicable) |
| **201 Created** | Resource created; often includes `Location` |
| **202 Accepted** | Accepted for processing; not yet completed |
| **203 Non-Authoritative Information** | Payload from a transformed / non-origin source |
| **204 No Content** | Success with **no** body |
| **205 Reset Content** | Success; client should reset the document view |
| **206 Partial Content** | Success for a **range** request (`Content-Range`) |

---

### ↪️ Redirection (3xx)

| Code | Meaning |
| :--- | :--- |
| **300 Multiple Choices** | Multiple representations; client or user chooses |
| **301 Moved Permanently** | Resource has a **new permanent** URI |
| **302 Found** | Temporary redirect (historically misused like 303) |
| **303 See Other** | GET the resource at `Location` (common after POST) |
| **304 Not Modified** | Cached copy still valid (conditional GET) |
| **307 Temporary Redirect** | Temporary redirect; **do not** change method |
| **308 Permanent Redirect** | Permanent redirect; **do not** change method |

---

### ⚠️ Client Error (4xx)

| Code | Meaning |
| :--- | :--- |
| **400 Bad Request** | Malformed syntax or invalid framing |
| **401 Unauthorized** | Authentication required (see `WWW-Authenticate`) |
| **403 Forbidden** | Authenticated or not—access **denied** |
| **404 Not Found** | No current representation for the URI |
| **405 Method Not Allowed** | Method not supported; check `Allow` |
| **406 Not Acceptable** | Cannot satisfy `Accept` / conneg |
| **407 Proxy Authentication Required** | Authenticate with the proxy |
| **408 Request Timeout** | Server gave up waiting for the complete request |
| **409 Conflict** | Conflict with current resource state |
| **410 Gone** | Resource intentionally removed; do not retry indefinitely |
| **411 Length Required** | `Content-Length` (or equivalent) needed |
| **412 Precondition Failed** | Precondition header failed (e.g. `If-Match`) |
| **413 Content Too Large** | Request body exceeds server limit |
| **414 URI Too Long** | Request target too long |
| **415 Unsupported Media Type** | Body media type not supported |
| **416 Range Not Satisfiable** | Cannot satisfy `Range` |
| **417 Expectation Failed** | Cannot meet `Expect` |
| **421 Misdirected Request** | Request not intended for this origin (e.g. wrong SNI / HTTP/2) |
| **422 Unprocessable Content** | Understood syntax but semantic validation failed (common in APIs) |
| **426 Upgrade Required** | Client must switch protocol (see `Upgrade`) |
| **429 Too Many Requests** | Rate limited; often with `Retry-After` |

---

### 💥 Server Error (5xx)

| Code | Meaning |
| :--- | :--- |
| **500 Internal Server Error** | Unexpected server error |
| **501 Not Implemented** | Server does not support the required functionality |
| **502 Bad Gateway** | Invalid response from an **upstream** server |
| **503 Service Unavailable** | Temporarily unavailable; maintenance or overload |
| **504 Gateway Timeout** | Upstream did not respond in time |
| **505 HTTP Version Not Supported** | HTTP version not supported |

---

## 🍪 Cookies & Same-Origin (Pointers)

| Topic | Hint |
| :--- | :--- |
| **Set-Cookie** | `HttpOnly`, `Secure`, `SameSite=Strict\|Lax\|None` for security |
| **CORS** | Browser-enforced; server sends `Access-Control-*` on preflight (`OPTIONS`) |

---

## 🧪 Quick `curl` Examples

```bash
# GET with headers
curl -i https://api.example.com/v1/items

# POST JSON
curl -i -X POST https://api.example.com/v1/items \
  -H "Content-Type: application/json" \
  -d '{"name":"demo"}'

# Follow redirects, show method
curl -iL --trace-ascii - https://example.com/
```

---

## 📌 Quick Reference Table

| Task | Typical choice |
| :--- | :--- |
| Fetch resource | `GET` + `200` |
| Create resource | `POST` + `201` + `Location` |
| Replace resource | `PUT` + `200` or `204` |
| Delete resource | `DELETE` + `204` or `200` |
| Validation errors (APIs) | `400` or `422` |
| Auth required | `401` + `WWW-Authenticate` |
| Not allowed | `403` |
| Rate limit | `429` + `Retry-After` |
| Check alive / metadata | `HEAD` or `OPTIONS` |
| Cache validation | `304` + `ETag` / `Last-Modified` |

---

## 📚 Normative References

| RFC | Topic |
| :--- | :--- |
| **RFC 9110** | HTTP semantics (methods, status codes, fields) |
| **RFC 9111** | HTTP caching |
| **RFC 9112** | HTTP/1.1 message syntax and routing |
