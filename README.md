# databricks-aws-firewall-rules

This repo contains some example firewall rules for [AWS Network Firewall](https://aws.amazon.com/network-firewall/), specifically designed to help organisations further secure their [Databricks](https://databricks.com/) environments against the threat of data exfiltration. 

> [!WARNING]
> You are ultimately responsible for the security of your environment, and for ensuring that the firewall rules that you implement meet your requirements. 

To use these rules:

1) Set up the [AWS CLI](https://docs.aws.amazon.com/cli/)
2) Test that it's working by running the ```aws network-firewall list-firewalls``` command
3) Replace the capitalised ```"<PLACEHOLDERS>"``` in the 3 JSON files with relevant URLs and CIDR ranges for your Databricks environment:
  
    * ```"<SPOKE VPC CIDR RANGE>"``` - The CIDR range for your [Databricks](https://databricks.com/) [Customer Managed VPC](https://docs.databricks.com/administration-guide/cloud-configurations/aws/customer-managed-vpc.html)
    * ```"<HUB VPC CIDR RANGE>"``` - The CIDR range for the VPC in which your [AWS Network Firewall](https://aws.amazon.com/network-firewall/) sits
    * ```"<DATABRICKS MANAGED HIVE METASTORE IP ADDRESS>"``` - (Optional) The **current IP address** of the [Databricks](https://databricks.com/) managed Hive Metastore for your region as per [RDS addresses for legacy Hive metastore](https://docs.databricks.com/en/resources/supported-regions.html#rds-addresses-for-legacy-hive-metastore). Alternatively customers can use [their own Hive Metastore (legacy)](https://docs.databricks.com/data/metastores/external-hive-metastore.html) or [AWS Glue (legacy)](https://docs.databricks.com/data/metastores/aws-glue-metastore.html).
  
> [!CAUTION]
> **The IP Address for the Databricks managed Hive Metastore is not static and is therefore subject to change**. Databricks recommends the use of [Unity Catalog](https://docs.databricks.com/en/data-governance/unity-catalog/index.html), the traffic for which can be routed over [AWS PrivateLink](https://docs.databricks.com/en/security/network/classic/privatelink.html). If the Databricks Managed Hive Metastore must be used, you will need to ensure that you have a way to detect and update the IP address in the event of it changing. Terraform is one potential solution for this. Please see our [SRA project](https://github.com/databricks/terraform-databricks-sra) for a built-in example. Please also ask AWS to add support for FQDN rules for more than just HTTP/S traffic to [AWS Network Firewall](https://aws.amazon.com/network-firewall/).
>  Alternatively, if the Hive Metastore is not needed, you could use Apache Derby as a non-persistent metastore, that only retains the objects persisted to it for as long as the cluster or SQL warehouse is online. In order to configure a cluster or SQL warehouse to use Apache Derby, please use the following configuration:

```
spark.hadoop.javax.jdo.option.ConnectionUserName admin
spark.hadoop.javax.jdo.option.ConnectionURL jdbc:derby:memory:myInMemDB;create=true
spark.hadoop.javax.jdo.option.ConnectionDriverName org.apache.derby.jdbc.EmbeddedDriver
```

4) Use the [create-rule-group](https://docs.aws.amazon.com/cli/latest/reference/network-firewall/create-rule-group.html) command to create each rule group:

  > ```aws network-firewall create-rule-group --rule-group-name Databricks-FQDNs --rule-group file://allow-list-fqdns.json --type STATEFUL --capacity 100```

  > ```aws network-firewall create-rule-group --rule-group-name Databricks-IPs --rule-group file://allow-list-ips.json --type STATEFUL --capacity 100```

  > ```aws network-firewall create-rule-group --rule-group-name Deny-Protocols --rule-group file://deny-list.json --type STATEFUL --capacity 100```

Happy firewalling!
