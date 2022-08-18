## devops-demo

A repo for playing with the basics of DevOps and cloud providers (AWS, GCP, Azure). There are CI/CD pipelines, cloud provisioning (IAAC), and other random fun stuff. I wrote a lot of this code while taking a DevOps course offered by in28min (link below under Author).

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

    terraform init
    export AWS_ACCESS_KEY_ID=*******
    export AWS_SECRET_ACCESS_KEY=*********
    terraform apply

As an aside:

I really enjoyed learning about Terraform. It's powerful, *declarative*, and plays nice with all cloud providers.
The only trouble is the cloud providers' API often change, so the code here may not even be perfectly valid
by the next time it's read.

### Ansible (Server Provisioning)

A playbook `playbook\demo.yml` exists to execute various commands on three different servers. One
*group* of servers ('qa'), apache web server is installed with `yum`.

The playbook can be executed by entering `ansible-playbook playbooks/demo.yml` at the commmand line.

Alternatively, one can send manual commands from the command line with `ansible <group> -a "<command>"`.

*Note*: the servers defined in `ansible_hosts` will likely not exist at the time you are reading this!

Fun fact: You can interface with AWS servers through Ansible using the `boto3` library (`pip install boto3`).

As an aside:

Ansible is certainly useful for routine provisioning of remote servers. It is 
*procedural* rather than *declarative*, meaning it will provision whatever you tell it to provision
without regard for the provisions already exist! When it comes to more general, non-routine server
tasks, I also like to use Python's ``paramiko`` library.

### Azure DevOps (CI/CD Pipelines)

There are three pipelines in the ``azure-devops`` folder:

- ``basic-pipeline.yml``: Just a template the does some ``echo`` statements in place of useful code
- ``ci-cd-pipeline.yml``: Build a Docker image, publish and download Kubernetes manifest, deploy to K8S cluster
- ``iaac-pipeline.yml``: Deploy K8S cluster via Terraform plugin 

These pipelines rely on a K8S ``deployment.yml`` and some ``.tf`` scripts in the ``configuration`` folder.

The ``src`` folder contains an app for a 'currency-exchange' service and that is what is built with Docker.
None of the code in the ``src`` folder was written by me, but rather by the folks at https://courses.in28minutes.com
(who also provided much templating code!).

As an aside:

I personally have much more experience building CI pipelines with Jenkins. IMO, Azure DevOps offers a *much* better
environment for building CI pipelines. It is easier to find useful code snippets using Azure Pipelines search and select tool,
and the YAML format is cleaner than Jenkins Declarative Pipelines. No dig on Jenkins (it's free, after all), I still love it!

## Author

- Matt Runyon
- https://courses.in28minutes.com (Ranga and Ravi)
