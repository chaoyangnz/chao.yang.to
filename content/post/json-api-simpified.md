# Content Negotiation
`Content-Type: application/vnd.api+json`
`Accept: application/vnd.api+json`

media type without parameter

# Document Structure

3 top-level members:
- `data`: the document's 'primary data'
- `errors`: an array of error objects
- `meta`: non-standard meta-information

`data` and `errors` MUST NOT coexists.

Optional top-level memeber:
- `jsonapi`: describe the server's implementation
- `links`: links related to the primary data
- `included`: included resources

## resource object
- `id`
- `type`
Optional:
- `attributes`
- `relationships`
- `links`
- `meta`

```
// ...
{
  "type": "articles",
  "id": "1",
  "attributes": {
    "title": "Rails is Omakase"
  },
  "relationships": {
    "author": {
      "links": {
        "self": "/articles/1/relationships/author",
        "related": "/articles/1/author"
      },
      "data": { "type": "people", "id": "9" }
    }
  }
}
// ...
```

A resource object's `attributes` and its `relationships` are collectively called its `fields`.

Fields of a resource object MUST share a common namespace with each other and with `type` and `id`.



