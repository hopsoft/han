# HAN - Hypermedia API Navigation

HAN is a specification designed to communicate navigation concerns for hypermedia APIs.

The spec defines data structures to represent navigational options for a resource.
*It makes no assumptions about API consumers & does not presume to dictate UI concerns.*

## Data Structures

* Resource

  ```javascript
  {
    version: "",     // the resource version
    name: "",        // the name of the resource
    value: {} || [], // the resource value
    action: {},      // the action performed to obtain the resource
    transitions: [], // the navigational actions that can be performed with the resource
    errors: []       // the errors encounted while obtaining the resource
  }
  ```

* Action

  ```javascript
  {
    name: "",    // the name of the action
    type: "",    // the type of action [hard, soft]
    href: "",    // the url for the action
    verbs: [],   // the http verbs supported by the action
    headers: [], // the http headers required by the action
    params: {},  // the params supported by the action
    formats: [], // the formats supported by the action [json, xml, ...]
  }
  ```

