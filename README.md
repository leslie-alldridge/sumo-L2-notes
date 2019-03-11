# sumo-L2-notes

Rules for SumoLogic Keyword Search:
https://help.sumologic.com/05Search/Get-Started-with-Search/How-to-Build-a-Search/Keyword-Search-Expressions#Case_sensitive_keyword_search

QUIZ: True or False

Keywords are case-sensitive F

AND is implicit and OR is explicit T

Keywords and metadata can use wildcards for Live tail F

### Calculating Average
avg(<numerical_field>) [as <field>]
e.g. 
    | avg(total_bytes) as average_size 

### View in JSON
_sourceCategory=Labs/AWS/CloudTrail

| json auto

### No drop 
https://help.sumologic.com/05Search/Search-Query-Language/01-Parse-Operators/Parse-nodrop-option

when you're using no drop, if a log doesn't match the query it won't be dropped. It will still come through but have a blank field. Otherwise, without nodrop you can be losing many logs. 

### Parsing operators 
https://help.sumologic.com/05Search/Search-Cheat-Sheets/Log-Operators-Cheat-Sheet

QUIZ: True or False

csv, json, split, keyvalue are all parsing operators. T

Once a field has been parsed, it cannot be parsed any further. F

Fields parsed by the Field Extraction Rules are available in the Field Browser. T

### Parent and sub queries 
https://help.sumologic.com/05Search/Subqueries

### Create map and filter to a specific region

_sourceCategory=Labs/Apache/Access

| parse "* - -" as client_ip

| lookup latitude, longitude, country_code from geo://location on ip=client_ip

| count by latitude, longitude, country_code

| where (country_code matches "US")

| sort _count

### Challenges 
1. Count Number of Messages by Method

_sourceCategory=Labs/Apache/Access

| timeslice 1m
| count by _timeslice,method

2. Count Number of 404 Error Messages by Method

_sourceCategory=Labs/Apache/Access AND 404

| timeslice 1m
| count by _timeslice,method

3. Count Successes versus Failures

_sourceCategory=Labs/Apache/Access

| parse "HTTP/1.1\" * " as status_code

| if(status_code=200, 1, 0) as successes

| if(status_code=404, 1, 0) as client_errors  

| sum(successes) as success_cnt, sum(client_errors) as client_errors_cnt

### Format Results

_sourceCategory=Labs/Apache/Access

| parse "HTTP/1.1\" * " as status_code

| timeslice by 1m

| count by _timeslice, status_code

| transpose row _timeslice column status_code

Transposing results helps to get x and y axis to interpret results

### Transposed results with no 200 or 304 status codes 

_sourceCategory=Labs/Apache/Access

| parse "HTTP/1.1\" * " as status_code

| timeslice by 1m

| count by _timeslice, status_code
| where !(status_code=200 or status_code=304)
| transpose row _timeslice column status_code 

### Changes and Moving Averages

https://support.sumologic.com/hc/en-us/community/posts/115007137347-Plot-Error-Counts-against-Rolling-Averages

Bar and line chart combo example 

### Logcompare
Allows you to compare log activity from two different time periods, providing you insight on how your current time compares to a baseline. 


### Indentify out of the ordinary events
_sourceCategory=Labs/Apache/Access and status_code=404

|timeslice 1m
|count (status_code) as error_count by _timeslice
|outlier error_count window=2, consecutive=2, threshold=2, direction=+-

Can use predict instead of outlier to predict future events.

### Checking state by IP

_sourceCategory=Labs/ecommark

| parse regex "(?<ip>[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3})" nodrop

| transaction on ip

with "*/confirmation*" as confirmation,

with "*Order shipped*" as ordershipped,

with "*/cart*" as cart,

with "*/shippingInfo*" as shippinginfo,

with "*/billinginfo*" as billinginfo

results by flow

| count by fromstate, tostate

### Logs to metrics 

Extracts metrics from logs. Logs can generate time-series charts on their own, this is not a necessary step. However, extracting metrics from logs reduces the need to re-query unstructured data to generate a chart. The chart runtime, performance, retention, and alerting capabilities are improved. 

For Metrics and Dimensions, you can select any field by which you want to slice and dice the data. Just remember, only one Metric can be selected and must be a numerical value. You may also use the number of resulting messages from the Parse Expression query as the Metric, which we’ll show in our example.

Timeshift is very handy for comparing across multiple time periods 

Identifying an outlier on a rate of change is a better indicator of an impending problem.  

### Relating metrics and logs
Metrics = identify symptoms in your environment (what is going on)
Relevant logs = identify the cause (why is this happening)

### Alerts
Meaningful alerts guidelines https://www.sumologic.com/blog/log-management-analysis/2-key-principles-creating-meaningful-alerts/

### Collector Considerations
Consider one collector for:
- Running a very high bandwidth network with high logging levels.
- A central collection point for many sources

Consider multiple collectors for:
- You expect the combined number of files coming into one Collector to exceed 500
- Your hardware has memory or cpu limitations
- You expect combined logging traffic for one Collector to be higher than 15,000 events per second.
- Your network clusters or regions are geographically separated.
- You prefer to install many collectors, for example, one per machine to collect local files.

Design your deployment
https://help.sumologic.com/03Send-Data/01-Design-Your-Deployment

### Collector installation
Installed on hosts and sends log data via https directly to sumo. 