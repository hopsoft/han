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

*HAN does not make assumptions about API consumers.*
*Neither does it presume to dictate UI concerns.*

## State Machine Example

Consider the following.

![User Resources & Transitions](https://raw2.github.com/hopsoft/han/master/user-example.png)

* The created state supports the `update` & `delete` transitions.
* The updated state supports the `delete` transition.
* The deleted state doesn't support any transitions.

**Note:** *The resource value (__User__) is the same across all states.*
*Only the possible transitions are different.*

## Spec

HAN respsonses are JSON objects.
The examples below are written in JavaScript for the brevity that inline comments allow.

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
* **soft** - indicates the action param values should be updated prior to invocation

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
  action: { // the action invoked to obtain this response
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
    value: { // the value is your custom object
      id: 1,
      name: "Han Solo",
      team: "Rebel Alliance"
    },
    transitions: [ // the actions that can be performed with the resource
      {
        type: "soft", // param values should be updated prior to invocation
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
        type: "hard", // must be invoked exactly as outlined
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
    custom: {} // a container for custom meta data about the resource
  }
}
```

### Basic ([list value](#list-value))

This example illustrates a "find users" call.

```javascript
```

### Embedded HAN Resources

HAN resource values may contain embedded HAN resources.
This technique yields a data structure which looks like this.

![HAN Resource](https://raw2.github.com/hopsoft/han/master/resource.png)

**Note:** *The [action](#resource) attriubte should be omitted from embedded HAN resources as it's not relevant.*

Consider the ["find users"](#basic-list-value) example above.
Let's update the `value.items` to be HAN resources.


```javascript
```

While significantly more verbose,
each `item` now contains the hypermedia info required to navigate all possible transitions.
