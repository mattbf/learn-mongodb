
# Concepts

![Working with MongoDB](https://github.com/mattbf/learn-mongodb/raw/master/images/workingwithmongo.png)

![CRUD in MongoDB](https://github.com/mattbf/learn-mongodb/raw/master/images/crud.png)

## Schema and Datatypes

![Schema in MongoDB](https://github.com/mattbf/learn-mongodb/raw/master/images/sqlvsnosql.png)
![Datatypes in MongoDB](https://github.com/mattbf/learn-mongodb/raw/master/images/datatypes.png)
![modelling chart](https://github.com/mattbf/learn-mongodb/raw/master/images/dataandmodellingchart.png)




### When to use Relations:

**One to One embedded:**  documents is good for a strong connection between the two
> db.patients.insertOne({name: "max", age: 29, diseaseSummary: {diseases: ["cold", "broken leg"]}})

**One to One reference:**  You could separate it if you had an application need to split it up (Eg persons and cars)

**One to Many embedded:**  questions and answers. List of answers embedded in questions

**One to Many references:**  cities with citizens. 1. Don’t want to go over 16mb limit for documents (Includes nested documents). So, each citizen has a reference to the city they live in 2. If you’re interested in the city meta data you don’t fetch all citizens

**Many to Many embedded:**  Ex. Many customers Many Products
SQL approach is create a orders table which takes a productID and customerID
Add an array with references in one or both tables (eg. order list in customers)
Or just directly embed information into an embedded list (Disadvantage is data duplication, also if you have to change the data you have to change it in the products database and in the order embedded documents, or maybe you don’t price doesn't change in past orders)

**Many to Many references:**  Books and Authors

![Working with MongoDB](https://github.com/mattbf/learn-mongodb/raw/master/images/relations-options.png)

# Using the Shell

db.collection.find() returns a cursor of the first X amount of data

IF you want all data write db.collection.find().toArray()

The $set operator replaces the value of a field with the specified value.

Projections: return filtered data and only the fields you want

```
db.collection.find({}, {name: 1, _id: 0})
-> returns
{ "name" : "Armin Glutch" }
{ "name" : "Klaus Arber" }
{ "name" : "Albert Twostone" }
```

### Embedded Documents

(Can have up to 100 levels of nested and overall document size has to be lower than 16mB)

Ex.
```
{
	"_id" : ObjectId("5ddb1ecbfbbb184b975c5d34"),
	"departureAirport" : "LHR",
	"arrivalAirport" : "TXL",
	"aircraft" : "Airbus A320",
	"distance" : 950,
	"intercontinental" : false,
	"status" : {
		"description" : "on-time",
		"lastUpdated" : "1 hour ago"
	}
}
```

Can be more specific:

```
db.passengers.findOne({name: “Albert”}).hobbies

db.passengers.findOne({name: "Albert Twostone"}).hobbies
```

OR drill into embedded documents like so:

```
db.flightData.find({status.description: “on-time”})
```



db.stats() returns some helpful information


```
{
	"db" : "companyData",
	"collections" : 2,
	"views" : 0,
	"objects" : 2,
	"avgObjSize" : 121,
	"dataSize" : 242,
	"storageSize" : 28672,
	"numExtents" : 0,
	"indexes" : 2,
	"indexSize" : 28672,
	"scaleFactor" : 1,
	"fsUsedSize" : 219360460800,
	"fsTotalSize" : 250790436864,
	"ok" : 1
}
```


![Working with MongoDB](https://github.com/mattbf/learn-mongodb/raw/master/images/joiningwithlookup.png)
Use lookup to use one call instead

![Working with MongoDB](https://github.com/mattbf/learn-mongodb/raw/master/images/example-blog.png)

Basic Collection Schema Validation

```
db.createCollection('posts', {
  validator: {
    $jsonSchema: {
      bsonType: 'object',
      required: ['title', 'text', 'creator', 'comments'],
      properties: {
        title: {
          bsonType: 'string',
          description: 'must be a string and is required'
        },
        text: {
          bsonType: 'string',
          description: 'must be a string and is required'
        },
        creator: {
          bsonType: 'objectId',
          description: 'must be an objectid and is required'
        },
        comments: {
          bsonType: 'array',
          description: 'must be an array and is required',
          items: {
            bsonType: 'object',
            required: ['text', 'author'],
            properties: {
              text: {
                bsonType: 'string',
                description: 'must be a string and is required'
              },
              author: {
                bsonType: 'objectId',
                description: 'must be an objectid and is required'
              }
            }
          }
        }
      }
    }
  }
});
```
