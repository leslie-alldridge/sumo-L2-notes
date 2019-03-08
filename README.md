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