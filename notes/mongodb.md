# Intro

NoSQL database that stores data in flexible, JSON-like documents

| Method | Description |
| --- | --- |
| `db.createCollection("users")` | Create a collection |
| `db.collection.insertOne(doc)` | Inserts a single document into the collection |
| `db.collection.insertMany([doc1, doc2])` | Inserts multiple documents. |
| `db.collection.find()` | Returns all documents in the collection. |
| `db.collection.find({ key: value })` | Finds documents matching the query filter |
| `db.collection.findOne({ key: value })` | Returns the first matching document |
| `db.collection.updateOne(filter, { $set: update })` | Updates the first document that matches the filter. |
| `db.collection.updateMany(filter, { $set: update })` | Updates all matching documents |
| `db.collection.replaceOne(filter, replacement)` | Replaces one document entirely |
| `db.collection.deleteOne(filter)` | Deletes the first matching document |
| `db.collection.deleteMany(filter)` | Deletes all matching documents |
| `db.collection.countDocuments(filter)` | Counts documents matching the filter |
| `db.collection.find().sort({ key: 1 })` | Sorts in ascending order (1 = asc, -1 = desc) |
| `db.collection.find().skip(offset).limit(n)` | Limits and offset |
| `db.collection.aggregate([ { $match: {} }, { $group: {} } ])` | Performs aggregation (like GROUP BY in SQL) |
| `db.collection.distinct("key", query)` | Gets unique values of a field |

GridFS

- storing and retrieving large files in MongoDB by splitting them into chunks and storing them across two collections:
- fs.files – metadata for each file (filename, length, uploadDate, etc.)
- fs.chunks – actual binary data chunks of the file
- Disadvantages
  - Slower due to database I/O, especially for large files
  - No backup
  - No versioning
