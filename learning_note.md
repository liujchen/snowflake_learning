
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

##Copy data into local table
copy into ags_game_audience.raw.game_logs
from @uni_kishore/kickoff
file_format = (format_name = FF_JSON_LOGS);


Did you notice that we did not write out the file name in the FROM line? This is because there is only one file in the kickoff folder. A COPY INTO statement like the one shown above will load EVERY file in the folder if more than one file is there, and the file name is not specified. This will come in very handy later in the course. 

There are other ways to specify what files should be loaded and Snowflake gives you a lot of tools to further specify what will be loaded, but for now accept the general rule that by not naming the file, you are asking SNOWFLAKE to attempt to load ALL files the stage or stage/folder location. 



select RAW_LOG:agent::text as AGENT,
RAW_LOG:user_event::text as USER_EVENT,
RAW_LOG:user_login::text as user_login,
RAW_LOG:datetime_iso8601::TIMESTAMP_NTZ as datetime,
*
from  game_logs;

## TIMESTAMP_NTZ (no time zone)
You'll need to know a few more initialisms for this lesson. NTZ means "No Time Zone." LTZ means "Local Time Zone." 

Pacific Standard Time
Time zone in Los Angeles, CA, USA (GMT-8)
Thursday, 30 November 2023, 3:29 am

CREATE OR REPLACE FUNCTION GET_CURRENT_TIMEZONE()
RETURNS VARCHAR
LANGUAGE JAVASCRIPT
AS 
$$
  const timezone = Intl.DateTimeFormat().resolvedOptions().timeZone;
  return timezone;
$$
;

select GET_CURRENT_TIMEZONE();
America/Los_Angeles

2023-11-30 03:30:53.828 -0800  (American time)

Kishore runs the command SELECT current_timestamp(); in a worksheet (in October) and sees -0600 as part of the results.

-0600 is the same thing as UTC-6.

This means Kishore's Snowflake session is currently using the Denver time zone. 

What time zone is your Snowflake Trial Account using?  Run the current_timestamp() command to find out. Our guess is that you'll see either UTC-7 (-0700) or UTC-8 (-0800) depending on the time of year it is (daylight savings time).

We can guess this because all Snowflake Trial Account use "America/Los_Angeles" as the default. This may be because Snowflake was founded in San Mateo, California, USA. 

--what time zone is your account(and/or session) currently set to? Is it -0700?
select current_timestamp();

--worksheets are sometimes called sessions -- we'll be changing the worksheet time zone
alter session set timezone = 'UTC';
select current_timestamp();

--how did the time differ after changing the time zone for the worksheet?
alter session set timezone = 'Africa/Nairobi';
select current_timestamp();

alter session set timezone = 'Pacific/Funafuti';
select current_timestamp();

alter session set timezone = 'Asia/Shanghai';
select current_timestamp();

--show the account parameter called timezone
show parameters like 'timezone';

## Day 1 Dec 23

You may see the term "schema-on-read" noted in some articles and posts as a great benefit Snowflake is able to provide. In a sense, you are seeing schema-on-read in action, here, because we can load anything we want into a VARIANT column, and parse it out (read it) differently over time. The change in the columns included (the schema difference in the two data loads) doesn't break anything because we are reading the structure after the load, not before or during the data load. 


select * from logs
WHERE USER_LOGIN ilike '%prajina%';


select GRADER(step, (actual = expected), actual, expected, description) as graded_results from
(
SELECT
   'DNGW02' as step
   ,( select sum(tally) from(
        select (count(*) * -1) as tally
        from ags_game_audience.raw.logs 
        union all
        select count(*) as tally
        from ags_game_audience.raw.game_logs)     
     ) as actual
   ,250 as expected
   ,'View is filtered' as description
); 


When we created our LOG view, we parsed out the JSON into the separate columns. Parsing the data could be considered a transformation. 

*** I like below comments ***
In many organizations, a Data Engineer is given access to extracted data, and told what the end goals are (the final, transformed state). Then, it is within their power and discretion to decide what steps they will follow to get there. 

These choices are called "design" and the structures and processes that result are called the "architecture." In this workshop we'll give you hands-on experience with the components, but design and architecture decisions are something it takes years to learn and we don't attempt to teach them in this beginner course. 

Data Engineers often perform a series of ETL steps and so they have different "layers" where data is expected to have reached certain levels of refinement or transformation. In this workshop we'll have named our layers: 

RAW
ENHANCED ==>Enrichment :) 
CURATED


--Look up Kishore and Prajina's Time Zone in the IPInfo share using his headset's IP Address with the PARSE_IP function.
select start_ip, end_ip, start_ip_int, end_ip_int, city, region, country, timezone
from IPINFO_GEOLOC.demo.location
where parse_ip('100.41.16.160', 'inet'):ipv4 --Kishore's Headset's IP Address
BETWEEN start_ip_int AND end_ip_int;



select parse_ip('100.41.16.160','inet');

select parse_ip('107.217.231.17','inet'):host;

select parse_ip('107.217.231.17','inet'):family;


--Look up Kishore and Prajina's Time Zone in the IPInfo share using his headset's IP Address with the PARSE_IP function.
select start_ip, end_ip, start_ip_int, end_ip_int, city, region, country, timezone
from IPINFO_GEOLOC.demo.location
where parse_ip('100.41.16.160', 'inet'):ipv4 --Kishore's Headset's IP Address
BETWEEN start_ip_int AND end_ip_int;



select logs.*
       , loc.city
       , loc.region
       , loc.country
       , loc.timezone
from AGS_GAME_AUDIENCE.RAW.LOGS logs
join IPINFO_GEOLOC.demo.location loc
where parse_ip(logs.ip_address, 'inet'):ipv4 
BETWEEN start_ip_int AND end_ip_int;



Looking up time zones using the IPInfo Geolocation share is going to be an important part of Kishore's data pipeline. Even though IPInfo is giving away this data sample for free, anyone querying it will still pay Snowflake for the use of Warehouse time. 

Of course, as a Trial Account User, you're not spending real money. Right now you're simply using up Free Trial Credits, but as a Data Engineer, you have to be able to write queries that don't work as hard, when they could get the same result set by working smart!

After running a query, especially one that he plans to run often, Kishore will need to make sure it will be "performant." He can do that by examining the Query Profile of any command he runs.  Let's run some commands that might not perform all that well and then look at the query profiles for each. 


The TO_JOIN_KEY function reduces the IP Down to an integer that is helpful for joining with a range of rows that might match our IP Address.
The TO_INT function converts IP Addresses to integers so we don't have to try to compare them as strings! 


SELECT logs.ip_address
, logs.user_login
, logs.user_event
, logs.datetime_iso8601
, city
, region
, country
, timezone 
from AGS_GAME_AUDIENCE.RAW.LOGS logs
JOIN IPINFO_GEOLOC.demo.location loc 
ON IPINFO_GEOLOC.public.TO_JOIN_KEY(logs.ip_address) = loc.join_key
AND IPINFO_GEOLOC.public.TO_INT(logs.ip_address) 
BETWEEN start_ip_int AND end_ip_int;


create table ags_game_audience.raw.time_of_day_lu
(  hour number
   ,tod_name varchar(25)
);

--insert statement to add all 24 rows to the table
insert into time_of_day_lu
values
(6,'Early morning'),
(7,'Early morning'),
(8,'Early morning'),
(9,'Mid-morning'),
(10,'Mid-morning'),
(11,'Late morning'),
(12,'Late morning'),
(13,'Early afternoon'),
(14,'Early afternoon'),
(15,'Mid-afternoon'),
(16,'Mid-afternoon'),
(17,'Late afternoon'),
(18,'Late afternoon'),
(19,'Early evening'),
(20,'Early evening'),
(21,'Late evening'),
(22,'Late evening'),
(23,'Late evening'),
(0,'Late at night'),
(1,'Late at night'),
(2,'Late at night'),
(3,'Toward morning'),
(4,'Toward morning'),
(5,'Toward morning');


create task load_logs_enhanced
    warehouse = 'COMPUTE_WH'
    schedule = '5 minute'
    -- <session_parameter> = <value> [ , <session_parameter> = <value> ... ] 
    -- user_task_timeout_ms = <num>
    -- copy grants
    -- comment = '<comment>'
    -- after <string>
  -- when <boolean_expr>
  as
    select 'hello';


create or replace task load_logs_enhanced
    warehouse = 'COMPUTE_WH'
    schedule = '5 minute'
    -- <session_parameter> = <value> [ , <session_parameter> = <value> ... ] 
    -- user_task_timeout_ms = <num>
    -- copy grants
    -- comment = '<comment>'
    -- after <string>
  -- when <boolean_expr>
  as
    create or replace table ags_game_audience.enhanced.logs_enhanced as(
SELECT logs.ip_address 
, logs.user_login as GAMER_NAME
, logs.user_event as GAME_EVENT_NAME
, logs.datetime_iso8601 as GAME_EVENT_UTC
, city
, region
, country
, timezone as GAMER_LTZ_NAME
, convert_timezone('UTC',timezone,logs.datetime_iso8601) as game_events_ltz
, dayname(game_events_ltz) as DOW_NAME
, TOD_NAME 
from AGS_GAME_AUDIENCE.RAW.LOGS logs
JOIN IPINFO_GEOLOC.demo.location loc 
ON IPINFO_GEOLOC.public.TO_JOIN_KEY(logs.ip_address) = loc.join_key
AND IPINFO_GEOLOC.public.TO_INT(logs.ip_address) 
BETWEEN start_ip_int AND end_ip_int
JOIN ags_game_audience.raw.time_of_day_lu lu
ON Hour(game_events_ltz) = lu.hour
)
;


Snowflake has some built in help for IDEMPOTENCY, especially when the file is first picked up from the stage, and we'll talk more about how Snowflake can help with that, but right now we'll focus on making this particular step IDEMPOTENT.  


Another method that was used in the early days of data warehousing was a database replace. A whole new warehouse would be built each night and when it was complete, the old one would be given an archival name, and the new one would be given the standard name.

Snowflake has the ability to CLONE databases, schemas, tables and more which means this sort of switching out can be done very easily but this isn't really a version of the old-school rebuild and replace. It's pretty significantly different. That's okay, because almost no orgs rebuild their warehouses from scratch each night anymore.

On the subject of Snowflake's cloning capabilities, some organizations use cloning to create test and dev copies of entire databases, schemas or just a few of the objects within them.  So, after using modern update methods, you could delete your test and dev instances each night and replace them with fresh clones of your production warehouse. 

Cloning is a very powerful tool, doesn't cost much, and doesn't take long. Cloning is more efficient than copying (you can read more about cloning at docs.snowflake.com).  

We can also use cloning to make back ups of things we feel more comfortable having a safe copy of while we are in heavy development. Let's make a back up copy of our LOGS_ENHANCED table.  We're about to start testing some complex logic and we might want to look back at this table, later. 


##Merge##


MERGE INTO ENHANCED.LOGS_ENHANCED e
USING (SELECT logs.ip_address 
, logs.user_login as GAMER_NAME
, logs.user_event as GAME_EVENT_NAME
, logs.datetime_iso8601 as GAME_EVENT_UTC
, city
, region
, country
, timezone as GAMER_LTZ_NAME
, CONVERT_TIMEZONE( 'UTC',timezone,logs.datetime_iso8601) as game_event_ltz
, DAYNAME(game_event_ltz) as DOW_NAME
, TOD_NAME
from ags_game_audience.raw.LOGS logs
JOIN ipinfo_geoloc.demo.location loc 
ON ipinfo_geoloc.public.TO_JOIN_KEY(logs.ip_address) = loc.join_key
AND ipinfo_geoloc.public.TO_INT(logs.ip_address) 
BETWEEN start_ip_int AND end_ip_int
JOIN ags_game_audience.raw.TIME_OF_DAY_LU tod
ON HOUR(game_event_ltz) = tod.hour) r
ON r.GAMER_NAME = e.GAMER_NAME
and r.GAMER_NAME = e.GAMER_NAME
and r.GAME_EVENT_NAME = e.GAME_EVENT_NAME
WHEN NOT MATCHED THEN
insert(IP_ADDRESS, GAMER_NAME, GAME_EVENT_NAME, GAME_EVENT_UTC, CITY, REGION, COUNTRY, GAMER_LTZ_NAME, GAME_EVENT_LTZ, DOW_NAME, TOD_NAME)
values(IP_ADDRESS, GAMER_NAME, GAME_EVENT_NAME, GAME_EVENT_UTC, CITY, REGION, COUNTRY, GAMER_LTZ_NAME, GAME_EVENT_LTZ, DOW_NAME, TOD_NAME)


As Desmond Tutu famously said, The way to eat an elephant is one bite at a time. 

select GRADER(step, (actual = expected), actual, expected, description) as graded_results from
(
SELECT
'DNGW04' as step
 ,( select count(*)/iff (count(*) = 0, 1, count(*))
  from table(ags_game_audience.information_schema.task_history
              (task_name=>'LOAD_LOGS_ENHANCED'))) as actual
 ,1 as expected
 ,'Task exists and has been run at least once' as description 
 ); 

By "data pipeline" we mean:

A series of steps...
that move data from one place to another...
in a repeated way.




 📓 Idempotent COPY INTO
So, did you notice that the COPY INTO is smart enough to know which files it already loaded and it doesn't load the same file, twice?

Snowflake is designed like this to help you. Without any special effort on your part, you have a process that doesn't double-load files.  In other words, it automatically helps you keep your processes IDEMPOTENT.

But, what if, for some crazy reason, you wanted to double-load your files? 

You could add a FORCE=TRUE; as the last line of your COPY INTO statement and then you would double the number of rows in your table. 

Then, what if you wanted to start over and load just one copy of each file?

You could TRUNCATE TABLE PIPELINE_LOGS; , set FORCE=FALSE and run your COPY INTO again. 

 

The COPY INTO is very smart, which makes it useful and efficient!! We aren't going to use the FORCE command in this workshop. We aren't going to truncate and reload to prove the stage and COPY INTO are colluding in your favor (they really do!), but we wanted you to know they are available to you for special situations. 