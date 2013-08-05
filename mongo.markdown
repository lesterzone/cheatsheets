All credits to @memodev07

### Insert Data: 
~~~js
db.people.insert( { name : "Guillermo", last_name: "Arias" } );
~~~

#### Comparisons 

*    $gt: greater than

*    $lt: less than

*    $gte: greater than equals

*    $lte: less than equals

*    $ne: not equals
~~~js
db.people.find( { name : { $gt : "M" } } );
~~~

### Conditions

*   $exists: checks if a property exists in a collection
~~~js
db.people.find( { profession : { $exists : true }  }  );
~~~

*   $type: checks the type of the properties in a collection
~~~js
db.people.find( { name : { $type : 2 }  }  ); (2 means that the property should be string)
~~~

*   Type numbers: http://docs.mongodb.org/manual/reference/glossary/#term-bson-types

*   $regex: condition based in a regular expression
~~~js
db.people.find( { name : { $regex : "e$" }  }  ); 
~~~

*   $or / $and: 
~~~js
db.people.find( { $or :  [ { name : { $regex : "e$" } }, { age: {$exists : true } } ] } 
~~~

*   $all: returns the records that match all the elements in the array
~~~js
db.people.find( { favorites : { $all : ["pretzels", "beer"] } } );
~~~

*   $in: returns the records that match at least one element in the array
~~~js
db.people.find( { names : { $in : ["pedro", "juan"] } } );
~~~

*   dot notation: work for finding elements inside an embedded document
~~~js
document: { email: { work: "guillermo.arias@dotcreek.com", personal: "g@example.com"}}
db.people.find( { "email.work" : "guillermo.arias@dotcreek.com" } );
~~~

### Cursors

How to assign: 
~~~js
cur = db.people.find(); null; // null avoids to print the result
~~~

Check if theres's a next element: 
~~~js
cur.hasNext(); //return boolean
~~~

Move to next element: 
~~~js
cur.next()
~~~

Limit: cur.limit(5); null;
~~~js
while (cur.hasNext()) printjson(cur.next()); (will print just 5 elements)
~~~

Sort: 
~~~js
cur.sort( { name : -1 } ); null; (sort DESC) cur.sort( { name: -1 } ).limit(5); null;
~~~

Skip: skips the first element specified
~~~js
curt.sort( { name: -1 } ).limit(5).skip(2); null; 
~~~    

Example:
~~~js
db.scores.find( { type:"exam" } ).sort( { score: -1 } ).skip( 50 ).limit( 20 )
~~~

The functions sort, skip and limit are executed in that order respectively no matter how you call them.

### Count:
~~~js
db.scores.count( {score: { $gt: 50 } )
~~~

### Updating documents:

~~~js
db.people.update( { name: "Guillermo"}, { name: "Guiller", last_name: "Arias"} ); // first argument means the condition to find the element and the second argument means the new values
~~~
    
*   $set: creates the attribute if doesn't exist when is updating
~~~js
db.people.update( { name: "Guillermo" }, { $set : { age: 23 } } )
~~~
    
*   $inc: increases a value based on the set data
~~~js
db.people.update( { name: "Guillermo" }, { $inc : { age: 1 } } ) // if age is 23 after update is 24
~~~
    
*   $unset: removes an attribute
~~~js
db.people.update( {name: "Guillermo" }, { $unset: { profession: 1 } } )
~~~
    
*   upsert: if the record doesn't exist create it
~~~js
db.people.update( { name: "Guillermo" }, { $set : { age: 23 } }, { upsert: true } )
~~~
    
*   multi update: 
~~~js
db.people.update( { }, { $set : { age: 23 } }, { multi: true } )
~~~
    
*   find_and_modify: http://docs.mongodb.org/manual/reference/method/db.collection.findAndModify/
~~~js
db.people.find_and_modify(query={ 'type': name }, update={ $inc : { 'value': 1 } }, upsert:True, new = True)
~~~

### Manipulating arrays:
~~~js
{ "_id" : 0, "a" : [ 1, 2, 3, 4, 5 ] }
~~~
    
*   $set: update array: 
~~~js
db.arrays.update( { _id: 0 }, { $set: { "a.2": 5 } } ) // changes '3' to '5'
~~~ 

*   $push: add to array: 
~~~js
db.arrays.update( { _id: 0 }, { $push: { a: 6 } } ) 
~~~    

*   $pop: remove from array: 
~~~js
db.arrays.update( { _id: 0 }, { $pop: { a: -1 } } )  // removes element at index 0 (1 will remove the element at the end of the array)
~~~
    
*   $pushAll: add several values to array: 
~~~js
db.arrays.update( { _id: 0 }, { $pushAll: { a: [ 7, 8, 9 ] } } )
~~~    

*   $pull: remove from array no matter the position: 
~~~js
db.arrays.update( { _id: 0 }, { $pull: { a: 5 } } ) 
~~~    

*   $pullAll: remove several items from array: 
~~~js
db.arrays.update( { _id: 0 }, { $pullAll: { a: [ 5, 6, 2 ] } } ) 
~~~    

*   $addToSet: adds to array only if doesn't exist (acts like push)

### Removing data:

Remove: removes the specified collection, if is empty removes all the documents one by one
~~~js
db.people.remove( { name: "Guillermo" } ) 
~~~   

Drop: removes all the documents in one step: 
~~~js
db.people.drop() 
~~~

### Getting errors:

getLastError: returns the error of the last operation
~~~js
db.runCommand( { getLastError: 1 } );
~~~~

### Indexes:
Example:
Students document
~~~js
{ "_id": 1, "name": "Student 1", "teachers": [1, 3] }
{ "_id": 2, "name": "Student 2", "teachers": [1, 2] }
{ "_id": 3, "name": "Student 3", "teachers": [2, 3] }
~~~

Teachers document
~~~js
{ "_id": 1, "name": "Teacher 1" }
{ "_id": 2, "name": "Teacher 2" }
{ "_id": 2, "name": "Teacher 3" }
~~~

Add an index:
~~~js
db.students.ensureIndex( { 'teachers': 1 } )
~~~

Find the students of teachers where teacher id is in an array:
~~~js
db.students.find( { 'teachers' : { '$all': [1, 3] } )
~~~

*   Inspect Query:   
~~~js 
db.students.find( { 'teachers' : { '$all': [1, 3] } ).explain()
~~~

*   Find all indexes in db:
~~~js 
db.system.indexes.find()
~~~

*   Find indexes in a particular collection:       
~~~js 
db.students.getIndexes()
~~~

*   Drop index:        
~~~js 
db.students.dropIndex({ 'student_id': 1 })
~~~

*   Add a unique index:      
~~~js 
db.students.ensureIndex({ 'identification': 1 }, { unique: true })
~~~

*   Remove duplicates for adding a unique index. Heads up! This will remove all the duplicates in the collection so use it 
carefully.
        
~~~js 
db.students.ensureIndex({'identification': 1}, { unique: true, dropDups: true })
~~~

*   Add a unique index even if some records in the collection don't have the key:  
~~~js 
db.students.ensureIndex({ 'identification': 1}, { unique: true, sparse: true })
~~~

*   Creating an index using the 'background' option set in true will be slower but it is recommended if you are doing it 
in a production server so the mongo server is not blocked.

*   Get stats: 
~~~js 
db.students.stats()
~~~

*   Get the size of an index: 
~~~js 
db.students.totalIndexSize();
~~~

*   Tell mongo which index to use:      
~~~js 
db.foo.find({ a:100, b:100, c:100 }).hint({ b: 1 }) // Use the index in b
db.foo.find({ a:100, b:100, c:100 }).hint({ $natural: 1 }) // Natural means that use no index
~~~

### Geospatial Indexes:
Add index: 
~~~js
db.routes.ensureIndex({ "location": "2d", type: 1 })
~~~

Search: 
~~~js
db.routes.find({ "location": { $near: [x, y] } })
~~~

### Geospacial Spherical:
Add index: 
~~~js
db.routes.ensureIndex({ "location": "2d", type: 1 })
~~~

The location should be in longitude and latitude
    
~~~js
db.runCommand({geonear: 'routes', near: [longitude, latitude], spherical: true, maxDistance: 1})
geonear: collection to query
near: geolocation values
maxDistance: distance in radius (1km: 1/111.12)
~~~

### Querying the profile collection:
Profiling levels:
    0: the profiler is off, doesn't collect any data
    1: collects profiling data for slow operations only. By default slow operations are those slower than 100 milliseconds.
    2: collects profiling data for all database operations
Find queries to the test.foo collection sorted by timestamp
        
~~~js
db.system.profile.find({ ns: /test.foo/ }).sort({ ts: 1 }).pretty()
~~~

Find slow queries sorted by timestamp:       
~~~js
db.system.profile.find({ millis: { $gt: 1 } }).sort({ ts: 1 }).pretty()
~~~

Get the profiling level: 
~~~js
db.getProfilingLevel()
~~~

Get the profiling status: 
~~~js
db.getProfilingStatus()
~~~

Set profiling level:
Set profiling to level 1 and log operations longer than 1 second
        
~~~js
db.setProfilingLevel(1, 1000)
~~~

### Stats: 
mongotop: similar to the 'top' command in unix. $mongotop 3 (run it each 3 seconds)
mongostat: similar to iostat in unix.

### Aggregation:

SQL count(*) and group by example:        
~~~js
db.products.aggregate([ { $group: { _id: "$manufacturer", num_products: { $sum: 1 } } } ])
~~~

Compound grouping:    
~~~js
db.products.aggregate([ { $group: { _id: { 'manufacturer': '$manufacturer', 'category': '$category' }, num_products: { $sum: 1 } } } ])
~~~

~~~js
db.products.aggregate([ { $group: { _id: { 'manufacturer': '$manufacturer' }, num_products: { $sum: '$price' } } } ])
~~~

Aggregation functions:
*    $sum
*    $avg
*    $addToSet
*    $push
*    $max - $min

Double $group stages:      
~~~js
db.grades.aggregate([
    { '$group': { _id: { class_id: "$class_id", student_id: "$student_id" }, 'average': { "$avg": "$score" } } },
    { '$group': { _id: "$_id.class_id", 'average': { "$avg": "$average" } } }
])
~~~
