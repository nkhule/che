# Add data node in a cluster
Update data nodes count in a ES cluster's environment tfvars file.

For e.g add a data node in a devx cluster

Edit tf-as-elasticsearch-5x/profiles/devx.tfvars file for a data_count value:

data_count="6"
