---
layout: doc
title: Templates
---
# {{ page.title }}

In Hurl file, you can generate value using two curly braces, i.e {% raw %}`{{my_variable}}`{% endraw %}. For instance, if you want to 
reuse a value from a HTTP response in the next entries, you can capture this value in a variable and reuse it in a 
template.

{% raw %}
```hurl
GET https://example.net

HTTP/1.1 200
[Captures]
csrf_token: xpath "string(//meta[@name='_csrf_token']/@content)"

# Do the login !
POST https://acmecorp.net/login?user=toto&password=1234
X-CSRF-TOKEN: {{csrf_token}}

HTTP/1.1 302
```
{% endraw %}

In this example, we capture the value of the [CSRF token](https://en.wikipedia.org/wiki/Cross-site_request_forgery) from
the body a first response, and inject it as a header in the next POST request. 

{% raw %}
```hurl
GET http://api.example.net/index
HTTP/* 200
[Captures]
index: body

GET http://api.example.net/status
HTTP/* 200
[Asserts]
jsonpath "$.errors[{{index}}].id" equals "error"
```
{% endraw %}

In this second example, we capture the body in a variable `index`, and reuse this value in the query 
{% raw %}`jsonpath "$.errors[{{index}}].id"`{% endraw %}.

Variables can also be injected in a Hurl file, by using `--variable` option :

```
$ hurl --variable host=example.net test.hurl
``` 

where `test.hurl` is the following file:

{% raw %}
```hurl
GET https://{{host}}/status
HTTP/1.1 304

GET https://{{host}}/health
HTTP/1.1 200
```
{% endraw %}

## Templating Body

Using templates with [JSON body]({% link _docs/request.md %}#json-body) or [XML body]({% link _docs/request.md %}#xml
-body)
 is not currently supported in Hurl. Besides, you can use templates in [raw string body]({% link _docs/request.md %}#raw
 -string-body) with variables to send a JSON or XML body:

{% raw %}
~~~hurl
PUT https://api.example.net/hits
Content-Type: application/json
```
{
    "key0": "{{a_string}}",
    "key1": {{a_bool}},
    "key2": {{a_null}},
    "key3": {{a_number}}
}
```
~~~
{% endraw %}

Variables can be initialized via command line:

```bash
$ hurl --variable key0=apple --variable key1=true --variable key2=null --variable key3=42 test.hurl
```

Resulting in a PUT request with the following JSON body:

```
{
    "key0": "apple",
    "key1": true,
    "key2": null,
    "key3": 42
}
```
