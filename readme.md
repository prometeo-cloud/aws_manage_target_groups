# aws_manage_target_groups

## Description:

Manages AWS ELB Target Groups as follows:

- Running the role will create the Target Groups defined in `aws_target_groups` when the variable `arg_action` is not defined or is defined and set to `create`
- Running the role will delete the Target Groups  defined in `aws_target_groups` when the variable `arg_action` is defined and is set to `delete`

For Ansible 2.5 or greater, this role requires Boto3 to be installed as follows:

`sudo pip install boto3`

or:

```yaml
- name: Install boto3
  easy_install:
    name: boto3
    state: present
```

## Dependencies

This role uses the AWS cli.  It therefore requires AWS credentials as environment variables or in a configuration file (see [here](https://docs.aws.amazon.com/cli/latest/topic/config-vars.html#cli-aws-help-config-vars)).  The role `aws_credentials` will create a config file for use with the AWS cli.

## Behaviour:

**Feature:** Create AWS ELB Target Groups  
- **Given** valid AWS credentials
- **Given** existing VPCs
- **When** the script is executed when `arg_action` is not defined or is defined and not set to `delete`
- **Then** the Target Groups are created
- **When** the script is executed when `arg_action` is defined and is set to `delete`
- **Then** the Target Groups are deleted if they are not associated with another resource 

## Configuration:

Common variables used by this role:

| Variable | Description | Example |
|-----|-----|-----|
| **aws_region** | AWS region | See [AWS regions](http://docs.aws.amazon.com/general/latest/gr/rande.html#ec2_region) |
| **aws_access_key** | AWS access key | AKIAIOSFODNN7EXAMPLE |
| **aws_secret_key** | AWS secret key | wJalrXUtnFEMI/K7MDENG/bPxRfiCYEXAMPLEKEY |
| **resource_tags** | List of resource tags | see usage |

Variables specific to this role:

| Variable | Description | Example |
|-----|-----|-----|
| **aws_target_groups** | List of Target Groups to create. | see usage |

## Usage:

How to invoke the role from a playbook (create Target Groups):

```yaml
- name: Create AWS ELB Target Groups
  include_role:
    name: "aws_manage_target_groups"
  vars:
    aws_access_key: AKIAIOSFODNN7EXAMPLE
    aws_secret_key: wJalrXUtnFEMI/K7MDENG/bPxRfiCYEXAMPLEKEY
    aws_region: eu-west-2  # London
    resource_tags:
      Name: "{{ resource_name }}"   # Set to upper case aws_target_groups.name
      Owner: "Acme Co."

    aws_target_groups:
      - name: "test-master-a-tg"
        vpc_name: "Test B"
        protocol: "tcp"
        port: 80
    
      - name: "test-master-b-tg"
        vpc_name: "Test B"
        protocol: "https"
        port: 80
```

How to invoke the role from a playbook (delete Target Groups):

```yaml
- name: Delete AWS ELB Target Groups
  include_role:
    name: "aws_manage_target_groups"
  vars:
    arg_action: "delete"

    aws_access_key: AKIAIOSFODNN7EXAMPLE
    aws_secret_key: wJalrXUtnFEMI/K7MDENG/bPxRfiCYEXAMPLEKEY
    aws_region: eu-west-2  # London

    aws_target_groups:
      - name: "test-master-a-tg"
      - name: "test-master-b-tg"
```
