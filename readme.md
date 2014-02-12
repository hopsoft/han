![HAN Solo](https://raw2.github.com/hopsoft/han/master/han.gif)

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

Because HAN is concerned with resources (their states & transitions),
it makes no assumptions related to API consumers & does not presume to dictate UI concerns.

## Data Structures

### Resource

A resource is a container that wraps a custom value & includes hypermedia meta data.

```javascript
{
  han: true,       // indicates that this is a HAN resource
  version: "",     // the resource version
  name: "",        // the name of the resource
  value: {},       // the resource value
  action: {},      // [the action performed to obtain the resource]
  transitions: [], // the navigational actions that can be performed with the resource
  errors: []       // the errors encounted while obtaining the resource
}
```

#### Resource Values

Resource values may contain an object (associative array) or a list (array).
Values contain metadata that describe their contents.

##### Object Value

```javascript
{
  value: {
    type: "object", // the type of value [list, object]
    item: {}        // the item
  }
}
```

##### List Value

```javascript
{
  value: {
    type: "list", // the type of value [list, object]
    count: 0,     // the number of items in the list (required for list types)
    items: []     // this list of items
  }
}
```

### Action

An action describes all necessary info required to make a transition for a given resource.

```javascript
{
  type: "",    // the type of action [hard, soft]
  name: "",    // the name of the action
  href: "",    // the url for the action
  verbs: [],   // the http verbs supported by the action [GET, POST, ...]
  headers: [], // the http headers required by the action
  formats: [], // the formats supported by the action [json, xml, ...]
  params: {},  // the params supported by the action
}
```

### Error

```javascript
{
  name: "",       // the name of the error
  message: "",    // the error message
  backtrace: [],  // [the error's backtrace]
  inner_error: {} // [the inner error]
}
```

## Examples

### Basic ([object value](#object-value))

This example illustrates the "create user" response described above.

```javascript
{
  han: true,
  version: "v1",
  name: "User",
  value: {
    type: "object",
    item: { // the item is your custom object
      id: 1,
      name: "Han Solo",
      team: "Rebel Alliance"
    }
  },
  action: { // the action that was invoked to receive this response
    type: "hard",
    name: "Create User",
    href: "http://api.example.com/users",
    verbs: [ "POST" ],
    headers: {
      Accept: "application/vnd.example.v1+json"
    },
    formats: [ "json" ],
    params: {
      name: "Han Solo",
      team: "Rebel Alliance"
    }
  },
  transitions: [ // note: transition values are actions
    {
      type: "soft", // note: this is a soft transition (params demonstrate possiblities)
      name: "Update User",
      href: "http://api.example.com/users/1",
      verbs: [ "PUT" ],
      headers: {
        Accept: "application/vnd.example.v1+json"
      },
      formats: [ "json" ],
      params: { // params should be modified before making the transition
        name: "New Name",
        team: "New Team"
      }
    },
    {
      type: "hard", // note: this is a hard transition (must be called exactly as presented)
      name: "Delete User",
      href: "http://api.example.com/users/1",
      verbs: [ "DELETE" ],
      headers: {
        Accept: "application/vnd.example.v1+json"
      },
      formats: [ "json" ],
      params: {}
    }
  ],
  errors: [],
}
```

### Basic ([list value](#list-value))

This example illustrates a "find users" call.

```javascript
{
  han: true,
  version: "v1",
  name: "User",
  value: {
    type: "list",
    count: 3,
    items: [ // the items are your custom objects
      {
        id: 1,
        name: "Han Solo",
        team: "Rebel Alliance"
      },
      {
        id: 2,
        name: "Luke Skywalker",
        team: "Rebel Alliance"
      },
      {
        id: 3,
        name: "Princess Leia",
        team: "Rebel Alliance"
      }
    ]
  },
  action: { // the action that was invoked to receive this response
    type: "hard",
    name: "Find Users",
    href: "http://api.example.com/users",
    verbs: [ "POST" ],
    headers: {
      Accept: "application/vnd.example.v1+json"
    },
    formats: [ "json" ],
    params: {
      team: "Rebel Alliance"
    }
  },
  transitions: [] // note: no transitions are available for the search result itself
  errors: [],
}
```

### Embedded HAN Resources

HAN resource values may contain embedded HAN resources.

![HAN Resource](https://raw2.github.com/hopsoft/han/master/resource.png)

```javascript
{
  // example soon...
}
```
