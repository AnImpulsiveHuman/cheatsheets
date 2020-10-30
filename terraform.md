# Learn Terraform:

## Basic commands:
```
terraform init                                                 -> initialize a working directory, install terraform modules/plugins and change the backend config
terraform validate                                             -> validates the configuration
terraform fmt                                                  -> automatically updates configurations in the current directory

terraform plan                                                 -> shows the new changes that will be made (i.e creates a plan)
terraform plan -out="<planname>"                               -> stores the plan in a file
terraform plan -destroy                                        -> creates a deletion plan

terraform apply                                                -> applies the new changes
terraform apply "<planname>"                                   -> applies the plan in the file
terraform apply -auto-approve                                  -> approves automatically while applying

terraform destroy                                              -> cleans up the infrastructure
terraform destroy -target <resource name>                      -> only destroys the specified resources

terraform show                                                 -> provides human-readable output from a plan

terraform apply -var 'variable=value'                          -> assign variables from arguments   
terraform apply -var-file='<varfile.tfvars>'                   -> assign variables from a file(can use mulitple -var-file in same command')
terraform apply -var 'amis={ key = "value", key1 = "value1" }  -> assign variables (maps) from arguments 
Env variable format: TF_VAR_variablename                       -> assign variables as environment variable
```
## Basic Infrastructure configuration:
#### Basic AWS configuration:
```
terraform {
  required_providers {
    aws = {
      source  = "hashicorp/aws"
      version = "~> 2.70"
    }
  }
}

provider "aws" {
  profile = "default"
  region  = "us-west-2"
}

resource "aws_instance" "example" {
  ami           = "ami-830c94e3"
  instance_type = "t2.micro"
}
NOTE: change the region, ami and instance type.
```
#### Basic Docker configuration:
```
terraform {
  required_providers {
    docker = {
      source = "terraform-providers/docker"
    }
  }
}

provider "docker" {}

resource "docker_image" "nginx" {
  name         = "nginx:latest"
  keep_locally = false
}

resource "docker_container" "nginx" {
  image = docker_image.nginx.latest
  name  = "tutorial"
  ports {
    internal = 80
    external = 8000
  }
}
NOTE: change the image name and ports as per need.
```
