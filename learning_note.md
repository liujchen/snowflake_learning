
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