# Mongo-DB
This repo will contain all the MongoDB fundamentals
# MongoDB with Mongoose  

## Table of Contents  
- [CRUD Operations](#crud-operations)  
- [Mongoose: Models, Schemas, Queries, Relationships](#mongoose-models-schemas-queries-relationships)  
- [Aggregation Pipeline](#aggregation-pipeline)  
- [Indexing for Performance Optimization](#indexing-for-performance-optimization)  
- [Transactions and Error Handling](#transactions-and-error-handling)  

---

## CRUD Operations  
Perform basic CRUD operations using MongoDB and Mongoose:  
- **Create**: Insert new documents into the database.  
    ```javascript  
    const newUser = new User({ name: 'John Doe', email: 'john@example.com' });  
    await newUser.save();  
    ```  
- **Read**: Retrieve documents from the database.  
    ```javascript  
    const users = await User.find({});  
    ```  
- **Update**: Modify existing documents.  
    ```javascript  
    await User.updateOne({ _id: userId }, { name: 'Jane Doe' });  
    ```  
- **Delete**: Remove documents.  
    ```javascript  
    await User.deleteOne({ _id: userId });  
    ```  

---

## Mongoose: Models, Schemas, Queries, Relationships  
Work with structured data in MongoDB using Mongoose:  
- **Schemas**: Define the structure of your documents.  
    ```javascript  
    const mongoose = require('mongoose');  

    const userSchema = new mongoose.Schema({  
        name: String,  
        email: String,  
        age: Number,  
    });  
    const User = mongoose.model('User', userSchema);  
    ```  
- **Queries**: Perform operations on the database.  
    ```javascript  
    const users = await User.find({ age: { $gte: 18 } });  
    ```  
- **Relationships**: Use `populate` to fetch related documents.  
    ```javascript  
    const postSchema = new mongoose.Schema({  
        title: String,  
        author: { type: mongoose.Schema.Types.ObjectId, ref: 'User' },  
    });  
    const Post = mongoose.model('Post', postSchema);  

    const post = await Post.findById(postId).populate('author');  
    console.log(post.author.name);  
    ```  

---

## Aggregation Pipeline  
Leverage MongoDB's powerful aggregation framework:  
- **$match**: Filter documents based on conditions.  
    ```javascript  
    const results = await User.aggregate([{ $match: { age: { $gte: 18 } } }]);  
    ```  
- **$group**: Group documents by a field and calculate metrics.  
    ```javascript  
    const results = await User.aggregate([  
        { $group: { _id: '$age', totalUsers: { $sum: 1 } } },  
    ]);  
    ```  
- **$project**: Shape the output documents.  
    ```javascript  
    const results = await User.aggregate([  
        { $project: { name: 1, email: 1, age: 1 } },  
    ]);  
    ```  

---

## Indexing for Performance Optimization  
Boost query performance with indexing:  
- **Create an Index**:  
    ```javascript  
    await User.createIndex({ email: 1 });  
    ```  
- **Analyze Query Performance**: Use `explain()` to check how a query is executed.  
    ```javascript  
    const queryPlan = await User.find({ email: 'john@example.com' }).explain();  
    console.log(queryPlan);  
    ```  
- **Unique Index**: Prevent duplicate values.  
    ```javascript  
    const userSchema = new mongoose.Schema({  
        email: { type: String, unique: true },  
    });  
    ```  

---

## Transactions and Error Handling  
Handle complex operations atomically and manage errors:  
- **Transactions**:  
    ```javascript  
    const session = await mongoose.startSession();  
    session.startTransaction();  

    try {  
        const user = new User({ name: 'John Doe', email: 'john@example.com' });  
        await user.save({ session });  

        const post = new Post({ title: 'New Post', author: user._id });  
        await post.save({ session });  

        await session.commitTransaction();  
    } catch (error) {  
        await session.abortTransaction();  
        console.error('Transaction failed:', error);  
    } finally {  
        session.endSession();  
    }  
    ```  
- **Error Handling**: Use `try...catch` blocks or Mongoose's error hooks.  
    ```javascript  
    userSchema.post('save', (error, doc, next) => {  
        if (error.name === 'MongoError' && error.code === 11000) {  
            next(new Error('Duplicate key error'));  
        } else {  
            next(error);  
        }  
    });  
    ```  
