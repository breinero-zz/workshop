# Data Analytics with MongoDB - Workshop Exercises

Audience: Data analysts, developers, data engineers, data scientists, and anyone who works with data to gain insight.

Prerequisites: None. Familiarity with mongodb, mongodb shell and Atlas are helpful

Data Set: publicly available AirBnB data set that can be found here
http://insideairbnb.com/get-the-data.html

## What we will use: 
	MongoDB Atlas
		register at https://www.mongodb.com/cloud and create an account
download mongodb shell
	Windows: https://downloads.mongodb.org/win32/mongodb-shell-win32-x86_64-2008plus-ssl-4.0.2.zip
	Mac OS X: https://downloads.mongodb.org/osx/mongodb-shell-osx-ssl-x86_64-4.0.2.tgz
connect with mongodb shell by typing this command in your terminal:
mongo "mongodb+srv://dataanalyticsworkshop-sshrq.mongodb.net/test" --username workshop_user --password dotlocal2018

## MongoDB Connector for BI hosted on Atlas
use this information to connect your BI tool
	hostname: dataanalyticsworkshop-biconnector-sshrq.mongodb.net
	port: 27015
			user: workshop_user?source=admin
To connect with Excel: https://docs.atlas.mongodb.com/tutorial/connect-bic-excel/ 
for more: https://docs.atlas.mongodb.com/bi-connection 
	
## MongoDB Charts
		charts server: http://charts.local.mongodb.parts/
		username: will be assigned 
		password: will be assigned 
	
## MongoDB Compass
download Compass
	Windows: https://downloads.mongodb.com/compass/mongodb-compass-community-1.15.4-win32-x64.exe
	Mac OS X: https://downloads.mongodb.com/compass/mongodb-compass-community-1.15.4-darwin-x64.dmg  
connect to MongoDB Atlas with this URI: 
mongodb+srv://workshop_user:@dataanalyticsworkshop-sshrq.mongodb.net/admin

## Analysing AirBnB data with MongoDB Connector for BI

### Examine Data with Mongo Shell
mongo "mongodb+srv://dataanalyticsworkshop-sshrq.mongodb.net/airbnb" --username demo

### Examine data with MySQL CLI
Download  authentication plugin 
https://docs.mongodb.com/bi-connector/master/tutorial/install-auth-plugin-c/#install-auth-c-plugin

Install mongosql_auth.so to <mysql-home>/lib/plugin/ directory. <br/>

#### Connect to Hosted BIC 
mysql -h dataanalyticsworkshop-biconnector-sshrq.mongodb.net -P 27015 -u demo -p --default-auth= airbnb

#### Execute a join
select austinListingsAndReviews._id, austinListingsAndReviews_amenities.amenities AS amenities from austinListingsAndReviews JOIN  austinListingsAndReviews_amenities ON austinListingsAndReviews._id = austinListingsAndReviews_amenities._id limit 10;

### Define the connection to MongoDB (via BIC)
Goal: Define a DSN for an Atlas cluster
* Open 32-bit DSN manager
* Click Add
* Choose MongoDB Unicode
* Enter name, server, db, and credential information
* Test connection

### Import Data Into Excel
Goal: Import data into Excel using a simple SQL JOIN query
Insert new query
Select BIC DSN
In Advanced section, input query provided

#### Activity Find the most expensive neighborhoods in Austin
Goal: Create pivot table with average price/neighborhood 

Create a pivot table
Rows: neighborhood Values: price, average

Goal: Expand on above pivot table to include price per Airbed
Create pivot table: price, beds
Add calculated field price per bed

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

## Analysing AirBnB data with Aggregation Framework

Explore data by doing some basic finds
1. Connect to your mongodb Atlas cluster using mongodb shell: 
mongo "mongodb+srv://dataanalyticsworkshop-sshrq.mongodb.net/test" --username dotlocaluser --password dotlocaluser

2. Define which database we are going to use
use airbnb

3. Explore collections available to use for analysis. We will use austinListingsAndReviews collection.
show collections

4. Find a single document
db.austinListingsAndReviews.findOne()

5. Find how many listings have a real bed and are greater than $100 but less than $200 per night by filling in the blanks.
	Hint:
db.___.find( { 
price: { ___: ___, $lt: ___ } } , 
{ ___ : "Real Bed" 
} ).count()



Learn how to do aggregations you usually do in SQL
We will use the following operators:	
$match
$group

Take a look at this example aggregation which computes minimum price per night by number of beds
db.austinListingsAndReviews.aggregate( [
   {
     $group: {
        _id: "$beds",
        avg_by_beds: { $min: "$price" }
     }
   },
   { $sort: { avg_by_beds: 1 } }
] )


1. Construct a very similar query to find average price per night by room type by filling in the blanks. 
db.austinListingsAndReviews.aggregate( [
   {
     $group: {
        _id: "___",
        avg_by_roomtype: { $avg: "___" }
     }
   }}
] )

2. Let’s practice some more. This time write the query from scratch to find the average price per night by suburb. Examine the document structure first. Hint: suburb is a field within a subdocument address; use dot notation to reference it.
3. Now let’s combine the skills we’ve learned. Compute the count of properties and average price per night by suburbs and number of bedrooms by filling in the blanks.
db.austinListingsAndReviews.aggregate([
	{"$group" : 
		{
_id:{suburb:"$___", bedrooms:"$___"}, 
___:{$sum:1}, 
avg:{$___:"$price"}}
		}
])

4. Use the query in #3 and add a $match stage to only show those properties that have a pool.

## Learn how to do aggregations you cannot do in SQL
We will use the following operators:	
$objectToArray
$geonear
$graphlookup

And the following aggregation pipeline stages:
	$match → $project: → $unwind: "$scores" → $group

1. Let’s take a look at an example first. Let’s assume we want to determine average review scores for all properties in Austin based on some amenities. Remember that review_scores is a sub document. Luckily we have $objectToArray to transform our object into an array which we can then $unwind and $group. 

db.austinListingsAndReviews.aggregate([
   { $match : {amenities: { $in: ["Pool", "Air conditioning", "Wifi"] } } },
   { $project: { scores: { $objectToArray: "$review_scores" } } },
   { $unwind: "$scores" },
   { $group: { _id: "$scores.k", average_score: { $avg: "$scores.v" } } }
])

Compare two neighbourhoods - South Lamar and West Campus - and decide based on average review where you would rather stay. Assume you also want a place that has pool, wifi, and air-conditioning, under $200
Hint: You will need to look up how to use $and operator for this

2. Let’s find all airbnb listings around our .local Austin venue, Alamo Drafthouse. The coordinates are 30.2988474,-97.7064386. We can do this by using $geoNear operator, as follows:

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

Now practice writing this query for yourself. Use $geoNear to find airbnbs near location of your choice and add the same search criteria we used in #1 above - a place that has pool, wifi, and air-conditioning, under $200
You can find $geoNear documentation and additional examples here. 

3. Take a look at this example. Let’s say we want to build a recommendation engine based on users that reviewed the same properties. We can use $graphlookup to run a recursive query that says: “for a given reviewer, e.g. 7538 let’s find all listings reviewed by users who also reviewed the same listings as 7538”

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



Now build your own $graphLookup query. Some host in Austin have multiple listings, for example, host_id:100208662. Construct a $graphlookup query that finds all the listings by that host. You can find $graphLookup documentation and additional examples here.


## The MongoDB Connector for Apache Spark

Using the machine learning ALS library in Spark to generate a set of personalized movie recommendations for a given user based MovieLens dataset. </br>

http://files.grouplens.org/datasets/movielens/ml-latest-small.zip

<br/>Ensure you have downloaded the data and imported it into MongoDB with mongorestore. You can find instructions on using mongorestore in the MongoDB Documentation. You can also run this full example with Execute the following Databricks Notebook<br/>
https://community.cloud.databricks.com/?o=2374217986360057#notebook/42327714871690

#### Download Spark 
http://apache.claz.org/spark/spark-2.3.2/spark-2.3.2-bin-hadoop2.7.tgz
tar -xf spark-2.3.2-bin-hadoop2.7.tgz

#### Execute Spark
from the commandline, start the Spark interactive shell

```
./spark-2.3.1-bin-hadoop2.7/bin/spark-shell \
 --conf \
"spark.mongodb.input.uri=mongodb+srv://demo:demo@dataanalyticsworkshop-sshrq.mongodb.net/recommendation.ratings" \
 --conf \
  "spark.mongodb.output.uri=mongodb+srv://demo:demo@dataanalyticsworkshop-sshrq.mongodb.net/recommendation.perUser" \
 --packages org.mongodb.spark:mongo-spark-connector_2.11:2.3.0
```

#### Run the scala example code

```
import com.mongodb.spark._

val ratings = spark.read.format("com.mongodb.spark.sql.DefaultSource").option("database", "recommendation").option("collection", "ratings").load()

case class Rating(userId: Int, movieId: Int, rating: Double, timestamp: Long)

import spark.implicits._
val ratingsDS = ratings.as[Rating]
ratingsDS.cache()
ratingsDS.show()

import org.apache.spark.ml.evaluation.RegressionEvaluator
import org.apache.spark.ml.recommendation.ALS

/* import org.apache.spark.ml.recommendation.ALS.Rating */
val Array(training, test) = ratings.randomSplit(Array(0.8, 0.2))

// Build the recommendation model using ALS on the training data
val als = new ALS().setMaxIter(5).setRegParam(0.01).setUserCol("userId").setItemCol("movieId").setRatingCol("rating")
val model = als.fit(training)

// Evaluate the model by computing the RMSE on the test data
// Note we set cold start strategy to 'drop' to ensure we don't get NaN evaluation metrics
model.setColdStartStrategy("drop")
val predictions = model.transform(test)

val evaluator = new RegressionEvaluator().setMetricName("rmse").setLabelCol("rating").setPredictionCol("prediction")
val rmse = evaluator.evaluate(predictions)
println(s"Root-mean-square error = $rmse")


// Write recommendations back to MongoDB
import org.apache.spark.sql.functions._
val docs  = predictions.map( r => ( r.getInt(4), r.getInt(1),  r.getDouble(2) ) ).toDF( "userID", "movieId", "rating" )

docs.show()

docs.write.format("com.mongodb.spark.sql.DefaultSource").mode("overwrite").option("database", "recommendation").option("collection", "recommendations").save()
```
