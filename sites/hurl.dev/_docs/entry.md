---
layout: doc
title: Entry
---
# {{ page.title }}

## Definition {#definition}

A Hurl file is a list of entry, each entry being a mandatory [request]({% link _docs/request.md %}), 
optionally followed by a [response]({% link _docs/response.md %}). 

Responses are not mandatory, a Hurl file consisting only of requests is perfectly valid. To sum up, responses can be used 
to capture values to perform subsequent requests, or add asserts to HTTP responses.

## Example {#example}

```hurl
# First, test home title.
GET https://acmecorp.net

HTTP/1.1 200
[Asserts]
xpath "normalize-space(//head/title)" equals "Hello world!"

# Get some news, response description is optional
GET https://acmecorp.net/news

# Do a POST request without csrf token and check
# that status code is Forbidden 403
POST https://acmecorp.net/contact
[FormParams]
default: false
email: john.doe@rookie.org
number: 33611223344

HTTP/1.1 403
```

## Description {#description}

### Cookie storage {#cookie-storage}

Requests in the same Hurl file share the cookie storage, enabling, for example, session based scenario.

### Redirects {#redirects}

Hurl runner doesn't follow redirection. To effectively run a redirection, entries should describe each step
of the redirection, allowing insertion of asserts in each response.

```hurl
# First entry, test the redirection (status code and
# Location header)
GET http://google.fr

HTTP/1.1 301
Location: http://www.google.fr/

# Second entry, the 200 OK response
GET http://www.google.fr

HTTP/1.1 200
``` 
