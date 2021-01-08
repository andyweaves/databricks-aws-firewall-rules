# databricks-aws-firewall-rules

This repo contains some example firewall rules for [AWS Network Firewall](https://aws.amazon.com/network-firewall/), specifically designed to help organisations further secure their [Databricks](https://databricks.com/) environments against the threat of data exfiltration. 

To use these rules:

1) Set up the [AWS CLI](https://docs.aws.amazon.com/cli/)
2) Test that it's working by running the ```aws network-firewall list-firewalls``` command
3) Replace the <PLACEHOLDERS> in the 3 JSON files:
  
  
  
4) Use the [create-rule-group](https://docs.aws.amazon.com/cli/latest/reference/network-firewall/create-rule-group.html) command to create each rule group:

```aws network-firewall create-rule-group --rule-group-name Databricks-FQDNs --rule-group file://allow-list-fqdns.json --type STATEFUL --capacity 100```

```aws network-firewall create-rule-group --rule-group-name Databricks-IPs --rule-group file://allow-list-ips.json --type STATEFUL --capacity 100```

```aws network-firewall create-rule-group --rule-group-name Deny-Protocols --rule-group file://deny-list.json --type STATEFUL --capacity 100```
