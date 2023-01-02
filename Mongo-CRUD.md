In a NoSQL database, a record is usually stored as a JSON document

Why should you use a NoSQL database?
- Flexibility
- Scalability
- High-performance
- Highly functional


Types of NoSQL Databases
- Key-value
- Document
- Graph
- In-memory
- Search
- Wide-column stores


Data comes in all shapes and sizes — structured, semi-structured, and polymorphic
Defining the schema in advance became nearly impossible. 
NoSQL databases allow developers to store huge amounts of unstructured data, giving them a lot of flexibility.


Documents typically store information about one object as well as any information related to that object. 
Related documents are grouped together in collections. 
Related collections are grouped together and stored in a database.



BSON has a variety of data types including 
- Double, String, Object, Array, Binary Data, ObjectId, Boolean, Date, Null, Regular Expression, JavaScript, JavaScript (with scope), 32-bit Integer, Timestamp, 64-bit Integer, Decimal128, Min Key, and Max Key.
- With all of these types available for you to use, you have the power to model your data as it exists in the real world
- Every document is required to have a field named _id
- The value of _id must be unique for each document in a collection, is immutable, and can be of any type other than an array.


MongoDB CRUD Operations

CRUD operations create, read, update, and delete documents

Create Operations - create or insert operations add new documents to a collection
Read Operations - retrieve documents from a collection; i.e. query a collection for documents
Update Operations - modify existing documents in a collection
Delete Operations -  remove documents from a collection
Bulk Write - ability to perform write operations in bulk



Insert One
db.inventory.insertOne(
   { item: "canvas", qty: 100, tags: ["cotton"], size: { h: 28, w: 35.5, uom: "cm" } }
)

Find 
db.inventory.find( { item: "canvas" } )


Insert Many
db.inventory.insertMany([
   { item: "journal", qty: 25, tags: ["blank", "red"], size: { h: 14, w: 21, uom: "cm" } },
   { item: "mat", qty: 85, tags: ["gray"], size: { h: 27.9, w: 35.5, uom: "cm" } },
   { item: "mousepad", qty: 25, tags: ["gel", "blue"], size: { h: 19, w: 22.85, uom: "cm" } }
])

To retrieve the inserted documents/Select All Documents in a Collection
db.inventory.find( {} )



Specify Equality Condition
db.inventory.find( { status: "D" } )

Specify Conditions Using Query Operators

{ <field1>: { <operator1>: <value1> }, ... }
db.inventory.find( { status: { $in: [ "A", "D" ] } } ) - retrieves all documents from the inventory collection where status equals either "A" or "D"


Specify AND Conditions
db.inventory.find( { status: "A", qty: { $lt: 30 } } )

Specify OR Conditions
db.inventory.find( { $or: [ { status: "A" }, { qty: { $lt: 30 } } ] } )

Specify AND as well as OR Conditions
db.inventory.find( {
     status: "A",
     $or: [ { qty: { $lt: 30 } }, { item: /^p/ } ]
} )


Query Documents 
1. Query on Embedded/Nested Documents

Query on Nested Field
db.inventory.find( { "size.uom": "in" } ) - Specify Equality Match on a Nested Field
db.inventory.find( { "size.h": { $lt: 15 } } ) - Specify Match using Query Operator
db.inventory.find( { "size.h": { $lt: 15 }, "size.uom": "in", status: "D" } ) - Specify AND Condition

2. Query an Array

Match an Array
db.inventory.find( { tags: ["red", "blank"] } ) - queries for all documents where the field tags value is an array with exactly two elements, "red" and "blank"
db.inventory.find( { tags: { $all: ["red", "blank"] } } ) - find an array that contains both the elements "red" and "blank", without regard to order or other elements in the array


Query an Array for an Element
db.inventory.find( { tags: "red" } ) - queries for all documents where tags is an array that contains the string "red" as one of its elements

{ <array field>: { <operator1>: <value1>, ... } }
db.inventory.find( { dim_cm: { $gt: 25 } } ) - queries for all documents where the array dim_cm contains at least one element whose value is greater than 25

Specify Multiple Conditions for Array Elements
db.inventory.find( { dim_cm: { $gt: 15, $lt: 20 } } ) - Query an Array with Compound Filter Conditions on the Array Elements
db.inventory.find( { dim_cm: { $elemMatch: { $gt: 22, $lt: 30 } } } ) - Query for an Array Element that Meets Multiple Criteria
db.inventory.find( { "dim_cm.1": { $gt: 25 } } ) - Query for an Element by the Array Index Position
db.inventory.find( { "tags": { $size: 3 } } ) - Query an Array by Array Length


3. Query an Array of Embedded Documents
db.inventory.find( { "instock": { warehouse: "A", qty: 5 } } ) - Query for a Document Nested in an Array


3a. Specify a Query Condition on a Field in an Array of Documents
db.inventory.find( { 'instock.qty': { $lte: 20 } } ) - Specify a Query Condition on a Field Embedded in an Array of Documents
db.inventory.find( { 'instock.0.qty': { $lte: 20 } } ) - Use the Array Index to Query for a Field in the Embedded Document

3b. Specify Multiple Conditions for Array of Documents
db.inventory.find( { "instock": { $elemMatch: { qty: 5, warehouse: "A" } } } ) - A Single Nested Document Meets Multiple Query Conditions on Nested Fields 
db.inventory.find( { "instock": { $elemMatch: { qty: { $gt: 10, $lte: 20 } } } } ) - A Single Nested Document Meets Multiple Query Conditions on Nested Fields

3b.a Combination of Elements Satisfies the Criteria
db.inventory.find( { "instock.qty": 5, "instock.warehouse": "A" } )
db.inventory.find( { "instock.qty": { $gt: 10,  $lte: 20 } } )


4. Project Fields to Return from Query

Return All Fields in Matching Documents
db.inventory.find( { status: "A" } )

Return the Specified Fields and the _id Field Only
db.inventory.find( { status: "A" }, { item: 1, status: 1 } ) - A projection can explicitly include several fields by setting the <field> to 1 in the projection document

Suppress _id Field
db.inventory.find( { status: "A" }, { item: 1, status: 1, _id: 0 } ) -  remove the _id field from the results by setting it to 0 in the projection

Return All But the Excluded Fields
db.inventory.find( { status: "A" }, { status: 0, instock: 0 } ) -  returns all fields except for the status and the instock fields in the matching documents

Return Specific Fields in Embedded Documents
db.inventory.find(
   { status: "A" },
   { item: 1, status: 1, "size.uom": 1 }
)

Suppress Specific Fields in Embedded Documents
db.inventory.find(
   { status: "A" },
   { "size.uom": 0 }
)

Projection on Embedded Documents in an Array
db.inventory.find( { status: "A" }, { item: 1, status: 1, "instock.qty": 1 } ) 


Project Specific Array Elements in the Returned Array
db.inventory.find( { status: "A" }, { item: 1, status: 1, instock: { $slice: -1 } } )


Update Documents

Update Documents in a Collection

1a. Update a Single Document
db.inventory.updateOne(
   { item: "paper" },
   {
     $set: { "size.uom": "cm", status: "P" },
     $currentDate: { lastModified: true }
   }
)

db.inventory.updateMany(
   { "qty": { $lt: 50 } },
   {
     $set: { "size.uom": "in", status: "P" },
     $currentDate: { lastModified: true }
   }
)



1b. Updates with Aggregation Pipeline

The aggregation pipeline can consist of the following stages:
- $addFields
- $set
- $project
- $unset
- $replaceRoot
- $replaceWith



Delete Documents
db.inventory.deleteMany({}) - Delete All Documents
db.inventory.deleteMany({ status : "A" }) - Delete All Documents that Match a Condition
db.inventory.deleteOne( { status: "D" } ) - Delete Only One Document that Matches a Condition


https://www.mongodb.com/docs/manual/
https://www.mongodb.com/docs/manual/reference/sql-comparison/
https://www.mongodb.com/docs/manual/crud/
https://www.mongodb.com/docs/manual/core/schema-validation/
https://www.guru99.com/nosql-tutorial.html
https://www.couchbase.com/resources/why-nosql/
https://www.mongodb.com/developer/products/mongodb/map-terms-concepts-sql-mongodb/
https://www.mongodb.com/docs/manual/tutorial/query-documents/
