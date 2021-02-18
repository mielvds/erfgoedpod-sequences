Register-function: Solid-compatible API 
==============================

# <a id="intro">Introduction</a>

This document outlines an _application programming interface_ (API) for NDE's Register iteroperable with the Solid ecosystem.

# <a id="design-principles">Design principles</a>

The design principles extend thos in [v4 of the Register function API](https://github.com/netwerk-digitaal-erfgoed/registry-poc/blob/master/api-4.md):

1. The API uses modern API standards and best practices
2. The API uses [Linked Data Platform](https://www.w3.org/TR/ldp/) (LDP) for HTTP operations on web resources.
3. The API uses [Linked Data Notifications](https://www.w3.org/TR/ldn/) (LDN) for communicating actions in a loose-coupled way. LDN is a specialized use of LDP for sending and consuming notifications.
4. The API uses [JSON-LD](http://json-ld.org/) as the preferred format for transmitting data.
5. The API uses established Linked Data vocabularies, one of which is the (schema.org based) [NDE publication model](https://netwerk-digitaal-erfgoed.github.io/requirements-datasets/)
### Discover the inbox URL with HEAD

You can use Linked Data Notifications to communicate with the Register, which need to be delivered to an Inbox (an LDP Basic Container). The client discovers the inbox by following a link from any web resource with the relation `http://www.w3.org/ns/ldp#inbox`.

#### Request

``` HTTP
HEAD https://demo.netwerkdigitaalerfgoed.nl/register-api
```

#### Response

##### Headers
Key | Value
--|--
Link | ```<https://demo.netwerkdigitaalerfgoed.nl/register-api/inbox/>; rel="http://www.w3.org/ns/ldp#inbox"```

##### Errors
Status | Error description
--|--
404 Not Found | The URL does not exist.

### Discover the inbox URL with GET

#### Request

``` HTTP
GET https://demo.netwerkdigitaalerfgoed.nl/register-api
```

##### Headers
Key | Value
--|--
Accept | application/ld+json

#### Response

##### Headers
Key | Value
--|--
Content-Type | application/ld+json

##### Body (example)

``` json
{
  "@context": "http://www.w3.org/ns/ldp",
  "@id": "https://demo.netwerkdigitaalerfgoed.nl/register-api",
  "inbox": "https://demo.netwerkdigitaalerfgoed.nl/register-api/inbox/"
}
```

##### Errors
Status | Error description
--|--
404 Not Found | The URL does not exist.

### <a id="endpoint-dataset-register">Register a URL of a dataset description or catalog</a>

#### Request

<code>POST https://demo.netwerkdigitaalerfgoed.nl/register-api/inbox/</code>

##### Headers
Key | Value
--|--
Host | demo.netwerkdigitaalerfgoed.nl
Content-Type | application/ld+json;profile="https://www.w3.org/ns/activitystreams"
Content-Language | en

##### Body (example)


``` json
{
  "@context": "https://www.w3.org/ns/activitystreams",
  "summary": "Koninklijke Bibliotheek announces Alba",
  "type": "Add",
  "actor": {
    "@context": "https://schema.org/",
    "@type": "Organization",
    "name": "Koninklijke Bibliotheek",
    "id": "https://www.kb.nl/"
  },
  "object": "https://demo.netwerkdigitaalerfgoed.nl/datasets/kb/2.html",
  "target": "https://demo.netwerkdigitaalerfgoed.nl/register-api",
  "updated": "2021-01-28T19:56:20.114Z"
}
```



#### Response sync

##### Statuscode

Status | Description
--|--
`201 Created` | Notification was received and processed.
##### Headers
Key | Value
--|--
Location | https://demo.netwerkdigitaalerfgoed.nl/register-api/inbox/5c6ca040
Link | `<https://demo.netwerkdigitaalerfgoed.nl/register-api/payloads.shacl.ttl>; rel="http://www.w3.org/ns/ldp#constrainedBy",`
     | `<http://www.w3.org/ns/ldp#BasicContainer>; rel="type",`
     | `https://demo.netwerkdigitaalerfgoed.nl/register-api/organizations/4/datasets/3; rel="http://www.w3.org/2000/01/rdf-schema#seeAlso"`


#### Response async (alternative scenario - possible confusion between notification resource and description resource)

##### Statuscode

Status | Description
--|--
`202 Accepted` | Notification was received and is being processed asynchronously.
##### Headers
Key | Value
--|--
Link | `<https://demo.netwerkdigitaalerfgoed.nl/register-api/payloads.shacl.ttl>; rel="http://www.w3.org/ns/ldp#constrainedBy",`
     | `<http://www.w3.org/ns/ldp#BasicContainer>; rel="type",`

##### Body (example)

###### URL added, will be crawled soon

``` json
{
  "notification": "https://demo.netwerkdigitaalerfgoed.nl/register-api/inbox/5c6ca040",
  "dataset": {
    "@id": "https://demo.netwerkdigitaalerfgoed.nl/register-api/organizations/4/datasets/3",
    "status":"added"
  }
}
```

###### URL already known, will be crawled soon

``` json
{
  "notification": "https://demo.netwerkdigitaalerfgoed.nl/register-api/inbox/5c6ca040",
  "dataset": {
    "@id": "https://demo.netwerkdigitaalerfgoed.nl/register-api/organizations/4/datasets/3",
    "status":"updated"
  }
}
```
###### URL was deleted (because URL already known and HEAD request to URL results in a 404)

``` json
{
  "notification": "https://demo.netwerkdigitaalerfgoed.nl/register-api/inbox/5c6ca040",
  "dataset": {
    "@id": "https://demo.netwerkdigitaalerfgoed.nl/register-api/organizations/4/datasets/3",
    "status":"deleted"
  }
}
```

#### Errors

##### Statuscode

Status | Error description
--|--
`404 Not Found` | The inbox URL does not exist.
`400 Bad Request` | The notification payload cannot be parsed or does not adhere to the given SHACL constraints.

##### Headers
Key | Value
--|--
Link | `https://demo.netwerkdigitaalerfgoed.nl/shapes/register-api; rel="http://www.w3.org/ns/ldp#constrainedBy"`

### <a id="endpoint-dataset-search">Read all dataset descriptions</a>

#### Request

<code>GET https://demo.netwerkdigitaalerfgoed.nl/register-api/organizations/4/datasets</code>

##### Headers
Key | Value
--|--
Accept | application/ld+json

#### Response

##### Statuscode

Status | Description
--|--
200 OK | Dataset descriptions are retrieved

##### Headers
Key | Value
--|--
ETag | {etagValue}
Content-Type | application/ld+json
Accept-Post | application/ld+json
Allow | POST,GET
Link | `<http://www.w3.org/ns/ldp#BasicContainer>; rel="type", <http://www.w3.org/ns/ldp#Resource>; rel="type",`
     | `<https://demo.netwerkdigitaalerfgoed.nl/register-api/inbox/>; rel="http://www.w3.org/ns/ldp#inbox"`
##### Body (example)

``` json
{
  "@context": {
    "dcterms": "http://purl.org/dc/terms/",
    "ldp": "http://www.w3.org/ns/ldp#"
  },
  "@id": "/organizations/4/datasets",
  "@type": ["ldp:Container", "ldp:BasicContainer"],
  "dcterms:title": "Datasets of Koninklijke Bibliotheek",
  "ldp:contains": [
    {"@id": "/organizations/4/datasets/1"},
    {"@id": "/organizations/4/datasets/2"},
    {"@id": "/organizations/4/datasets/3"}
  ]
}
```

##### Errors
Status | Error description
--|--
400 Bad Request | The request is invalid
404 Not Found | The resource does not exist

### <a id="endpoint-dataset-get">Get a dataset description</a>

#### Request

<code>GET https://demo.netwerkdigitaalerfgoed.nl/register-api/organizations/4/datasets/3</code>

##### Headers
Key | Value
--|--
Accept | application/ld+json

#### Response

##### Headers

Key | Value
--|--
Status | 200 OK
ETag | {etagValue}
Content-Type | application/ld+json
Accept-Post | application/ld+json
Allow | POST,GET
Link | `<http://www.w3.org/ns/ldp#BasicContainer>; rel="type",<http://www.w3.org/ns/ldp#Resource>; rel="type"`
     | `<https://demo.netwerkdigitaalerfgoed.nl/register-api/inbox/>; rel="http://www.w3.org/ns/ldp#inbox"`


##### Body (example)



## Notifications send by the API


### 


``` json
{
  "@context": "https://www.w3.org/ns/activitystreams",
  "summary": "Koninklijke Bibliotheek announces Alba",
  "type": "Accept",
  "actor": {
    "@context": "https://schema.org/",
    "@type": "WebAPI",
    "name": "Register API",
    "id": "https://demo.netwerkdigitaalerfgoed.nl/register-api"
  },
  "object": "https://demo.netwerkdigitaalerfgoed.nl/datasets/kb/2.html",
  "target": {
    "@context": "https://schema.org/",
    "@type": "Organization",
    "name": "Koninklijke Bibliotheek",
    "id": "https://www.kb.nl/"
  },
  "updated": "2021-01-28T19:56:20.114Z"
}
```

``` json
{
  "@context": "https://www.w3.org/ns/activitystreams",
  "summary": "Koninklijke Bibliotheek announces Alba",
  "type": "Create",
  "actor": {
    "@context": "https://schema.org/",
    "@type": "WebAPI",
    "name": "Register API",
    "id": "https://demo.netwerkdigitaalerfgoed.nl/register-api"
  },
  "object": "https://demo.netwerkdigitaalerfgoed.nl/datasets/kb/2.html",
  "target": "https://www.kb.nl/",
  "updated": "2021-01-28T19:56:20.114Z"
}
```