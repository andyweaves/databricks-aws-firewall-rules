# databricks-aws-firewall-rules

This repo contains some example firewall rules for [AWS Network Firewall](https://aws.amazon.com/network-firewall/), specifically designed to help organisations further secure their [Databricks](https://databricks.com/) environments against the threat of data exfiltration. 

To use these rules:

1) Set up the [AWS CLI](https://docs.aws.amazon.com/cli/)
2) Test that it's working by running the ```aws network-firewall list-firewalls``` command
3) Replace the capitalised ```"<PLACEHOLDERS>"``` in the 3 JSON files with relevant URLs and CIDR ranges for your Databricks environment:
  
    * ```"<SPOKE VPC CIDR RANGE>"``` - The CIDR range for your [Databricks](https://databricks.com/) [Customer Managed VPC](https://docs.databricks.com/administration-guide/cloud-configurations/aws/customer-managed-vpc.html)
    * ```"<HUB VPC CIDR RANGE>"``` - The CIDR range for the VPC in which your [AWS Network Firewall](https://aws.amazon.com/network-firewall/) sits
    * ```"<DATABRICKS MANAGED HIVE METASTORE IP ADDRESS>"``` - (Optional) The current IP address of the [Databricks](https://databricks.com/) Managed Hive Metastore for your region as per [RDS addresses for legacy Hive metastore](https://docs.databricks.com/en/resources/supported-regions.html#rds-addresses-for-legacy-hive-metastore). Alternatively customers can use [their own Hive Metastore](https://docs.databricks.com/data/metastores/external-hive-metastore.html) or [AWS Glue](https://docs.databricks.com/data/metastores/aws-glue-metastore.html) if they prefer
4) Use the [create-rule-group](https://docs.aws.amazon.com/cli/latest/reference/network-firewall/create-rule-group.html) command to create each rule group:

  > ```aws network-firewall create-rule-group --rule-group-name Databricks-FQDNs --rule-group file://allow-list-fqdns.json --type STATEFUL --capacity 100```

  > ```aws network-firewall create-rule-group --rule-group-name Databricks-IPs --rule-group file://allow-list-ips.json --type STATEFUL --capacity 100```

  > ```aws network-firewall create-rule-group --rule-group-name Deny-Protocols --rule-group file://deny-list.json --type STATEFUL --capacity 100```

Happy firewalling!
