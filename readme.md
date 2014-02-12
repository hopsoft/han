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

Because HAN is concerned with resources, their states & transitions,
it makes no assumptions related to API consumers & does not presume to dictate UI concerns.

## Data Structures

#### Resource

```javascript
{
  han: true,       // indicates that this is a HAN resource
  version: "",     // the resource version
  name: "",        // the name of the resource
  value: {} || [], // the resource value
  action: {},      // [the action performed to obtain the resource]
  transitions: [], // the navigational actions that can be performed with the resource
  errors: []       // the errors encounted while obtaining the resource
}
```

##### Resource Value
A resource's value may be a List (as one-dimensional array) OR an Object (as in HashMap, Hash Table, Hash, Associative Array, etc.), but not both. Both value types contain metadata about their type. In the case that you provide a List you must provide a count of how many items are in the List. For an Object there is no count..

##### List

```javascript
{ ...
  value : {
    type: List, // the type of items
    count: 0,   // count of number of items in list
    items : []  // list of items
  }

}
```
###### Object

```javascript
{ ...
  value : {
    type: Object, // the type of items
    item: {}      // list of items
  }

}
```

#### Action

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

#### Error

```javascript
{
  name: "",       // the name of the error
  message: "",    // the error message
  backtrace: [],  // [the error's backtrace]
  inner_error: {} // [the inner error]
}
```

## Examples

#### Basic with Object

This example illustrates the create user response described above. It contains a value of Object type.

```javascript
{
  han: true,
  version: "v1"
  name: "User",
  value: { // the value is your customized object
    type: "Object",
    item: {"id" : 1, "name" : "Jon Doe"}
  },
  action: { // the action that was invoked to receive this response
    type: "hard",
    name: "Create User",
    href: "http://api.example.com/users",
    verbs: [ "POST" ],
    headers: {
      "Accept": "application/vnd.example.v1+json"
    },
    formats: [ "json" ],
    params: {
      name: "Jon Doe"
    }
  },
  transitions: [ // note: transition values are actions
    {
      type: "soft", // note: this is a soft transition (params demonstrate possiblities)
      name: "Update User",
      href: "http://api.example.com/users/1",
      verbs: [ "PUT" ],
      headers: {
        "Accept": "application/vnd.example.v1+json"
      },
      formats: [ "json" ],
      params: {
        name: "New Name" // params should be modified before making the transition
      }
    },
    {
      type: "hard", // note: this is a hard transition (must be called exactly as presented)
      name: "Delete User",
      href: "http://api.example.com/users/1",
      verbs: [ "DELETE" ],
      headers: {
        "Accept": "application/vnd.example.v1+json"
      },
      formats: [ "json" ],
      params: {}
    }
  ],
  errors: [],
}
```

#### Basic with List

This example illustrates the create user response described above. It contains a value of List type.

```javascript
{
  han: true,
  version: "v1"
  name: "User",
  value: { // the value is your customized object
    type: "List",
    count: 3,
    items: [{"id" : 333, "name" : "Han Solo"}]
  },
  action: { // the action that was invoked to receive this response
    type: "hard",
    name: "Create User",
    href: "http://api.example.com/users",
    verbs: [ "POST" ],
    headers: {
      "Accept": "application/vnd.example.v1+json"
    },
    formats: [ "json" ],
    params: {
      name: "Han Solo"
    }
  },
  transitions: [ // note: transition values are actions
    {
      type: "soft", // note: this is a soft transition (params demonstrate possiblities)
      name: "Update User",
      href: "http://api.example.com/users/333",
      verbs: [ "PUT" ],
      headers: {
        "Accept": "application/vnd.example.v1+json"
      },
      formats: [ "json" ],
      params: {
        name: "New Name" // params should be modified before making the transition
      }
    },
    {
      type: "hard", // note: this is a hard transition (must be called exactly as presented)
      name: "Delete User",
      href: "http://api.example.com/users/333",
      verbs: [ "DELETE" ],
      headers: {
        "Accept": "application/vnd.example.v1+json"
      },
      formats: [ "json" ],
      params: {}
    }
  ],
  errors: [],
}
```

#### Embedded HAN Resources

HAN resource values may contain embedded HAN resources.

![HAN Resource](https://raw2.github.com/hopsoft/han/master/resource.png)

```javascript
{
  // ...
}
```
