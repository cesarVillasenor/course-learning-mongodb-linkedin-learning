mongo
use cooker

# 2.0 Documents and collections

## 2.1 Create a document
```javascript
doc = {"title": "Tacos", "desc": "Yummie tacos", "cook_time": 20};
db.tacos.insertOne(doc)
db.tacos.find()
db.tacos.find( {"title": "Tacos"}, {"title": 1}).pretty()
```

## 2.2 using .find() to query documents 
```javascript
db.recipes.find({"title": { $regex: /taco/i}},  {"titles": 1});
```

## 2.3 What can we store in a document?
```javascript
db.test.insertOne({ "title": "test doc" })
db.test.find()
```

## 2.4 Collections
```javascript
db.recipes.find({}, {"title": 1});
show dbs;
show collections;
db.getName();
db.cool_newStuff.insertOne({})
```


## 2.5 Challenge: Create a recipe document
```javascript
doc = {"title": "Tacos 2", "desc": "My desc", "rating": 4.2, "tags": ["traditional", "Mexican"], "comments": [{"body": "I love these! They are so fluffy!", "date": {"$date": "2000-01-03T18:42:30Z"},"name": "Grace Hopper","user_id": 2}],	"directions": ["Put seasoning on chicken breasts", "Grill until done", "Chop chicken into pieces", "Put in tortillas"], 	"ingredients": [{"name": "chicken breast","quantity": {"amount": 1,"unit": "lbs"}},{"name": "taco seasoning","quantity":{"amount": 2,"unit": "oz"}},{"name": "small flour tortillas","quantity": {"amount": 12,"unit": "oz"}}]} 
db.recipes.insertOne(doc)
```

***
# 3.0 Querying

## 3.1 Sort, limit and skip

```javascript
db.recipes.find().count();
db.recipes.find({}, {"title": 1}).count();
db.recipes.find({}, {"title": 1}).sort({"title": 1});
db.recipes.find({}, {"title": 1}).sort({"title": -1});
db.recipes.find({}, {"title": 1}).sort({"title": 1}).limit(2);
db.recipes.find({}, {"title": 1}).sort({"title": 1}).skip(1);
db.recipes.find({}, {"title": 1}).sort({"title": 1}).skip(2).limit(1);
```

## 3.2 Working with operator and arrays
- $lt  less than
- $gt     greater than
- $lte 
```javascript
db.recipes.find({"cook_time": { $l"te :30}},{"title": 1});
db.recipes.find({"cook_time": { $lte :30}, "prep_time": { $lte : 10}},{"title": 1});
db.recipes.find({"_id": ObjectId("5e878f5220a4f574c0aa56db")}, {"title": 1, "prep_time": 1 ,"cook_time": 1})
db.recipes.find({ $or :[{"cook_time": { $lte :30}}, {"prep_time": { $lte : 10}}]},{"title": 1});
db.recipes.find({ "tags": "easy"}, {"title":1 ,"tags": 1});
// not proper way
db.recipes.find({ "tags": ["easy", "mexican"]}, {"title":1 ,"tags": 1}); 
// proper way
db.recipes.find({ "tags": { $all: ["easy", "quick"]}}, {"title":1 ,"tags": 1}); 
db.recipes.find({ "tags": { $in: ["easy", "quick"]}}, {"title":1 ,"tags": 1}); 

```

## 3.2 Updating documents
### Operators:
- $set
- $unset
- $inc

```javascript
db.examples.find({}, {"title": 1});
db.examples.updateOne({"title": "Pizza"}, { $set: {"title": "Thin crust pizza"}});
db.examples.find({}, {"title": 1});
db.examples.find({"title": "Thin crust pizza"});
db.examples.updateOne({"title": "Thin crust pizza"}, { $set: {"vegan": false}});
db.examples.find({"title": "Thin crust pizza"});
// $unset
db.examples.updateOne({"title": "Thin crust pizza"}, { $unset: {"vegan": false}});
// $inc 
db.examples.updateOne({"title": "Tacos"}, { $inc: {"likes_count": 1}});
db.examples.find({"title": "Tacos"});
db.examples.updateOne({"title": "Tacos"}, { $inc: {"likes_count": -1}});
db.examples.find({"title": "Tacos"});
```

## 3.3 Updating arrays

### Operators:
- $push
- $pull 
```javascript
// push
db.examples.updateOne({"title": "Tacos"}, { $push: { "likes": 60}});
db.examples.find({"title": "Tacos"}).pretty();
// pull
db.examples.updateOne({"title": "Tacos"}, { $pull: { "likes": 60}});
db.examples.find({"title": "Tacos"}).pretty();
```

## 3.4 Deleting documents

```javascript
db.examples.find({}, {"title": 1});
db.examples.deleteOne({ "_id": ObjectId("5ee69e393260aab97ea0d58e")});
db.examples.find({}, {"title": 1});
```

## 3.5 Challenge: write a query
```javascript
// 1 sort reversed top 4 of rating_avg
db.recipes.find({}, {"title": 1}).sort({"ratin_avg": -1}).limit(4); 
// 2 filter mexican tags and sort reversed top 4 of rating_avg 
db.recipes.find({ "tags": "mexican"}, {"title": 1}).sort({"ratin_avg": -1}).limit(4);
// 3
db.recipes.find({}, {"title": 1, "likes": 1 }).sort({ "title": 1});
db.recipes.find({ "likes": { $all: [1] }}, {"title": 1, "likes": 1 }).sort({ "title": 1});

```

## 3.6 Solution: Write a query
```javascript
db.recipes.find({ "likes": { $all: [35] }}, {"title": 1, "likes": 1 }).sort({ "title": 1});
db.recipes.find({ "likes": { $all: [1, 35] }}, {"title": 1, "likes": 1 }).sort({ "title": 1});
db.recipes.find({ "likes": { $in: [1, 35] }}, {"title": 1, "likes": 1 }).sort({ "title": 1});
```


## 3.7 The new MongoDB Shell

Use command `mongosh`.

```javascript
db.recipes.find({"title": "Tacos"})
const results  = db.recipes.find({}, {title: 1, prep_time: 1, _id: 0 }).toArray();
console.table(results)
db.recipes.find({}, {title: 1, prep_time: 1, _id: 0 }).toArray();
```

***
# 4.0 Data and schema modeling

## 4.1 How we need to think differently
Data should be together

## 4.2 Basic indexes

```javascript
db.recipes.find({"cook_time": 10}, {"title": 1});
db.recipes.find({"cook_time": 10}, {"title": 1}).explain("executionStats");
db.recipes.find({"title": "Tacos"}, {"title": 1}).explain("executionStats");
db.recipes.getIndexes()
db.recipes.createIndex({"cook_time": -1})
// cook_time_-1
db.recipes.getIndexes()
db.recipes.find({"cook_time": 10}, {"title": 1}).explain("executionStats");
db.recipes.dropIndex("cook_time_-1");
```

## 4.3 Using different collection types
```javascript
db.createCollection("error_log", {capped: true, size: 10000, max: 10000});
```

## 4.4 Challenge: Avoiding joins
```javascript
// Create json data structure for recipes avoiding joins
```

## 4.5 Solution: Avoiding joins
```javascript
{
    "user_id": 1,
    "first_name": "Yoshi",
    "last_name": "yoshi@zxy.com"
    "backing_level": 2,
    "shipping_address": {
       "street_name": "123 Mushroom Ln",
       "city": "Gondor",
       "state": "MS",
       "zip": "12345", 
    },
    "rewards":{
        "book",
        "beach towel",
    },
}
```
***
# 5.0 Coding with MongoDB
Examples on coding

## 5.1 Python 
Install pymongo from requirements.txt

examples on lessons 5.1

## 5.2 Node.js
npm install
examples on lessons 5.2

## 5.3 PHP
Install PECL
sexamples on lessons 5.3

## 5.4 Golang
Install Golang MongoDB driver
examples on lessons 5.4

## 5.5 How to use GridFS to store files
examples on lessons 5.5
```
mongofiles list --db=files --quiet
mongofiles get pike-place.jpg  --db=files 
mongofiles put apple-pie.jpg --db=files 
mongofiles put ozmaofoz.pdz --db=files 
mongofiles delete ozmaofoz.pdz --db=files 
```

***
# 6.0 Server Administration

## 6.1 MongoDB config file
Config examples files

```
systemLog:
  destination: file
  path: /usr/local/var/log/mongodb/mongo.log
  logAppend: true
storage:
  dbPath: /usr/local/var/mongodb
net:
  bindIp: 127.0.0.1
  port: 27017
```

```
systemLog:
  destination: file
  path: /var/log/mongodb/mongo.log
  logAppend: true
storage:
  dbPath: /store/db
  directoryPerDB: true
net:
  bindIp: 127.0.0.1
  port: 27017
  maxIncomingConnections: 65536
processManagement:
  fork: true
security:
   authorization: enabled
   javascriptEnabled: true
replication:
   replSetName: cookerSet
```

Select specific config
```
mongod --config=mongod.cfg
```
## 6.2 Replication 

On 3 docker container
#### Commands to setup replicas

`mongod --replSet cookingSet --dbpath=/store/data/rs1 --port 27017 --smallfiles --oplogSize 200`

`mongod --replSet cookingSet --dbpath=/store/data/rs2 --port 27018 --smallfiles --oplogSize 200`

`mongod --replSet cookingSet --dbpath=/store/data/rs3 --port 27019 --smallfiles --oplogSize 200`

#### Config Replica Set

```
config = {
  _id: "cookingSet",
  "members": [
    {_id: 0, host: "localhost:27017"},
    {_id: 1, host: "localhost:27018"},
    {_id: 2, host: "localhost:27019"},
  ]
}
```

#### Initiate Replica Set

`rs.initiate(config)`

#### Status of Replica Set

`rs.status()`

#### Connect Replica Set

`mongo "mongodb://localhost:27017,localhost:27018,localhost:27019/?replicaSet=cookingSet"`


## 6.3 Sharding



## 6.4 Authentication and authorization

mongo
```
use admin
```

```javascript
db.createUser(
  {
    user: "taco",
    pwd: passwordPrompt(),
    roles: [ { role: "userAdminAnyDatabase", db: "admin" }, "readWriteAnyDatabase" ]
  }
);
db.adminCommand({ shutdown: 1 });

```
add to mongod.conf
```
security:
    authorization: enabled
```

```
mongo --authenticationDatabase "admin" -u "admin" -p
use admin
```

```javascript
db.auth("taco", passwordPrompt());
```
## 6.5 Backups

```javascript
db.fsyncLock()
db.fsyncUnlsock()
```
### Backup and restore
- mongodump
- mongorestore