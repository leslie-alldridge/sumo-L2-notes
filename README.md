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

