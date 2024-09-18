# MongoDB (Cheat Sheet)

> [!NOTE]  
> This is a cheat sheet on MongoDB (mostly notes-to-self). They are incomplete by default.

## `mongosh` (MongoDB Shell)

### Show all databases

```sh
show dbs
```

### Show all collections in the current database

```sh
show collections
```

### Query a collection

```sh
db.your_collection_name.find()
```

For example: 

```sh
testdb> db.ATTESTATIONS.find().pretty()
[
  {
    _id: ObjectId('66e83d1756c90708d7e60a66'),
    attestation_id: 'attest1-uuid',
    user_id: 'user1-uuid',
    attribute_id: 'attr1-uuid'
  },
  {
    _id: ObjectId('66e83d1756c90708d7e60a67'),
    attestation_id: 'attest2-uuid',
    user_id: 'user2-uuid',
    attribute_id: 'attr2-uuid'
  }
]
```

### Delete a collection

```sh
db.your_collection_name.drop()
```

For example: 

```sh
testdb> db.ATTESTATIONS.find().pretty()
[
  {
    _id: ObjectId('66e83d1756c90708d7e60a66'),
    attestation_id: 'attest1-uuid',
    user_id: 'user1-uuid',
    attribute_id: 'attr1-uuid'
  },
  {
    _id: ObjectId('66e83d1756c90708d7e60a67'),
    attestation_id: 'attest2-uuid',
    user_id: 'user2-uuid',
    attribute_id: 'attr2-uuid'
  }
]
testdb> db.ATTESTATIONS.drop()
true
testdb> db.ATTESTATIONS.find().pretty()
```

## FAQ

### Run MongoDB locally

Source: Claude
Other ref: https://www.mongodb.com/docs/manual/tutorial/install-mongodb-on-os-x/ 

This setup is great for local development and testing. When you're ready to move to production, you can easily switch the connection string to your MongoDB Atlas instance.

**Step 1: Install MongoDB Community Edition**

```sh
brew tap mongodb/brew
brew install mongodb-community 
```

**Step 2: Start MongoDB**

```sh
brew services start mongodb-community
```

**Step 3: Verify MongoDB is running**

```sh
$ mongosh
```

Docs: https://www.mongodb.com/docs/mongodb-shell/ 

If it connects successfully, you have a local MongoDB instance running.

**Step 4: Install Mongoose in your project**

```sh
npm install mongoose
```

**Step 5: Update your server.ts file to connect to the local MongoDB instance**

```ts
// Add this import at the top of the file
import mongoose from "mongoose";

// ... existing imports ...

// Add this before your server setup
mongoose.connect('mongodb://localhost:27017/your_database_name')
  .then(() => console.log('Connected to MongoDB'))
  .catch(err => console.error('Error connecting to MongoDB:', err));

// ... rest of your existing code ...
```

**Step 6: Remember to stop the MongoDB service when you're done:**  

```sh
brew services stop mongodb-community
```

### View local MongoDB instance

Source: Claude

You can check the state of your local MongoDB database. The MongoDB shell and MongoDB Compass are particularly useful for ad-hoc queries and data exploration during development.

**Option 1: MongoDB Shell (mongo)**

The simplest way is to use the MongoDB shell:
```sh
mongosh
```

Once you’re in the MongoDB shell:

Show all databases:

```sh
show dbs
```

Use a specific database:

```sh
use your_database_name
```

Show all collections in the current database:

```sh
show collections
```

Query a collection:

```sh
db.your_collection_name.find()
```

**Option 2: MongoDB Compass**

MongoDB Compass is a graphical user interface for MongoDB. It's very user-friendly and great for visualizing your data.

1. Download and install MongoDB Compass from the official website
2. Connect to `mongodb://localhost:27017`
3. Browse your databases and collections visually

**Option 3: Programmatically**

You can also check your database state programmatically in your Node.js application:

```ts
import mongoose from "mongoose";

// ... existing code ...

// Add this after your mongoose.connect() call
mongoose.connection.on('connected', async () => {
  console.log('Connected to MongoDB');
  
  // List all databases
  const adminDb = mongoose.connection.db.admin();
  const dbList = await adminDb.listDatabases();
  console.log('Databases:', dbList.databases.map(db => db.name));

  // List all collections in the current database
  const collections = await mongoose.connection.db.listCollections().toArray();
  console.log('Collections:', collections.map(c => c.name));

  // Example: Count documents in a collection
  const count = await YourModel.countDocuments();
  console.log('Document count in YourModel:', count);
});

// ... rest of your existing code ...
```