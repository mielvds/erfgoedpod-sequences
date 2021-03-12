# Payloads used in notifications

## Dashboard 

### Activity types

#### [as:Create](https://www.w3.org/ns/activitystreams#Create)

A new dataset was created by the institution.

| actor  | [as:Organization](https://www.w3.org/ns/activitystreams#Organization) |
| object | [schema:Dataset](http://schema.org/Dataset) |
| origin | [schema:SoftwareApplication](https://schema.org/SoftwareApplication) |

Example:

``` json

{
  "@context": "https://www.w3.org/ns/activitystreams",
  "summary": "Koninklijke Bibliotheek announces Alba",
  "type": "Create",
  "actor": {
    "@context": "https://schema.org/",
    "@type": "Organization",
    "name": "Koninklijke Bibliotheek",
    "@id": "https://www.kb.nl/"
  },
  "object": {
    "@context": "https://schema.org/",
    "@type": "Dataset",
    "@id": "https://data.bibliotheken.nl/doc/dataset/rise-alba",
  },
  "origin": {
    "@context": "https://schema.org/",
    "@id": "http://example.org/Adlib",
    "@type": "SoftwareApplication"
  },
  "updated": "2021-01-28T19:56:20.114Z"
}

```


#### [as:Announce](https://www.w3.org/ns/activitystreams#Announce)

| target | [as:Service](https://www.w3.org/ns/activitystreams#Service) "|" [as:Organization](https://www.w3.org/ns/activitystreams#Organization) |

#### [as:Update](https://www.w3.org/ns/activitystreams#Update)
#### [as:Delete](https://www.w3.org/ns/activitystreams#Delete)




### Object types



## Service Hub ([as:Service](https://www.w3.org/ns/activitystreams#Service))

## Activity types

- [as:Accept](https://www.w3.org/ns/activitystreams#Accept)
- [as:Add](https://www.w3.org/ns/activitystreams#Add)


## Pod to Pod

## Pod to Service Hub

## Service Hub to Service Hub