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


# Add data node in a cluster
Update data nodes count in a ES cluster's environment tfvars file.

For e.g add a data node in a devx cluster

Edit tf-as-elasticsearch-5x/profiles/devx.tfvars file for a data_count value:

data_count="6"

#### IMPORTANT- Following things needs to be considered before adding nodes in a cluster-
## Disable shard re-balancing in a cluster for newly added node
By default , elasticsearch does shard re-balancing across the nodes. It means whenever new node is added elasticsearch starts re-balancing of shards in a cluster, to make shards equally distributed across the data-nodes.  

If we are having high load at the time of adding node, we can disable the shard re-balancing by using following api from any of the cluster nodes-

curl -XPUT 'localhost:9200/_cluster/settings' -d'
{
    "transient" : {
        "cluster.routing.rebalance.enable" : "none"
    }
}'

Settings updated can either be persistent (applied cross restarts) or transient (will not survive a full cluster restart).

More about cluster update settings can be found here- https://www.elastic.co/guide/en/elasticsearch/reference/current/cluster-update-settings.html

## Enable default shard rebalancing-
Once you see cluster is back to normal load or minimal load, you can enable re-balancing of shards in a cluster for newly added node by hitting the api from any of the cluster nodes as follows -

curl -XPUT 'localhost:9200/_cluster/settings' -d'
{
    "transient" : {
        "cluster.routing.rebalance.enable" : null
    }
}'
(This will reset to default i.e. rebalance "all")

OR

curl -XPUT 'localhost:9200/_cluster/settings' -d'
{
    "transient" : {
        "cluster.routing.rebalance.enable" : "all"
    }
}'

(Note- If we do not enable the cluster re-balance, shards will not be balanced in a cluster. Newly added nodes will not contain shards for old indices.)

## Check Number of nodes in a cluster-
#### ES API -
curl http://localhost:9200/_cat/nodes?v

#### Grafana dashboard- Analytics Elasticsearch-5.x
Check graphana chart from dashboard- Active nodes

## Check cluster settings-
#### ES API -
curl localhost:9200/_cluster/settings?pretty

## Check shards allocated to nodes- 
#### ES API-
curl localhost:9200/_cat/allocation?v

## Check current shard re-allocation-
#### ES API-
curl localhost:9200/_cluster/health?pretty

#### Grafana dashboard- Analytics Elasticsearch-5.x
Check chart- #Relocated shards  

## Cluster monitoring using grafana-
#### Grafana dashboard- Analytics Elasticsearch-5.x
Check charts- 
1. Cluster status(0- Green, 1- Yellow, 2- Red)
1. CPU Used percent
1. JVM Heap Used %
1. JVM GC Time
1. Disk IOPS per node
1. Segment count per minutes / node 
1. Thread Pool Rejected
    



