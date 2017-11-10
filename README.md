# M101JS
[A MongoDB course for Node.js Developers](https://university.mongodb.com/courses/M101JS/about)

- db
  - collection
    - documents
      - fields(keys)
      - values

## Week 1
### What is MongoDB?
1. scaling out vs. scaling up
2. Design data models

### Install MongoDB(Mac)
1. `mongo` is the MongoDB shell
2. `mongod` is the actual server
3. the mongodb server puts its data in `/data/db` by default
  - go back to the root `sudo bash`
  - `mkdir -p /data/db`
  - `chmod 777 /data`
  - `chmod 777 /data/db`
  - `ls -ld /data/db` to check the write&read permission
4. run the server
  - go back to the /bin, run `.mongod` if use the default file to store data
  - run `./mongod --dbpath /User/username/appname/data`
5. add the server to the usr/bin
  - `cp * /usr/local/bin`

### [JSON Data Format](http://www.json.org/)
1. key/value pairs
  - the value can be object, array, number, string
  - nesting

### [BSON](http://bsonspec.org/)
- A binary format
- `document`

### Intro to Creating and Reading Documents
#### CRUDs
 - Create
 - Read
 - Update
 - Delete
#### mongo shell
- run `./mongo`
- `help` to check the commands
- command `show dbs`
- command `use xxx`(switch to xxx database)
- `db.movies.insertOne()` to insert a record into the movie collection
- `db.movies.find().pretty()` to show all the objects in the collection
- pass query to `find()`

### Intro to [MongoDB Node.js Driver](https://mongodb.github.io/node-mongodb-native/)
- library for node app to connect to mongodb

### Homeworks
#### Homework 1
- `mongorestore`

## Week 2 CRUD
### Creating Documents
- `db.collectionName.insertOne({})`
- `db.collectionName.insertMany([document, document, ...], {ordered: false})`
- `db.collectionName.drop()`
- update commands ("upserts")

### The `_id` Field
- ObjectId: Date + mac address + pid + counter (12 byte hex string)

### Reading Documents - return documents match the query
- `db.collectionName.find({}).pretty()`
- `db.collectionName.find({"tomato.meter": 100}).count()` - use . for nested properties(quotes are required for dot notation)
- Equality matches on array
  - on the entire array (order of elements in the query array matters!)
    -  `db.collectionName.find({"": ["", ""]}).count()`
    -  `db.collectionName.find({"xx.0": ""}).count()` - the first element of an array
  - based on any element
  - based on special element
  - more complex matches using operators
  - `cursor` - `find()` return cursors
  - `projection`- reduce the fields returned - second argument(object) for the find()
    -  `db.collectionName.find({"xx.0": ""}, { title: 1, _d: 0}).count()`
    - `0` exclude fields, `1` include fields

### [Comparison Operators](https://docs.mongodb.com/manual/reference/operator/query/)
- `db.collectionName.find({ runtime: { $gt: 90, $lt: 150 }}).pretty()`
- query based on the value of properties

### Element Operators
- `db.collectionName.find({ "tamato.meter": { $exists: true }}).pretty()`
- `db.collectionName.find({"_id": { $type: "string"}})`
- query based on the value of properties

### Logical Operators
- `db.collectionName.find({ $or: [{"tomato.meter": { $gt: 95}}, { "metacritic": { $gt: 88 }}]})`
- `db.collectionName.find({ $and: [{"metacritic": { $ne: null}}, { "metacritic": { $exists: true} }]})` - useful when the query fields are the same

### Regex Operators
- `db.collectionName.find({ "awards.text": { $regex: /^Won\s.*/ }})`
- `^`: begin with
- `\s`: space
- `.*`: all afterwards
- query based on the value of properties

### Array Operators
- `db.collectionName.find({ genres: { $all: ["xxx", "xxx", "xxx"]}}).pretty()`
- `db.collectionName.find({ genres: { $size: 1}}).pretty()` - check the length of an array
- `db.collectionName.find({ boxOffice: { $elemMatch: { country: "UK", revenue: { $gt: 15 }}}})`

### [Updating Documents](https://docs.mongodb.com/manual/reference/operator/update/)
- `db.collectionName.updateOne({ title: " The Martian"}, { $set: { poster: "xxxxxxxx"}})` - the first parameter select the document and the second parameter passes the fields and value to be updated
- `db.collectionName.updateOne({title: "The Martian"}, { $inc: { "tomato.reviews": 3, "tomato.userReviews": 25 }})` - increment by 3/25
- `db.collectionName.updateOne({title: "The Martian"}, { $push: { xxx, xxx}})`
- `db.collectionName.updateMany()`
- `db.collectionName.find({}, {upsert: true})`
- `db.collectionName.replaceOne()`

## Week 3: The Node.js Driver
### find() and Cursors in the Node.js Driver
- `mongodimport`
- `cursor` to describe the query instead of calling the db immediately, get batches results instead of getting the whole result at once

### Projection in the Node.js Driver
- `cursor.project({ })`
-  `projection` project out the fields we need
- `0` exclude the fields
- `1` include the fields

### Query Operators in the Node.js Driver
- `command line args module`
- `find(query, projection)`

### `$regex` operator in the Node.js Driver
- `"options": i`

### Dot Notation in the Node.js Driver
- `{ }`
- Dot notation
- `[]`

### Dot Notation on embedded Documents in Arrays

### Sort, Skip, and Limit in the Node.js Driver
- paging
- sort by single field: `cursor.sort({})`
- sort by multiple fields: `cursor.sort([[], []])` - because the sequence matters
- `cursor.skip(#)`: skip the specified number of documents. Eg. `cursor.skip(4)`: skip the first 4 documents
- `cursor.limit(#)`
- the order of sort, skip and limit methods does not matter, because mongodb will always run sort() first, then skip() and limit() at the end.

### `insertOne()` and `insertMany()` in the Node.js Driver

### `deleteOne(filter, callback)` and `deleteMany()` in the Node.js Driver

## Week 4: Schema Design
### MongoDB Schema Design
- application driven schema
- support rich documents (array, annother documents, etc)
- pre join/embedded data
- no Mongo Joins
- no constraints
- atomic operations
- no declared schema
- What is the single most important factor in designing the application schema within MongodDB
  - Matching the data access patterns of the application

### Relational Normalization
- MongodDB frees the database of modification anomalies.
- Minize redesign when extending

### Modeling a Blog in documents
-  `Post` collection
- only need to access to one collection

### Living without constraints
- relational database
  - foreign key constraint
- mongodb
  - no guarantee for foreign key constraint
  - no use embedded data to make the data consistent

### Living without transactions
- use atomic operations (avoid making changes to the same documents at the same time)
- three approaches
  - restructure
  - implement locking in software operating system
  - tolerate a little bit inconsistency
- `Update`, `findAndModify`, `$addToSet(within an update)` and `$push within an update` are all the operations operate atomically within a single document.

### One to One Relations
- Examples:
  - (one)Employee: (one)Resume
  - (one)Building: (one)Floor Plan
  - (one)Patient: (one)Medical History
- When to keep documents in separate collections
  - to reduce the working set size of the application
  - when the combined size of documents would be larger than 16MB

### One to Many Relations
- Examples:
  - (one)city: (many)person

- true linking (store info in multiple collections)
  - people collection
    `{
      name: "xxx",
      city: "NYC"
    }`
  - city collection
   `{
     "_id": "NYC"
   }`

- One to Few
  - nested the other one in one single collection

### Many to Many Relations
- Examples:
  - Books: Authors (Few to Few)
  - Students: Teachers

- Few to Few
  - one collection with nested one

### MultiKey Indexes(??)
- for complicated queries to different collections
- `ensureIndex({})`
- `explain()`

### Benefits of Embedding
- Improved Read Performance
  - It takes long time to get the first byte from the db. However, the subsequent ones won't take long times.
- One Round trip to the DB

### Representing Trees in MongoDB
- list ancestors/children as the value of the "ancestors" property for example.

### When to denormalize
- 1: 1 (embedded)
- 1: Many (embedded: from the many to the one)
- Many: Many (Link through objectID)

## Questions
1. How to search in array of object in mongodb?
  - [Query an Array of embedded documents](https://docs.mongodb.com/manual/tutorial/query-array-of-documents/)
2. How to remove fields in a documents with specific queries?
  - ($unset)[https://docs.mongodb.com/manual/reference/operator/update/unset/]
3. How to check a field is null ?
  - [Query for null or missing fields](https://docs.mongodb.com/v3.2/tutorial/query-for-null-fields/)
