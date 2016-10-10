## Specify the SQL Table and Its Unique Key

We'll add a couple of properties to the `GraphQLObjectType` definition on `User`. Our users data lives in the `accounts` table, so we'll set the `sqlTable` property to `'accounts'`.

We also need a unique identifier so it's unambiguous which objects are distinct entities and which were duplicated due to a join. Our `accounts` table has a primary key, the `'id'`, so we'll set that as the `uniqueKey` property. The `uniqueKey` does not need to have any constraints in the actual database. It's up to you to make sure no duplicate values exist in whichever column you indicate as being unique.

```javascript
const User = new GraphQLObjectType({
  name: 'User',
  sqlTable: 'accounts', // the SQL table for this object type is called "accounts"
  uniqueKey: 'id', // id is different for every row
  fields: () => ({ /*...*/ })
})
```

## Table Name Details

If your table is on a SQL schema that is not the default, e.g. `public`, you can specify it in `sqlTable` with a dot separator. Names that include characters other than **a-z**, **#**, and **$** (for example capital letters) must be wrapped in double quotes.

```javascript
const User = new GraphQLObjectType({
  name: 'User',
  sqlTable: 'public."Accounts"', // the SQL table is on the schema "public" called "Accounts"
  uniqueKey: 'id',
  fields: () => ({ /*...*/ })
})
```

## Composite Keys

If no single column in your table is unique, that's okay. Perhaps you have a *composite key*, where the combined value of multiple column is unique for each row.

| generation | first_name | last_name |
| ---------- | ---------- | --------- |
| 1          | erlich     | bachman   |
| 1          | andrew     | bachman   |
| 2          | erlich     | bachman   |
| 2          | matt       | bachman   |
| 1          | matt       | daemon    |

Just make `uniqueKey` an array of string instead of a string. Join Monster will use the SQL `||` operator to concatenate the values of those columns and identify the row based on the combination.

```javascript
const User = new GraphQLObjectType({
  name: 'User',
  sqlTable: 'accounts',
  uniqueKey: [ 'generation', 'first_name', 'last_name' ],
  fields: () => ({ /*...*/ })
})
```