## Implement Functions: Count – Sort – Limit – Skip – Aggregate using MongoDB 
```
use 4VP22CD019
```
```
db.createCollection("studentdb");
```
```
db.student.insertOne({ "_id": 1, "name": "ababyj", "age": 34, "score": 69, "course": "CSY" });
```
```
db.student.insertMany([
{ "_id": 2, "name": "kaushik", "age": 32, "score": 78, "course": "CSY" },
{ "_id": 3, "name": "babu", "age": 39, "score": 98, "course": "CSY" },
{ "_id": 4, "name": "shinu", "age": 36, "score": 72, "course": "CSY" },
{ "_id": 5, "name": "manoj", "age": 34, "score": 68, "course": "CD" },
{ "_id": 6, "name": "darshan", "age": 32, "score": 70, "course": "CD" },
{ "_id": 7, "name": "karthik", "age": 38, "score": 78, "course": "CD" },
{ "_id": 8, "name": "anup", "age": 33, "score": 64, "course": "CSY" },
{ "_id": 9, "name": "anu", "age": 34, "score": 68, "course": "CD" },
{ "_id": 10, "name": "ramesh", "age": 35, "score": 82, "course": "CD" },
{ "_id": 11, "name": "madhu", "age": 36, "score": 88, "course": "CSY" },
{ "_id": 12, "name": "rani", "age": 34, "score": 98, "course": "CD" }
]);
```
```
db.student.find();
```
```
db.student.find({ course: "cd" });
```
```
db.student.find({ age: { $gt: 32 } }).count();
```
```
db.student.find({ score: { $gt: 75, $lt: 89 } }).count();
```
```
db.student.find(
{ course: "cd" },
{ name: 1, score: 1, _id: 0 }
);
```
```
db.student.find().pretty().limit(3);
```
```
db.student.find().skip(10);
```
```
db.student.aggregate([
  { $match: { course: "cd" } },
  { $sort: { score: -1 } },
  { $limit: 3 }
]);
```
```
db.student.aggregate([
{ $group: { _id: "$course", avgscore: { $avg: "$score" } } }
]);
```
```
db.student.aggregate([
{
$group: {
_id: "$course",
avgscore: { $avg: "$score" },
maxscore: { $max: "$score" },
minscore: { $min: "$score" }
}
}
]);
```
