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

## 4.1
```javascript
```