# HAN - Hypermedia API Navigation

### A specification for communicating hypermedia API navigation concerns

HAN considers APIs to be state machines.
Think of each rendered resource as a state that can transition to another state.
Consider the following example.
*While somewhat contrived, it illustrates the concept well.*

![User Resources & Transitions](https://raw2.github.com/hopsoft/han/master/user-example.png)

* The created state supports the `update` & `delete` transitions.
* The updated state supports the `delete` transition.
* The deleted state doesn't support any transitions.

**Note**: The User resource is actually the same across all states.
The difference being the available transitions from each state.

---

*HAN makes no assumptions about API consumers & does not presume to dictate UI concerns.*

---

## Data Structures

#### Resource

```javascript
{
  han: true,       // indicates that this a HAN resource
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

## Complete Example

![HAN Resource](https://raw2.github.com/hopsoft/han/master/resource.png)

```javascript
{
  // coming soon...
}
```
