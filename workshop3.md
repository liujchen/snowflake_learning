
It is good to be aware that another name for "THE SNOWFLAKE database" is "the Account Usage Share." At one time, this question was on the SnowPro Core Certification exam. 

Notice that the database called SNOWFLAKE_SAMPLE_DATA is coming from an account called SFSALESSHARED (followed by a schema that will vary by region).

This account named their outbound share "SAMPLE_DATA." 

It is only in our account that this data appears under the name SNOWFLAKE_SAMPLE_DATA.

grant imported privileges
on database DATABASE SNOWFLAKE_SAMPLE_DATA
to role SYSADMIN;

The real value of consuming shared data is:

Someone else will maintain it over time and keep it fresh
Someone else will pay to store it
You will only pay to query it