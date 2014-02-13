# HAN - Hypermedia API Navigation

![HAN Solo](https://raw2.github.com/hopsoft/han/master/han.gif)

> Traveling through hyperspace ain't like dusting crops, boy!
> Without precise calculations we could fly right through a star or bounce too close to a supernova, and that'd end your trip real quick, wouldn't it?

---

If you regard APIs as state machines,
you'll discover that responses include resource(s) which are in a distinct state.
These resource(s) can typically transition to other states.

**HAN facilitates this behavior by helping communicate the navigational possibilities.**

## State Machine Example

Consider the following.

![User Resources & Transitions](https://raw2.github.com/hopsoft/han/master/user-example.png)

* The created state supports the `update` & `delete` transitions.
* The updated state supports the `delete` transition.
* The deleted state doesn't support any transitions.

**Note:** *The resource value is the same across all states.*
*The possible transitions are the only difference.*

---

HAN does not make assumptions about API consumers.
Neither does it presume to dictate UI concerns.

---

## Data Structures

### Response

```javascript
{
  han_version: "",    // the version of the HAN spec being used
  han_spec: "",       // the href to the HAN spec
  api_version: "",    // the version of the API being used
  api_spec: "",       // the href to the API spec
  action: {},         // the action invoked to obtain this response
  errors: [],         // errors encountered while creating this response
  resource_type: "",  // type type of resource [object, list]
  resource: {} || []  // the HAN resource(s)
}
```

### Resource

A resource is a container that wraps a custom value & includes hypermedia meta data.

```javascript
{
  name: "",        // the name of the resource
  value: {},       // the resource value
  transitions: [], // the navigational actions that can be performed
  custom: {}       // a container for custom meta data
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

This example illustrates the [create user](#state-machine-example) response outlined above.

```javascript
{
  han_version: "1.0",
  han_spec: "https://github.com/hopsoft/han",
  api_version: "2.1",
  api_spec: "http://api.example.com/spec",
  action: {
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
  errors: [],
  resource_type: "object",
  resource: {
    name: "User",
    value: { // the item is your custom object
      id: 1,
      name: "Han Solo",
      team: "Rebel Alliance"
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
    custom: {}
  }
}
```

### Basic ([list value](#list-value))

This example illustrates a "find users" call.

```javascript
{
  han: true,
  version: "v1",
  name: "UserList",
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
    verbs: [ "GET" ],
    headers: {
      Accept: "application/vnd.example.v1+json"
    },
    formats: [ "json" ],
    params: {
      team: "Rebel Alliance"
    }
  },
  transitions: [], // note: no transitions are available for the user list
  errors: []
}
```

### Embedded HAN Resources

HAN resource values may contain embedded HAN resources.
This technique yields a data structure which looks like this.

![HAN Resource](https://raw2.github.com/hopsoft/han/master/resource.png)

**Note:** *The [action](#resource) attriubte should be omitted from embedded HAN resources as it's not relevant.*

Consider the ["find users"](#basic-list-value) example above.
Let's update the `value.items` to be HAN resources.


```javascript
{
  han: true,
  version: "v1",
  name: "UserList",
  value: {
    type: "list",
    count: 3,
    items: [
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
        transitions: [
          {
            type: "soft",
            name: "Update User",
            href: "http://api.example.com/users/1",
            verbs: [ "PUT" ],
            headers: {
              Accept: "application/vnd.example.v1+json"
            },
            formats: [ "json" ],
            params: {
              name: "New Name",
              team: "New Team"
            }
          },
          {
            type: "hard",
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
        errors: []
      },
      {
        han: true,
        version: "v1",
        name: "User",
        value: {
          type: "object",
          item: { // the item is your custom object
            id: 2,
            name: "Luke Skywalker",
            team: "Rebel Alliance"
          }
        },
        transitions: [
          {
            type: "soft",
            name: "Update User",
            href: "http://api.example.com/users/2",
            verbs: [ "PUT" ],
            headers: {
              Accept: "application/vnd.example.v1+json"
            },
            formats: [ "json" ],
            params: {
              name: "New Name",
              team: "New Team"
            }
          },
          {
            type: "hard",
            name: "Delete User",
            href: "http://api.example.com/users/2",
            verbs: [ "DELETE" ],
            headers: {
              Accept: "application/vnd.example.v1+json"
            },
            formats: [ "json" ],
            params: {}
          }
        ],
        errors: []
      },
      {
        han: true,
        version: "v1",
        name: "User",
        value: {
          type: "object",
          item: { // the item is your custom object
            id: 3,
            name: "Princess Leia",
            team: "Rebel Alliance"
          }
        },
        transitions: [
          {
            type: "soft",
            name: "Update User",
            href: "http://api.example.com/users/3",
            verbs: [ "PUT" ],
            headers: {
              Accept: "application/vnd.example.v1+json"
            },
            formats: [ "json" ],
            params: {
              name: "New Name",
              team: "New Team"
            }
          },
          {
            type: "hard",
            name: "Delete User",
            href: "http://api.example.com/users/3",
            verbs: [ "DELETE" ],
            headers: {
              Accept: "application/vnd.example.v1+json"
            },
            formats: [ "json" ],
            params: {}
          }
        ],
        errors: []
      }
    ]
  },
  action: {
    type: "hard",
    name: "Find Users",
    href: "http://api.example.com/users",
    verbs: [ "GET" ],
    headers: {
      Accept: "application/vnd.example.v1+json"
    },
    formats: [ "json" ],
    params: {
      team: "Rebel Alliance"
    }
  },
  transitions: [],
  errors: []
}
```

While significantly more verbose,
each `item` now contains the hypermedia info required to navigate all possible transitions.
