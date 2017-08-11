# Add data node in a cluster
Update data nodes count in a ES cluster's environment tfvars file.

For e.g add a data node in a devx cluster

Edit tf-as-elasticsearch-5x/profiles/devx.tfvars file for a data_count value:

data_count="6"

# ES Query performance
You can use siege tool for query performance test.
Siege installation and configuration document can be found here -
http://techieroop.com/siege-a-simple-load-testing-and-benchmarking-tool/#.Vas85flF76A

Run query tests on elasticsearch-
1. Add query in siege urls.txt. Path for file can be found using following command- 
    $siege --config   
   Default path-/usr/local/etc/urls.txt

for e.g. query -

http://ES-node-IP:9200/search-2f65f08d-36af-4753-b169-eda8f8a6f0ab/_search POST {"size": 0,"query": {"bool": {"must": [ {"match_all": {}},{"range": {"received_ts": {"from": 1429632058670,"to": 1431703651825,"include_lower": true,"include_upper": true}}}],"must_not": [{"exists": {"field" : "policy_violation"}}],"should": [{"terms": {"api_version_id": ["62495", "62497", "102486", "102485", "98168", "99259", "127144", "127143", "144312", "147422", "57213", "60740", "88459", "88458", "88457", "192382", "147419", "192383", "49681", "192385", "192386"],"_name" : "visible_api_version_id_filter"}},{"terms": {"api_version_id": ["192382"]}}]}},"_source" : false,"aggregations" : {"time_series_5m" : {"date_histogram" : {"field" : "@timestamp","interval" : "5m","min_doc_count" : 1},"aggregations" : {"status_code" : {"terms" : {"field" : "status_code","size" : 10,"min_doc_count" : 1,"value_type" : "string","order" : {"_count" : "desc"}}}}}}}

You can add multiple queries in file, to run multiple queries.

2. Run test using siege-

siege -c30 -t 50m -m test1

##### -c concurrent=NUM CONCURRENT users, 
##### -t –time=NUMm TIME based testing where “m” is the modifier S, M, or H no space between NUM and “m”, ex: –time=1H, 1 hour test.
##### -m –mark=”text” MARK, mark the log file with a string separator




