# HAN - Hypermedia API Navigation

HAN is a specification designed to communicate navigation concerns for hypermedia APIs.

The spec defines data structures to represent navigational options for a resource.
*It makes no assumptions about API consumers & does not presume to dictate UI concerns.*

## Data Structures

* Resource

  ```javascript
  {
    version: "string",    // the resource version
    name: "string",       // the name of the resource
    value: "object",      // the resource value
    action: "object",     // the action performed to obtain the resource
    transitions: "array", // the navigational actions that can be performed with the resource
    errors: "array"       // the errors encounted while obtaining the resource
  }
  ```

* Action

  ```javascript
  {
    name: "string",   // the name of the action
    type: "string",   // the type of action [hard, soft]
    href: "string",   // the url for the action
    verbs: "array",   // the http verbs supported by the action
    headers: "array", // the http headers required by the action
    params: "object", // the params supported by the action
    formats: "array", // the formats supported by the action [json, xml, ...]
  }
  ```

