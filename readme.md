# HAN - Hypermedia API Navigation

> Traveling through hyperspace ain't like dusting crops, boy!
> Without precise calculations we could fly right through a star or bounce too
> close to a supernova, and that'd end your trip real quick, wouldn't it?
> --Han Solo

![HAN Solo](https://raw2.github.com/hopsoft/han/master/han.gif)

APIs are state machines.
Their responses include resource(s) in a distinct state.
These resource(s) can typically transition to other states.
HAN facilitates this navigation by communicating the possibilities.

At it's core, HAN defines the [response](#response), [resource](#resource), & resource [transitions](#action).
It does not make assumptions about API consumers.
Neither does it presume to dictate UI concerns.

## State Machine Example

Consider the following.

![User Resources & Transitions](https://raw2.github.com/hopsoft/han/master/user-example.png)

* The created state supports the `update` & `delete` transitions.
* The updated state supports the `delete` transition.
* The deleted state doesn't support any transitions.

**Note:** *The resource value (__User__) is the same across all states.*
*Only the possible transitions are different.*

## Spec

HAN respsonses are JSON objects,
but the examples below are written in JavaScript for the brevity that inline comments allow.

## Data Structures

### Response

A response is the root element which contains the entire result.
*Note that the response's resource value can be a single [resource](#resource) or a list of [resources](#resource).*

```javascript
{
  han_version: "",    // the version of the HAN spec being used
  han_spec: "",       // the href to the HAN spec
  api_version: "",    // the version of the API being used
  api_spec: "",       // the href to the API spec
  action: {},         // the action invoked to obtain this response
  errors: [],         // errors encountered while creating this response
  custom: {},         // a container for custom meta data about the response
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
  transitions: [], // the navigational actions that can be performed with the resource
  custom: {}       // a container for custom meta data about the resource
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

#### Action Types

* **hard** - indicates the action must be invoked exactly as outlined
* **soft** - indicates the action params should be modified prior to invocation

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


### Single Resource

This example illustrates the [**create user**](#state-machine-example) response outlined above.

```javascript
{
  han_version: "1.0",
  han_spec: "https://github.com/hopsoft/han/tree/v1.0",
  api_version: "2.1",
  api_spec: "http://api.example.com/docs/v2.1",
  action: { // the action invoked to obtain this response
    type: "hard",
    name: "Create User",
    href: "http://api.example.com/users",
    verbs: [ "POST" ],
    headers: {
      Accept: "application/vnd.example.v2.1+json"
    },
    formats: [ "json" ],
    params: {
      name: "Han Solo",
      team: "Rebel Alliance"
    }
  },
  errors: [], // errors encountered while creating this response
  custom: {}, // a container for custom meta data about the response
  resource_type: "object",
  resource: {
    name: "User",
    value: { // the value is your custom object
      id: 1,
      name: "Han Solo",
      team: "Rebel Alliance"
    },
    transitions: [ // the actions that can be performed with the resource
      {
        type: "soft", // params should be modified prior to invocation
        name: "Update User",
        href: "http://api.example.com/users/1",
        verbs: [ "PUT" ],
        headers: {
          Accept: "application/vnd.example.v2.1+json"
        },
        formats: [ "json" ],
        params: {
          name: "Han Solo", // value to update to [optional]
          team: "Rebel Alliance" // value to update to [optional]
        }
      },
      {
        type: "hard", // must be invoked exactly as outlined
        name: "Delete User",
        href: "http://api.example.com/users/1",
        verbs: [ "DELETE" ],
        headers: {
          Accept: "application/vnd.example.v2.1+json"
        },
        formats: [ "json" ],
        params: {}
      }
    ],
    custom: {} // a container for custom meta data about the resource
  }
}
```

### Multiple Resources

This example illustrates a **list users** call.
It includes a list of HAN resources which looks something like this.

![HAN Response](https://raw2.github.com/hopsoft/han/master/response.png)

```javascript
{
  han_version: "1.0",
  han_spec: "https://github.com/hopsoft/han/tree/v1.0",
  api_version: "2.1",
  api_spec: "http://api.example.com/docs/v2.1",
  action: { // the action invoked to obtain this response
    type: "hard",
    name: "List Users",
    href: "http://api.example.com/users",
    verbs: [ "GET" ],
    headers: {
      Accept: "application/vnd.example.v2.1+json"
    },
    formats: [ "json" ],
    params: {
      team: "Rebel Alliance"
    }
  },
  errors: [], // errors encountered while creating this response
  custom: {}, // a container for custom meta data about the response
  resource_type: "list",
  resource: [
    {
      name: "User",
      value: { // the value is your custom object
        id: 1,
        name: "Han Solo",
        team: "Rebel Alliance"
      },
      transitions: [ // the actions that can be performed with the resource
        {
          type: "soft", // params should be modified prior to invocation
          name: "Update User",
          href: "http://api.example.com/users/1",
          verbs: [ "PUT" ],
          headers: {
            Accept: "application/vnd.example.v2.1+json"
          },
          formats: [ "json" ],
          params: {
            name: "Han Solo", // value to update to [optional]
            team: "Rebel Alliance" // value to update to [optional]
          }
        },
        {
          type: "hard", // must be invoked exactly as outlined
          name: "Delete User",
          href: "http://api.example.com/users/1",
          verbs: [ "DELETE" ],
          headers: {
            Accept: "application/vnd.example.v2.1+json"
          },
          formats: [ "json" ],
          params: {}
        }
      ],
      custom: {} // a container for custom meta data about the resource
    },
    {
      name: "User",
      value: { // the value is your custom object
        id: 2,
        name: "Luke Skywalker",
        team: "Rebel Alliance"
      },
      transitions: [ // the actions that can be performed with the resource
        {
          type: "soft", // params should be modified prior to invocation
          name: "Update User",
          href: "http://api.example.com/users/2",
          verbs: [ "PUT" ],
          headers: {
            Accept: "application/vnd.example.v2.1+json"
          },
          formats: [ "json" ],
          params: {
            name: "Luke Skywalker", // value to update to [optional]
            team: "Rebel Alliance" // value to update to [optional]
          }
        },
        {
          type: "hard", // must be invoked exactly as outlined
          name: "Delete User",
          href: "http://api.example.com/users/2",
          verbs: [ "DELETE" ],
          headers: {
            Accept: "application/vnd.example.v2.1+json"
          },
          formats: [ "json" ],
          params: {}
        }
      ],
      custom: {} // a container for custom meta data about the resource
    },
    {
      name: "User",
      value: { // the value is your custom object
        id: 3,
        name: "Princess Leia",
        team: "Rebel Alliance"
      },
      transitions: [ // the actions that can be performed with the resource
        {
          type: "soft", // params should be modified prior to invocation
          name: "Update User",
          href: "http://api.example.com/users/3",
          verbs: [ "PUT" ],
          headers: {
            Accept: "application/vnd.example.v2.1+json"
          },
          formats: [ "json" ],
          params: {
            name: "Princess Leia", // value to update to [optional]
            team: "Rebel Alliance" // value to update to [optional]
          }
        },
        {
          type: "hard", // must be invoked exactly as outlined
          name: "Delete User",
          href: "http://api.example.com/users/3",
          verbs: [ "DELETE" ],
          headers: {
            Accept: "application/vnd.example.v2.1+json"
          },
          formats: [ "json" ],
          params: {}
        }
      ],
      custom: {} // a container for custom meta data about the resource
    },
  ]
}
```
