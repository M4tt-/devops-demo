## Overview

A repo for playing with the basics of DevOps and cloud providers (AWS, GCP, Azure). There are CI/CD pipelines, cloud provisioning (IAAC), and other random fun stuff.
Tech stack:
- Docker
- Kubernetes
- Ansible
- Terraform
- Azure DevOps
- Jenkins

## Content

### Terraform (IAAC)
`main.tf` declares an AWS infrastructure with the following resources:

- S3 bucket
- EC2 instances (as HTTP servers)
- Load Balancer
- Security Groups

`output.tf` is merely a file that outputs some details about the security group on the HTTP servers.
`variables.tf` contains a variable for the AWS key pair used for AWS cloud authentication.
`data-providers.tf` contains some AWS data sources that are referred to in `main.tf`.

Assuming one has an AWS account and basic configuration in place, one can get up and running with this infrastructure by carrying out
the following commands:

    ```
    terraform init
    export AWS_ACCESS_KEY_ID=*******
    export AWS_SECRET_ACCESS_KEY=*********
    terraform apply
    ```
### Ansible (Server Provisioning)

A playbook `playbook\demo.yml` exists to execute various commands on three different servers. One
*group* of servers ('qa'), apache web server is installed with `yum`.

The playbook can be executed by entering `ansible-playbook playbooks/demo.yml` at the commmand line.

Alternatively, one can send manual commands from the command line with `ansible <group> -a "<command>"`.

*Note*: the servers defined in `ansible_hosts` will likely not exist at the time you are reading this!

Fun fact: You can interface with AWS servers through Ansible using the `boto3` library (`pip install boto3`).

### Azure DevOps (CI/CD Pipelines)
