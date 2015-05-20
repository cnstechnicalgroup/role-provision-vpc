Role: cns.provision-vpc
========

Create / provision AWS VPC, subnets, and route-tables.

Requirements
------------

Nothing, it runs out of the box.

Role Variables
--------------

In the current version, you can specify the following variables:

| Name               | Default |                                                        |
|--------------------|---------|--------------------------------------------------------|
| aws_access_key     |   ---   | AWS Access Key for provision account                   |
| aws_secret_key     |   ---   | AWS Secret Key for provision account                   |
| region             |   ---   | AWS region (e.g. eu-west-1)                            |
| vpc_cidr_block     |   ---   | VPC CIDR block (e.g. 10.0.1.0/16)                      |
| vpc_resource_tags  |   ---   | Resource tags to assign to VPC (see example)           |
| subnets            |   ---   | Subnets dict describing all VPC subnets (see example)  |
| route_tables       |   ---   | Route table dict describing all VPC route tables       |

* All fields required

Dependencies
------------

This package has no dependencies.

License
-------

GPLv2

Author Information
------------------

Created by Sam Morrison [@samcns](https://www.twitter.com/samcns)

Examples
--------

```yaml
---
- name: cns.ec2 role test
  hosts: all
  roles:
    - { role: cns.ec2, instance: "{{ instance_ec2 }}" }
```

```yaml
# group_vars/all.yml
---
aws_access_key: PROVISIONACCOUNT_ACCESS_KEY
aws_secret_key: PROVISIONACCOUNT_SECRET_KEY
owner: {{ client_name }}
region: {{ region }}

# Refer to `Client Networks` document for next CIDR block.
# Update the `Client Networks` document with selected CIDR block.
cidr_block: 10.n.0.0/16
resource_tags: { "Name" : "{{ client_name }}-prod-10-n-0-0"  }

# Define client's subnets here
# Create one block, separated by PROJECTNAME header, for each project owned by `owner`.

subnets:
##################################
# PROJECTNAME Production Subnets #
##################################
  - cidr: 10.n.1.0/24
    az: eu-west-1c
    resource_tags: { "Name" : "PROJECTNAME-elb-prod", "Project":"PROJECTNAME", "Tier" : "elb", "Mode" : "prod" }
  - cidr: 10.n.2.0/24
    az: eu-west-1c
    resource_tags: { "Name" : "PROJECTNAME-web-prod", "Project":"PROJECTNAME", "Tier" : "web", "Mode" : "prod" }
  - cidr: 10.n.3.0/24
    az: eu-west-1c
    resource_tags: { "Name" : "PROJECTNAME-app-prod", "Project":"PROJECTNAME", "Tier" : "app", "Mode" : "prod" }
  - cidr: 10.n.4.0/24
    az: eu-west-1c
    resource_tags: { "Name" : "PROJECTNAME-rdsa-prod", "Project":"PROJECTNAME", "Tier" : "rds-a", "Mode" : "prod" }
  - cidr: 10.n.5.0/24
    az: eu-west-1b
    resource_tags: { "Name" : "PROJECTNAME-rdsb-prod", "Project":"PROJECTNAME", "Tier" : "rds-b", "Mode" : "prod" }
  - cidr: 10.n.6.0/24
    az: eu-west-1c
    resource_tags: { "Name" : "PROJECTNAME-rdsc-prod", "Project":"PROJECTNAME", "Tier" : "rds-c", "Mode" : "prod" }

# Define client's route_tables here
# Create one block, separated by PROJECTNAME header, for each project owned by `owner`.
# Customize routes as needed.

route_tables:
######################################
# PROJECTNAME Production Route Table #
######################################
  # ELB subnet route table
  - subnets:
      - 10.10.n.0/24
    routes:
      - dest: 0.0.0.0/0
        gw: igw
    resource_tags: { "Name" : "PROJECTNAME-elb-prod-10-10-n-0", "Project":"PROJECTNAME", "Tier" : "elb", "Mode" : "prod" }
  # Web subnet route table
  - subnets:
      - 10.10.n.0/24
    routes:
      - dest: 0.0.0.0/0
        gw: igw
    resource_tags: { "Name" : "PROJECTNAME-elb-prod-10-10-n-0", "Project":"PROJECTNAME", "Tier" : "elb", "Mode" : "prod" }
```
