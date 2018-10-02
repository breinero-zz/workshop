# Data Analytics with MongoDB - Workshop Exercises

__Audience:__ 
Data analysts, developers, data engineers, data scientists, and anyone who works with data to gain insight.
__Prerequisites:__ 
None. Some familiarity with mongodb, mongodb shell and Atlas are helpful
__Data Set:__ 
This workshop uses the publicly available [AirBnB data set](http://insideairbnb.com/get-the-data.html). This data set has been pre-loaded into an instance of MongoDB hosted in Altas, our fully managed MongoDB-as-a-Service database. Attendees of the live workshop will run the exercises against this hosted instance. If you missed the workshop, you may register an Atlas account here https://www.mongodb.com/cloud and run these examples anytime.

## Part 1: Analysing AirBnB data with the BI Connector

### Activity 1: Examine AirBnb Data With the MongoDB Shell
__the MongoDB Shell__ (the command line interface for MongoDB)
Download the shell here:
  Windows: [mongodb-shell-win32-x86_64-2008plus-ssl-4.0.2.zip](https://downloads.mongodb.org/win32/mongodb-shell-win32-x86_64-2008plus-ssl-4.0.2.zip)
  Mac OS X: [mongodb-shell-osx-ssl-x86_64-4.0.2.tgz](https://downloads.mongodb.org/osx/mongodb-shell-osx-ssl-x86_64-4.0.2.tgz)
  
Use the mongo command line shell to connect to the Altas Instance typing
this command in your terminal:
   ```
   mongo "mongodb+srv://dataanalyticsworkshop-sshrq.mongodb.net/airbnb" \
   --username workshop_user \
   --password dotlocal2018
   ```

__Step 1__ List the collections in the database 
From the sell prompt, execute the following command
```
show collections
```
__Step 2__ examine an individual document in the austinListingsandReviews collection
at the prompt execute:
```
db.austinListingsAndReviews.findOne()
``` 

### Activity 2: Examine the data through the BI Connnector, using the MySQL CLI 
__The MongoDB Connector for BI__ translates queries and data between SQL and MongoDB, allowing you to connect data visualiztion tools such as Tableau, PowerBI, MS Excel, or the MySQL Command Line to data managed in MongoDB. We are going to use a fully managed version of the BI Connnector, hosted in Atlas. The BI Connector can be accessed with the following information

  hostname: dataanalyticsworkshop-biconnector-sshrq.mongodb.net
  port: 27015
    user: workshop_user?source=admin
    password: dotlocal2018

To Connect the BI Connector from the MySQL client
__Step 1__ Download and install [MySQL](https://dev.mysql.com/downloads/windows/installer/8.0.html)

__Step 2__ Install MongoDB Authentication Plugin 
The Authentication Plugin eases authentication between the BI Connector and SQL clients. Download and install the plugin per our [instructions](https://docs.mongodb.com/bi-connector/master/tutorial/install-auth-plugin-c/#install-auth-c-plugin)

__Step 3__ Connect view MySQL Client
from your command line prompt, execute
```
mysql -h dataanalyticsworkshop-biconnector-sshrq.mongodb.net \
-P 27015 \
-u workshop_user -p \
--default-auth=mongosql_auth airbnb
```
authenticate with the password "dotlocal2018"

__Step 4__ Explore the tabular representation of the airbnb data
at the MySQL prompt execute:
```
mysql> show tables;
mysql> describe austinListingsAndReviews;
mysql> describe austinListingsAndReviews_amenities;
```

__Step 5__ Execute a JOIN to list the set of amenities per listing
```
mysql> SELECT austinListingsAndReviews._id AS id, austinListingsAndReviews_amenities.amenities AS amenity 
FROM austinListingsAndReviews 
JOIN  austinListingsAndReviews_amenities 
    ON austinListingsAndReviews._id = austinListingsAndReviews_amenities._id
LIMIT 10;
```
### Activity 3: Analyze Listings Data with Excel
__Step 1__ Download MongoDB ODBC driver
[Select and download](https://github.com/mongodb/mongo-odbc-driver/releases/) the appropriate version for your OS

__Step 2__ Create a DSN
Follow OS specfic [instructions](https://docs.atlas.mongodb.com/tutorial/create-system-dsn/)

__Step 3__ Connect from Excel
* Start Excel, select the "data" Tab, and clik on "New Database Query"
* Select the DSN you created in step 2 from the pop-up window and click "ok"
* enter username "workshop_user" and password "dotlocal2018"
More detailed and Illustrated details on connecting from Excel are available in this [tutorial](https://docs.mongodb.com/bi-connector/master/connect/excel/)  

__Step 4__ Enter Query or Join 
In the popup's "SQL Query" section, input query
```
SELECT *
FROM austinListingsAndReviews 
JOIN  austinListingsAndReviews_amenities "return data"
    ON austinListingsAndReviews._id = austinListingsAndReviews_amenities._id;
```
__Step 5__ Load data into sheet
Click "return data" to populate the spread sheet

#### Excercise: Find the most expensive neighborhoods in Austin
__Goal:__ Create pivot table with average price/neighborhood 
__step 1__ Create a pivot table
Set Rows: neighborhood Values: price, average

__Goal:__ Expand on above pivot table to include price per Airbed
__Step 1__ Create pivot table: price, beds
__Step 2__ Add calculated field price per bed

## Part 2: Analysing AirBnB data with Charts
    charts server: http://charts.local.mongodb.parts/
    username: workshop_user
    password: dotlocal2018 
    
## Analysing AirBnB data with Charts
Add a Data Source
Sign in to Charts: http://charts.local.mongodb.parts
Go to Data Sources tab
Click New Data Source
Use the URI: 
Choose a data source for a city of your choice
Keep the data source private (don’t share with others)
Publish the data source
Edit the Alias to have a friendly name, e.g. “AirBnB New York”
Create a Dashboard
Go to the Dashboards tab
Click New Dashboard
Enter a name and description, e.g. “Tom’s AirBnB dashboard”
Add some charts
For each of the following, click Add Chart from the dashboard view. After the chart has been saved, resize and rearrange on the dashboard as desired.

### Multi-Series Stacked Bar Chart
Goal: Show the localities with the most properties, split out by property type.
Data Source: <AirBnB data source for chosen city>
Chart Type: Bar / Stacked
Encodings:
X axis: _id, Count aggregation
Y axis: address.government_area
Sort By: Aggregated Value, Descending
Limit: 30
Series: property_type
Chart Title: Properties by Location
Result:

### Coloured Bar Chart 
Goal: Show the areas with the most expensive properties, and correlate with the review scores.
Data Source: <AirBnB data source for chosen city>
Chart Type: Bar / Colored
Encodings:
X axis: price, Mean aggregation
Y axis: address.government_area
Sort By: Aggregated Value, Descending
Limit: 30
Color: review_scores.review_scores_location
Chart Title: Price by Location, coloured by Location Review score
Result:

### Review Scores Histogram
Goal: Show the number of properties with various review scores
Data Source: <AirBnB data source for chosen city>
Chart Type: Column / Grouped
Encodings:
X axis: review_scores.review_scores_rating 
Binning: On, bin size 5
Y axis: _id, Count aggregation
Filter (to hide properties with no review score):
  { 'review_scores.review_scores_rating': { $gte: 0}}
Chart Title: Review Score Histogram
Result:

### Filtered Donut Chart
Goal: Show the types of properties that contain pools
Data Source: <AirBnB data source for chosen city>
Chart Type: Circular / Donut
Encodings:
Label: Property Type
Arc: _id, Count aggregation
Filter (to show only properties with pools):
  { amenities: 'Pool'}
Chart Title: Properties with Pools
Result:

### Area Chart with Binning
Goal: Show the number of reviews of any property, month by month
Data Source: <AirBnB data source for chosen city>
Chart Type: Area / Discrete
Encodings:
X axis: reviews.date
Array Reduction: Unwind Array
Binning: On, bin by Month
Y axis: _id, Count aggregation
Chart Title: Reviews over time
Result:

### Array Reduction - Existence of Value
Goal: Show the number of properties with and without pools by area
Data Source: <AirBnB data source for chosen city>
Chart Type: Column / Stacked
Encodings:
X axis: address.government_area
Y axis: _id, Count aggregation
Series: amenities
Array Reduction: Existence of value: “Pool”
Chart Title: Pool or No Pool
Result:


### Create your own charts
Now that you’ve got an understanding of what Charts can do, try creating your own charts that provides some new insight from this data set.

If you want to make something similar to one of your existing charts, click the [...] menu on the chart and then choose Duplicate Chart. 
Share your dashboard and data source

When you’ve finished creating your dashboard, share it with the class!
Go to the Data Sources tab
Find your data source and click the ACCESS button
Choose to share with Everyone, with Reader role
Save the data source 
Go to the Dashboards tab
Find your dashboard and click the ACCESS button
Choose to share with Everyone, with Viewer role.

Once others have shared their dashboards with you, take a look at what they have created!

## Part 3: Analysing AirBnB data with Aggregation Framework
The following examples are executed in the mongo shell, but you are welcome to use __MongoDB Compass__ the GUI for MongoDB. __Compass__ Allows you Visually explore your data and construct ad hoc queries with its _Aggregation Builder_.

__Download Compass__
  Windows: [mongodb-compass-community-1.15.4-win32-x64.exe](https://downloads.mongodb.com/compass/mongodb-compass-community-1.15.4-win32-x64.exe)
  Mac OS X: [mongodb-compass-community-1.15.4-darwin-x64.dmg](https://downloads.mongodb.com/compass/mongodb-compass-community-1.15.4-darwin-x64.dmg)
  
__connect to MongoDB Atlas with this URI:__
mongodb+srv://workshop_user:@dataanalyticsworkshop-sshrq.mongodb.net/admin

### Activity: Explore data by doing some basic finds
1. Connect to your mongodb Atlas cluster using mongodb shell: 
```
mongo "mongodb+srv://dataanalyticsworkshop-sshrq.mongodb.net/test" --username dotlocaluser --password dotlocaluser
```
2. Define which database we are going to use
```
use airbnb
```

3. Explore collections available to use for analysis. We will use austinListingsAndReviews collection.
```
show collections
```

4. Find a single document
```
db.austinListingsAndReviews.findOne()
```
5. Find how many listings have a real bed and are greater than $100 but less than $200 per night by filling in the blanks.
_Hint:
```
db.___.find( { 
price: { ___: ___, $lt: ___ } } , 
{ ___ : "Real Bed" 
} ).count()
```

### Activity: Learn how to do aggregations you usually do in SQL
We will use the following operators:  
$match
$group

Take a look at this example aggregation which computes minimum price per night by number of beds
```
db.austinListingsAndReviews.aggregate( [
   {
     $group: {
        _id: "$beds",
        avg_by_beds: { $min: "$price" }
     }
   },
   { $sort: { avg_by_beds: 1 } }
] )
```

1. Construct a very similar query to find average price per night by room type by filling in the blanks. 
```
db.austinListingsAndReviews.aggregate( [
   {
     $group: {
        _id: "___",
        avg_by_roomtype: { $avg: "___" }
     }
   }}
] )
```
2. Let’s practice some more. This time write the query from scratch to find the average price per night by suburb. Examine the document structure first. Hint: suburb is a field within a subdocument address; use dot notation to reference it.
3. Now let’s combine the skills we’ve learned. Compute the count of properties and average price per night by suburbs and number of bedrooms by filling in the blanks.
```
db.austinListingsAndReviews.aggregate([
  {"$group" : 
    {
_id:{suburb:"$___", bedrooms:"$___"}, 
___:{$sum:1}, 
avg:{$___:"$price"}}
    }
])
```
4. Use the query in #3 and add a $match stage to only show those properties that have a pool.

## Learn how to do aggregations you cannot do in SQL
We will use the following operators:  
$objectToArray
$geonear
$graphlookup
And the following aggregation pipeline stages:
  $match → $project: → $unwind: "$scores" → $group

1. Let’s take a look at an example first. Let’s assume we want to determine average review scores for all properties in Austin based on some amenities. Remember that review_scores is a sub document. Luckily we have $objectToArray to transform our object into an array which we can then $unwind and $group. 
```
db.austinListingsAndReviews.aggregate([
   { $match : {amenities: { $in: ["Pool", "Air conditioning", "Wifi"] } } },
   { $project: { scores: { $objectToArray: "$review_scores" } } },
   { $unwind: "$scores" },
   { $group: { _id: "$scores.k", average_score: { $avg: "$scores.v" } } }
])
```
Compare two neighbourhoods - South Lamar and West Campus - and decide based on average review where you would rather stay. Assume you also want a place that has pool, wifi, and air-conditioning, under $200
Hint: You will need to look up how to use $and operator for this

2. Let’s find all airbnb listings around our .local Austin venue, Alamo Drafthouse. The coordinates are 30.2988474,-97.7064386. We can do this by using $geoNear operator, as follows:
```
 db.austinListingsAndReviews.aggregate([
   {
     $geoNear: {
        near: { type: "Point", coordinates: [ -97.7064386 , 30.2988474 ] },
        distanceField: "dist.calculated",
        maxDistance: 500,
        includeLocs: "dist.location",
        num: 5,
        spherical: true
     }
   }
]).pretty()
```
Now practice writing this query for yourself. Use $geoNear to find airbnbs near location of your choice and add the same search criteria we used in #1 above - a place that has pool, wifi, and air-conditioning, under $200
You can find $geoNear documentation and additional examples here. 

3. Take a look at this example. Let’s say we want to build a recommendation engine based on users that reviewed the same properties. We can use $graphlookup to run a recursive query that says: “for a given reviewer, e.g. 7538 let’s find all listings reviewed by users who also reviewed the same listings as 7538”
```
db.austinListingsAndReviews.aggregate( [
   { $match: { "reviews.reviewer_id": "7538" } } ,{ $unwind: "$reviews" },{
      $graphLookup: {
         from: "austinListingsAndReviews",
         startWith: "$reviews.reviewer_id",
         connectFromField: "reviews.reviewer_id",
         connectToField: "reviews.reviewer_id",
         as: "reviewed_listings",
         maxDepth: 1,
         depthField: "depth",
         restrictSearchWithMatch: { "reviews.reviewer_id": { $ne: "7538" } }
      }},
   {  $project: { 
        _id: 0,
        listing: "$_id",
        url: "$listing_url" }}]).pretty()
```

Now build your own $graphLookup query. Some host in Austin have multiple listings, for example, host_id:100208662. Construct a $graphlookup query that finds all the listings by that host. You can find $graphLookup documentation and additional examples [here](https://docs.mongodb.com/manual/reference/operator/aggregation/graphLookup/).


## Part 4: Analysing data with The MongoDB Connector for Apache Spark

In this section we'll use the machine learning ALS library in Spark to generate a set of personalized movie recommendations for a given user, based on the publically available [MovieLens dataset](http://files.grouplens.org/datasets/movielens/ml-latest-small.zip)

<br/>Ensure you have downloaded the data and imported it into MongoDB with mongorestore. You can find instructions on using mongorestore in the MongoDB Documentation. You can also run this full example with Execute the following [Databricks Notebook](
https://community.cloud.databricks.com/?o=2374217986360057#notebook/42327714871690)

__Step 1__ Download Spark 
From the commandline download and decompress Spark v2.3
```
http://apache.claz.org/spark/spark-2.3.2/spark-2.3.2-bin-hadoop2.7.tgz
tar -xf spark-2.3.2-bin-hadoop2.7.tgz
```
__Step 2__ Start the Spark interactive shell
From the commandline execute
```
./spark-2.3.1-bin-hadoop2.7/bin/spark-shell  \
--conf \
"spark.mongodb.input.uri=mongodb+srv://demo:demo@dataanalyticsworkshop-sshrq.mongodb.net/airbnb.austinListingsAndReviews \
--conf   \
"spark.mongodb.output.uri=mongodb+srv://demo:demo@dataanalyticsworkshop-sshrq.mongodb.net/airbnb.clusters" \
--packages org.mongodb.spark:mongo-spark-connector_2.11:2.3.0
```

__Step 3__ Prepare to Read data in from MongoDB
At the Spark prompt execute the follwing code
```// First prefilter data in MongoDB, filtering out fields we don't need
val pipeline = """[ { $project: {
 "_id": 0,
 "accommodates": "$accommodates",
 "price": "$price",
 "coordinates": "$address.location.coordinates"
 }
}]"""

// Read from the collection defined in the command line parameter
val ds = spark.read.format("com.mongodb.spark.sql.DefaultSource").option("pipeline", pipeline).load()

// The Spark Connector will use MongoDB's $sample command to infer the Dataset's schema from a sample set of docs.
// However, you can avoid the need to sample if you know what the schema is
// We use the following case class to explicitly declare the schema without sampling

case class Listing( accommodates: Int, price: BigDecimal, coordinates: Array[Double]  )

// This second case class is used to flatten out the coordinate array in to lat / long pairs
case class FlatListing( accommodates: Int, price: BigDecimal, latitude: Double, longitude: Double )

import spark.implicits._
val listings = ds.as[Listing].map( v => (  FlatListing ( v.accommodates, v.price, v.coordinates(1), v.coordinates(0) ) ) )

// let's cache the dataset as we are going to use it in a multi-pass algorithm
listings.cache()
// Let's see what the data looks like so far
listings.show()
```

 __Step 4__ Reformat data so that the KMeans class can process it
At the Spark prompt execute the follwing code
```
// We need to reformat the dataset so that it can be processed by MLLib's KMeans class
import org.apache.spark.ml.feature._
val assembler = new VectorAssembler().setInputCols( listings.columns ).setOutputCol("features")
val vectors = assembler.transform(listings)

//Here's what it looks like now
vectors.show()
```

 __Step 5__ Train a KMeans model with the prepared data vector 
At the Spark prompt execute the follwing code
```
import org.apache.spark.ml.clustering.{KMeans, KMeansModel}
// Cluster the data into 4 classes of listing using KMeans.
// This is where the machine learning magic happens!!!
val kmeans = new KMeans().setK(4).setSeed(1L).setMaxIter(5)
val model = kmeans.fit( vectors )

// let see what our centroids look like
model.clusterCenters.foreach( println )
```

 __Step 6__ Assign each airbnb listing in the area to a cluster 
At the Spark prompt execute the follwing code
```
// let's now assign each listing to a cluster
val classifiedListings = model.transform( vectors ).drop( "features" )
// and see what that looks like
classifiedListings.show()
```
 __Step 7__ Write the cluster assignments back to MongoDB 
At the Spark prompt execute the follwing code
// finally, let's write the listing clusters to MongoDB
classifiedListings.write.format("com.mongodb.spark.sql.DefaultSource").mode("overwrite").save()
```
```
