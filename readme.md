# HAN - Hypermedia API Navigation

### HAN is a specification designed to communicate navigation concerns for hypermedia APIs

This spec defines the data structures needed to represent a HAN compliant API response.

---

HAN treats your API as a state machine.
Think of each rendered resource as a state that can transition to another state.
Consider the following example.

![User Resources & Transitions](https://raw2.github.com/hopsoft/han/master/user-example.png)

* The **created-user** supports the `update` & `delete` transitions.
* The **updated-user** supports the `delete` transition.
* The **deleted-user** doesn't support any transitions.

---

*HAN makes no assumptions about API consumers & does not presume to dictate UI concerns.*

## Data Structures

#### Resource

```javascript
{
  version: "",     // the resource version
  name: "",        // the name of the resource
  value: {} || [], // the resource value
  action: {},      // [the action performed to obtain the resource]
  transitions: [], // the navigational actions that can be performed with the resource
  errors: []       // the errors encounted while obtaining the resource
}
```

#### Action

```javascript
{
  name: "",    // the name of the action
  type: "",    // the type of action [hard, soft]
  href: "",    // the url for the action
  verbs: [],   // the http verbs supported by the action [GET, POST, ...]
  headers: [], // the http headers required by the action
  params: {},  // the params supported by the action
  formats: [], // the formats supported by the action [json, xml, ...]
}
```

#### Error

```javascript
{
  name: "",       // the name of the error
  message: "",    // the error message
  backtrace: [],  // [the error's backtrace]
  inner_error: {} // [the inner error]
}
```
