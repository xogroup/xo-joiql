# 2.0.0 API Reference
  
## Joi2GQL

### `type(schema, [config])` - aliases: `transmuteType`
Accepts a joi schema and options object defining the name, args and resolver.
- `schema` - Joi schema. *Will **throw** if schema is not an object type.*
- `config` - Optional Javascript object detailing:
  - `name` - Name given to GraphQL data type. *Name of Anon will be provided if one is not given*
  - `args` - Arguments provided as parameters to GraphQL resolver function.
  - `resolve` - Function that is used as resolver for this data type.

> If data type will be used as a field in the [Root Query](http://graphql.org/learn/execution/#root-fields-resolvers) then it's recommended that you provide a config.

```js
const schema = Joi.object().keys({
  title: Joi.string(),
  director: Joi.string(),
  actors: Joi.array(Joi.string())
});

const options = {
  name: 'Film',
  args: { id: Joi.string().guid() },
  resolve: (root, args) => {
    //...return object
  }
};

const Film = Joi2GQL.type(schema, options);
```

### `schema(schema)` - aliases: `transmuteSchema`
Helper method that will create a Root GraphQL schema. Supports: `Query`, `Mutation`, `Subscription`.
- `schema` - Plain javascript object. Checks for the prescence of either a `query`, `mutation`, or `subscription` key at the parent level. The value needs to be a GraphQL data type.

```js
const Film = Joi2GQL.type(schema, config);

const schema = {
  query: {
    film: Film
  }
};

const GraphQLSchema = Joi2GQL.schema(schema);
```

## Mappings

Reference of Joi to GraphQL type mappings.

| Joi   |  GraphQL   |
|-------|------------|
| `string()` |  `GraphQLString` |
| `number()` | `GraphQLFloat` |
| `number().integer()` | `GraphQLInt` |
| `array()` | `GraphQLList` |
| `object()` | `GraphQLObjectType` |
| `string().guid()` | `GraphQLID` |
| `boolean()` | `GraphQLBoolean` |
| `any().required()` | `GraphQLNonNull` |
| `any().only()` | `GraphQLEnumType` |

### GraphQL Type Examples ( non-scalar ):
- `GraphQLList`
  ```js
  const Joi    = require('joi');
  const Joi2GQL = require('joi2gql');
  
  const listSchema = Joi.object().keys({
    a: Joi.array().items(Joi.string())
  });
  
  const List = Joi2GQL.type( listSchema );
  ```
- `GraphQLEnumType`
  ```js
  const Joi    = require('joi');
  const Joi2GQL = require('joi2gql');
  
  const scalars = [
    {
      value      : 'a',
      derivedFrom: 0
    },
    {
      value      : 'b',
      derivedFrom: 1
    }
  ];
  
  const enumSchema = Joi.object().keys({
    a: Joi.any().only(scalars).meta({ name: 'A' })
  });
  
  const Enum = Joi2GQL.type( enumSchema );
  ```
