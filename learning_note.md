
Enterprise Edition is required because we will be creating a Materialized View, which requires Enterprise Edition. 


#Run the CURRENT_ACCOUNT() command to get your account locator
##Account Locator
select current_account();  
FI58683

As of November 1, 2023, we have moved your existing badges to ACHIEVE.SNOWFLAKE.COM!

When we talk about Data Types - we usually mean the column type settings for Structured Data stored in Tables, like NUMERIC, VARCHAR, VARIANT and more. But we can also talk about the structure of the files that hold the data. In this workshop we will always say Data Structure Types when we mean the file storage structures for data that can be referred to as Structured, Semi-Structured, and Unstructured. 

Structured data in a file (like a .txt or .csv) is arranged as a series of rows (often each line in the file is a row). Each row in the file is separated into columns. The value used to separate each row of data into columns can vary. Sometimes a comma is used to separate the values in each row into columns. Sometimes a tab is used. You can have pipes (sometimes called "vertical bars") as column delimiters, or many other symbols.  Regardless of which separator is used, any data file with data arranged in rows and columns (and no nesting) is called "structured data" and we often load each value into a column and each line into a row in our Snowflake tables. 

Semi-Structured data (in a file like .json, or .xml)  is data that has unpredictable levels of nesting and often each "row" of data can vary widely in the information it contains. Semi-structured data stored in a flat file may have markup using angle brackets (like XML) or curly brackets (like JSON). Snowflake can load and process common nested data types like JSON, XML, Avro, Parquet, and ORC. Each of the semi-structured data types is loaded into Snowflake tables using a column data type called VARIANT.  A VARIANT column might contain many key-value pairs and multiple nested records and it will also keep the markup symbols like curly braces and angle brackets.  NOTE: A file can be semi-structured, even if the file's name ends in .txt. If the data inside a file is formatted using semi-structured layout and markup, the file is considered semi-structured.  

There is a third type of data file called Unstructured data. (File names will have extensions like .mp3, .mp4, .png, etc.)  Snowflake added support for Unstructured Data in August of 2021. Snowflake has ways to help you store, access, process, manage, govern, and share unstructured data. Videos, images, audio files, pdfs and other file types are considered unstructured data.

Snowflake's goal is to make it very easy for you to combine all three types of data so that you can analyze and process it together rather than needing to use multiple tools to do your data work. 

 Rapid Prototyping techniques Proof of Concept.

 A Proof of Concept usually doesn't require perfect data structures, it needs just enough data development to show a concept or idea to others, and give them a rough idea of how the app would behave or work. Zena agrees that using rapid development techniques sounds good. 

External Stage Object --> S3 Bucket

https://uni-klaus.s3.us-west-2.amazonaws.com/

https://uni-klaus.s3.us-west-2.amazonaws.com/clothing/90s_tracksuit.png

s3 bucket
s3://uni-klaus/clothing

That's right aws bucket names are globaly unique. No other AWS account will have the same bucket name

And the same applies for Azure and GCS blob storage alternatives. All of them are unique within given cloud provider across all regions.

s3://uni-klaus/zenas_metadata

s3://uni-klaus/sneakers


When we first learned about stages and the staging of files, we said that Snowflake Internal tables (regular tables) were like the shelving in a real-world warehouse. With tables being a place where we would very deliberately place our data for long-term storage. We also claimed that the yellow areas on the floor of a a warehouse were like stages in Snowflake. 


By the time we finished Badge 2: Data Application Builders Workshop, Mel understood the difference between External and Internal Stages, how to set them up and use them. He also understood that when we talk about "Stages" there are actually 3 parts. The cloud folder is the stage's storage location, the files within those locations are "staged data", and the objects we create in Snowflake are not locations, instead they are connections to cloud folders - which metaphorically can also be called "windows", or shown as loading bay doors on diagrams. 

As it turns out, a Snowflake Stage Object can be used to connect to and access files and data you never intend to load!!! 

Zena can create a stage that points to an external bucket. Then, instead of pulling data through the stage into a table, she can reach through the stage to access and analyze the data where it is already sitting.

She does not have to load data, she can leave it where it is already stored, but still access it AND if she uses a File Format, she can make it appear almost as if it is actually loaded! (weird, but true!) 

REDEFINING THE WORD "STAGE" FOR SNOWFLAKE ADVANCED USE

We already know that in the wider world of Data Warehousing, we can use the word "stage" to mean "a temporary storage location", and we can also use "stage" to mean a cloud folder where data is stored -- but now, more than ever, we should open our mind to the idea that a defined Snowflake Stage Object is most accurately thought of as a named gateway into a cloud folder where, presumably, data files are stored either short OR long term. 

Named Gateway to access cloud folder

In this lesson, you'll learn different things you can do  with data even when you don't load it. We'll call it "non-loaded" so that we can separate the concept of data that is loaded into Snowflake and then output back into a stage using an "unload" process. This data that is NEVER loaded, we'll call "non-loaded." 

list @UNI_KLAUS_ZMD;


select $1 from @UNI_KLAUS_ZMD/product_coordination_suggestions.txt

Snowflake hasn't been told anything about how the data in these files is structured so it's just making assumptions.  Snowflake is presuming that the files are CSVs because CSVs are a very popular file-formatting choice. It's also presuming each row ends with CRLF (Carriage Return Line Feed) because CRLF is also very common as a row delimiter.

Snowflake hedges its bets and presumes if you don't tell it anything about your file, the file is probably a standard CSV.

By using these assumptions, Snowflake treats the product_coordination_suggestions.txt file as if it only has one column and one row. 

carets (^) 

create file format zmd_file_format_1
RECORD_DELIMITER = '^';

select $1
from @uni_klaus_zmd/product_coordination_suggestions.txt
(file_format => zmd_file_format_1);

create file format zmd_file_format_2
FIELD_DELIMITER = '^';  

create file format zmd_file_format_3
FIELD_DELIMITER = '='
RECORD_DELIMITER = '^'; 

create file format zmd_file_format_3
FIELD_DELIMITER = '='
RECORD_DELIMITER = '^'; 

select $1,$2,$3 
from @uni_klaus_zmd/product_coordination_suggestions.txt
(file_format => zmd_file_format_3);


create or replace file format zmd_file_format_1
RECORD_DELIMITER = ';';

create or replace file format zmd_file_format_2
FIELD_DELIMITER = '|'
RECORD_DELIMITER = ';'
TRIM_SPACE = True;

select $1,$2,$3 
from @uni_klaus_zmd/swt_product_line.txt
(file_format => zmd_file_format_2);



create or replace file format zmd_file_format_1
RECORD_DELIMITER = ';'
TRIM_SPACE = True;

select replace($1,chr(13)||chr(10)) as sizes_available
from @uni_klaus_zmd/sweatsuit_sizes.txt
(file_format => zmd_file_format_1 )
where sizes_available<>'';


create or replace view zenas_athleisure_db.products.sweatsuit_sizes as 
select replace($1,chr(13)||chr(10)) as sizes_available
from @uni_klaus_zmd/sweatsuit_sizes.txt
(file_format => zmd_file_format_1 )
where sizes_available<>'';

create or replace view zenas_athleisure_db.products.SWEATBAND_PRODUCT_LINE as 
select replace($1,chr(13)||chr(10)) as PRODUCT_CODE,$2 as HEADBAND_DESCRIPTION,$3 as WRISTBAND_DESCRIPTION
from @uni_klaus_zmd/swt_product_line.txt
(file_format => zmd_file_format_2);

select replace(replace(replace(UPPER(RELATIVE_PATH),'/'),'_',' '),'.PNG') as PRPDUCT_NAME
from directory(@uni_klaus_clothing);

https://uni-klaus.s3.us-west-2.amazonaws.com/clothing

We can do this with a quick CROSS JOIN.  Cross Joins are also called "cartesian products" and many times when data professionals talk about cartesian products they are describing a bad join that resulted in many more records than they intended. In this case, though, the cartesian product (multiplicative) is our goal.



select color_or_style
, direct_url
, price
, size as image_size
, last_modified as image_last_modified
, sizes_available
from sweatsuits 
join directory(@uni_klaus_clothing) 
on relative_path = SUBSTR(direct_url,54,50)
cross join sweatsuit_sizes;

select b.color_or_style
,b.direct_url
,b.price
,a.size as image_size
,a.last_modified as image_last_modified
from directory(@uni_klaus_clothing) a
JOIN ZENAS_ATHLEISURE_DB.PRODUCTS.SWEATSUITS b
ON replace(b.DIRECT_URL,'https://uni-klaus.s3.us-west-2.amazonaws.com/clothing') = a.RELATIVE_PATH;

I used 'contains' function as below on join. contains (sw.direct_url, cl.relative_path)
 DIRECT_URL LIKE '%' || RELATIVE_PATH ;

The Data Lake metaphor was introduced to the world in 2011 by James Dixon, who was the Chief Technology Officer for a company called Pentaho, at that time.

Dixon said:

If you think of a data mart as a store of bottled water -- cleansed and packaged and structured for easy consumption -- the data lake is a large body of water in a more natural state. The contents of the data lake stream in from a source to fill the lake, and various users of the lake can come to examine, dive in, or take samples.

When we talk about Data Lakes at Snowflake, we tend to mean data that has not been loaded into traditional Snowflake tables. We might also call these traditional tables "native" Snowflake tables, or "regular" tables. 

As we've already seen, Structured and Semi-structured data that is sitting outside of Snowflake can be easily accessed and analyzed using familiar Snowflake tools like views, file formats, and SQL queries. 

We've also seen how Unstructured data, not loaded into Snowflake, can be accessed with a special Snowflake tool called a Directory Table. We've also seen how Directory Tables can be used in combination with functions, joins, internal tables, and standard views to access that non-loaded data. 

And through all this, we've seen that bringing together loaded and non-loaded data is a simple and seamless process.  When some data is loaded and some is left in a non-loaded state the two types can be joined and queried together, this is sometimes referred to as a Data Lakehouse. 


### Lession 5

select $1 from @trails_geojson
(file_format => FF_JSON);

select $1 from @trails_parquet
(file_format => FF_parquet);


USE ROLE ACCOUNTADMIN;
GRANT USAGE ON INTEGRATION DORA_API_INTEGRATION TO ROLE SYSADMIN;


select $1:sequence_1 as sequence_1,
$1:trail_name as trail_name,
$1:latitude as latitude,
$1:longitude as longitude,
$1:sequence_2 as sequence_2,
$1:elevation as elevation
from @trails_parquet
(file_format => FF_parquet)
order by $1:sequence_1;

Remember that Latitudes are between 0 (the equator)  and 90 (the poles) so no more than 2 digits are needed left of the decimal for latitude data.

Longitudes are between 0 (the prime meridian) and 180. So no more than 3 digits are needed to the left of the decimal for longitude data.

select 
 $1:sequence_1 as point_id,
 $1:trail_name::varchar as trail_name,
 $1:latitude::number(11,8) as lng, --remember we did a gut check on this data
 $1:longitude::number(11,8) as lat
from @trails_parquet
(file_format => ff_parquet)
order by point_id;

select $1:sequence_1 as sequence_1,
$1:trail_name::varchar as trail_name,
$1:latitude::number(11,8) as longitude,
$1:longitude::number(11,8) as latitude,
$1:sequence_2 as sequence_2,
$1:elevation as elevation
from @trails_parquet
(file_format => FF_parquet)
order by $1:sequence_1;


select top 100
lng||' '||lat as coord_pair,
'POINT('||coord_pair||')' as trail_point
from cherry_creek_trail;

create or replace view cherry_creek_trail as
select 
 $1:sequence_1 as point_id,
 $1:trail_name::varchar as trail_name,
 $1:latitude::number(11,8) as lng,
 $1:longitude::number(11,8) as lat,
 lng||' '||lat as coord_pair
from @trails_parquet
(file_format => ff_parquet)
order by point_id;

select 
'LINESTRING('||
listagg(coord_pair, ',') 
within group (order by point_id)
||')' as my_linestring
from cherry_creek_trail
where point_id <= 10
group by trail_name;

select
$1:features[0]:properties:Name::string as feature_name
,$1:features[0]:geometry:coordinates::string as feature_coordinates
,$1:features[0]:geometry::string as geometry
,$1:features[0]:properties::string as feature_properties
,$1:crs:properties:name::string as specs
,$1 as whole_object
from @trails_geojson (file_format => ff_json);

select 
'LINESTRING('||
listagg(coord_pair, ',') 
within group (order by point_id)
||')' as my_linestring
,st_length(TO_GEOGRAPHY(my_linestring)) as length_of_trail --this line is new! but it won't work!
from cherry_creek_trail
group by trail_name;


DENVER_AREA_TRAILS


create or replace view DENVER_AREA_TRAILS( FEATURE_NAME, FEATURE_COORDINATES, GEOMETRY, FEATURE_PROPERTIES, SPECS, WHOLE_OBJECT ) as select $1:features[0]:properties:Name::string as feature_name ,$1:features[0]:geometry:coordinates::string as feature_coordinates ,$1:features[0]:geometry::string as geometry ,$1:features[0]:properties::string as feature_properties ,$1:crs:properties:name::string as specs ,$1 as whole_object from @trails_geojson (file_format => ff_json);



Remember that we've done a lot of cool things even though we still haven't loaded any of this data!!

We have left the data where it landed (when Camila downloaded it from her fitness-tracking watch). And we have layered structure into our queries using file formats and views. We're not saying this is a great way to engineer data, we're teaching you about all the tools in the leave-it-where-it-lands toolbox.

And again, does it feel a little kluge-y sometimes? Yes! But depending on the project team, and the setting, and the project deadline - these no-loading tools might save you from spending critical time on the wrong tasks.

So, let's keep pushing the limits of this leave-it-where-it-lands strategy and layer on a little more of what we need to meet our goals. 

Let's try to get the data from CHERRY_CREEK_TRAIL and DENVER_AREA_TRAILS to look enough alike that we can run some GeoSpatial functions on all 5 trails at one time!


https://geojson.io/#map=12.06/39.65341/-105.08187

https://clydedacruz.github.io/openstreetmap-wkt-playground/


Sonra
NOTE: You will need to switch your role to ACCOUNTADMIN to get the share added to your trial. 

//Openstreet Map Denver
// Give me the length of a Way
SELECT
ID,
ST_LENGTH(COORDINATES) AS LENGTH
FROM DENVER.V_OSM_DEN_WAY;

// List the number of nodes in a Way
SELECT
ID,
ST_NPOINTS(COORDINATES) AS NUM_OF_NODES
FROM DENVER.V_OSM_DEN_WAY;

// Give me the distance between two Ways
SELECT
 A.ID AS ID_1,
 B.ID AS ID_2,
 ST_DISTANCE(A.COORDINATES, B.COORDINATES) AS DISTANCE
FROM (SELECT
 ID,
 COORDINATES
FROM DENVER.V_OSM_DEN_WAY
WHERE ID = 705859567) AS A
INNER JOIN (SELECT
 ID,
 COORDINATES
FROM DENVER.V_OSM_DEN_WAY
WHERE ID = 705859570) AS B;

// Give me all amenities from education category in a radius of 2,000 metres from a point
SELECT
*
FROM DENVER.V_OSM_DEN_AMENITY_EDUCATION
WHERE ST_DWITHIN(ST_POINT(-1.049212522000000e+02,
    3.969829250000000e+01),COORDINATES,2000);

// Give me all food and beverage Shops in a radius of 2,000 metres from a point

SELECT
*
FROM DENVER.V_OSM_DEN_SHOP_FOOD_BEVERAGES  
WHERE ST_DWITHIN(ST_POINT(-1.049632800000000e+02,
    3.974338330000000e+01),COORDINATES,2000);

-- Melanie's Location into a 2 Variables (mc for melanies cafe)
set mc_lat='-104.97300245114094';
set mc_lng='39.76471253574085';

--Confluence Park into a Variable (loc for location)
set loc_lat='-105.00840763333615'; 
set loc_lng='39.754141917497826';

--Test your variables to see if they work with the Makepoint function
select st_makepoint($mc_lat,$mc_lng) as melanies_cafe_point;
select st_makepoint($loc_lat,$loc_lng) as confluent_park_point;

--use the variables to calculate the distance from 
--Melanie's Cafe to Confluent Park
select st_distance(
        st_makepoint($mc_lat,$mc_lng)
        ,st_makepoint($loc_lat,$loc_lng)
        ) as mc_to_cp;


set home_lng = '151.19314977286453';
set home_lat = '-33.787922152622585';

set bus_lat  = '-33.79262132726079';
set bus_lng = '151.19460939102035';

set train_lat = '-33.79767076522433';
set train_lng = '151.18088000717145'; 

--ST_MAKEPOINT( <longitude> , <latitude> )
select st_makepoint($home_lng,$home_lat) as home_point;
select st_makepoint($bus_lng,$bus_lat) as bus_point;

select st_distance(
        st_makepoint($home_lng,$home_lat)
        ,st_makepoint($bus_lng,$bus_lat)
        ) as home_to_bus;

select st_distance(
        st_makepoint($home_lng,$home_lat)
        ,st_makepoint($train_lng,$train_lat)
        ) as home_to_train;

select st_distance(
        st_makepoint($bus_lng,$bus_lat)
        ,st_makepoint($train_lng,$train_lat)
        ) as bus_to_train;



13,405,887.8641714


We've highlighted the changed parts in blue.


CREATE OR REPLACE FUNCTION distance_to_mc(lat_and_lng GEOGRAPHY)
  RETURNS FLOAT
  AS
  $$
   st_distance(
        st_makepoint('-104.97300245114094','39.76471253574085')
        ,lat_and_lng
        )
  $$
  ;
🥋 Now We Can Use it In Our Sonra Select

SELECT
 name
 ,cuisine
 ,distance_to_mc(coordinates) AS distance_from_melanies
 ,*
FROM  competition
ORDER by distance_from_melanies;

If you are new to coding, you may not know about something called "overloading" a function. Overloading sounds like a bad thing, but it's actually pretty cool. 

Basically, it means that you can have different ways of running the same function and Snowflake will figure out which way to run the UDF, based on what you send it. So if you send the UDF two numbers it will run our first version of the function and if you pass it one geography point, it will run the second version. 


This means we can run the function several different ways and they will all result in the same answer.  When speaking about a FUNCTION plus its ARGUMENTS we can refer to it as the FUNCTION SIGNATURE. 

-- Tattered Cover Bookstore McGregor Square
set tcb_lat='-104.9956203'; 
set tcb_lng='39.754874';

--this will run the first version of the UDF
select distance_to_mc($tcb_lat,$tcb_lng);

--this will run the second version of the UDF, bc it converts the coords 
--to a geography object before passing them into the function
select distance_to_mc(st_makepoint($tcb_lat,$tcb_lng));

--this will run the second version bc the Sonra Coordinates column
-- contains geography objects already
select name
, distance_to_mc(coordinates) as distance_to_melanies 
, ST_ASWKT(coordinates)
from SONRA_DENVER_CO_USA_FREE.DENVER.V_OSM_DEN_SHOP
where shop='books' 
and name like '%Tattered Cover%'
and addr_street like '%Wazee%';


A Materialized View is like a view that is frozen in place (more or less looks and acts like a table).

The big difference is that if some part of the underlying data changes,  Snowflake recognizes the need to refresh it, automatically.

People often choose to create a materialized view if they have a view with intensive logic that they query often but that does NOT change often.  We can't use a Materialized view on any of our trails data because you can't put a materialized view directly on top of staged data. 


select * from mels_smoothie_challenge_db.trails.cherry_creek_trail;
describe view mels_smoothie_challenge_db.trails.cherry_creek_trail;

select get_ddl('view', 'mels_smoothie_challenge_db.trails.cherry_creek_trail');
alter view mels_smoothie_challenge_db.trails.cherry_creek_trail
rename to mels_smoothie_challenge_db.trails.v_cherry_creek_trail;


create or replace external table mels_smoothie_challenge_db.trails.T_CHERRY_CREEK_TRAIL(
	POINT_ID number as ($1:sequence_1::number),
	TRAIL_NAME varchar(50) as  ($1:trail_name::varchar),
	LNG number(11,8) as ($1:latitude::number(11,8)),
	LAT number(11,8) as ($1:longitude::number(11,8)),
	COORD_PAIR varchar(50) as (lng::varchar||' '||lat::varchar)
) 
location= @mels_smoothie_challenge_db.trails.trails_parquet
auto_refresh = true
file_format = mels_smoothie_challenge_db.trails.ff_parquet;

create or replace external table mels_smoothie_challenge_db.trails.T_CHERRY_CREEK_TRAIL(
	POINT_ID number as ($1:sequence_1::number),
	TRAIL_NAME varchar(50) as  ($1:trail_name::varchar),
	LNG number(11,8) as ($1:latitude::number(11,8)),
	LAT number(11,8) as ($1:longitude::number(11,8)),
	COORD_PAIR varchar(50) as (lng::varchar||' '||lat::varchar)
) 
location= @mels_smoothie_challenge_db.trails.trails_parquet
auto_refresh = true
file_format = mels_smoothie_challenge_db.trails.ff_parquet;

select * from mels_smoothie_challenge_db.trails.T_CHERRY_CREEK_TRAIL;

create or replace materialized view SMV_CHERRY_CREEK_TRAIL
as select * from mels_smoothie_challenge_db.trails.T_CHERRY_CREEK_TRAIL;

select * from SMV_CHERRY_CREEK_TRAIL;


WORKSHOP 5

alter user LIUJCHEN set default_role = 'SYSADMIN';
alter user LIUJCHEN set default_warehouse = 'COMPUTE_WH';
alter user LIUJCHEN set default_namespace = 'UTIL_DB.PUBLIC';

create or replace TABLE AGS_GAME_AUDIENCE.RAW.GAME_LOGS (
	RAW_LOG VARIANT
);

NOTE: Notice one of the names has a dash and the other has an underscore. It's easy to get this mixed up. AWS bucket names cannot have underscores. Snowflake stage names cannot have dashes!!

select current_user();

select $1 from @uni_kishore/kickoff
(file_format => FF_JSON_LOGS);
