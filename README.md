# GraphQL + BookshelfJS

[![peerDependency Status](https://david-dm.org/brysgo/graphql-bookshelf/peer-status.svg)](https://david-dm.org/brysgo/graphql-bookshelf#info=peerDependencies)

This is an early version of the `BookshelfType` I wrote to help me link up my Bookshelf models (built on top of Postgres) to my GraphQL schema.
### Install
`npm install --save graphql-bookshelf`

...

`var BookshelfType = require('graphql-bookshelf');`
### Example use...
Use `BookshelfType` inside of `GraphQLObjectType`...
```js
export default new GraphQLObjectType(BookshelfType({
  name: 'Classroom',
  description: 'Need I say more?',
  // ...and an object gets passed into the fields to help with your model.
  fields: (model) => ({
```
Simply wrap with `model.attr()`...
```js
    id: model.attr({
      type: new GraphQLNonNull(GraphQLInt),
      description: 'The id of the classroom.',
      // ...and you don't need to resolve table attributes.
    }),
```
Or wrap with model.belongsTo()...
```js
    subject: model.belongsTo({
      type: SubjectType, // And use the right association type...
      description: 'The subject of the classroom.',
      // And you get one-to-one relationships for free
    }),
```
Use model.hasMany()...
```js
    students: model.hasMany({
      type: new GraphQLList(StudentType), // And make sure you use `GraphQLList`
      description: 'Students in the classroom.',
      // Now you have associated collections for free
    }),
```
Need to do more on your associated collection?
```js
    homeworks: model.hasMany({
      type: new GraphQLList(HomeworkType),
      description: 'Homework submitted to the classroom (latest to oldest).',
      // Define a resolve function...
      resolve: (qb) => {
        // And get a sweet KnexJS query builder
        qb.orderBy('created_at', 'DESC');
      }
    }),
```
Or just leave it alone...
```js
    size: {
      type: GraphQLInt,
      description: 'How many students there are in the class.',
      resolve: (model) => {
        // And do it the old fashioned way
        return model.studentCount();
      },
    }
  }),
}));
```

[See this example in action via the tests...](https://github.com/brysgo/graphql-bookshelf/blob/master/schema/ClassroomType.js)

### Contributing
 1. Install sqlite3, clone repo, npm install
 2. Create database in project root by running `sqlite3 graphql_bookshelf.sqlite`
 3. Run migrations, `knex migrate:latest`
 4. Run the tests with `npm test`
 5. When they pass, submit a PR

