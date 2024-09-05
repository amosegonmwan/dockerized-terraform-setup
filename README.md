# Terraform in Docker

This repository provides a Docker setup to run Terraform within a Docker container, built on top of an Ubuntu base image. The setup allows you to manage AWS infrastructure using Terraform commands directly from within the container.

## Files in this Repository

### Dockerfile-terra
This Dockerfile outlines the steps to create a Docker image that installs Terraform:

- **Base Image:** Uses the latest Ubuntu image as the base.
- **Working Directory:** Sets the working directory inside the container to `/data`.
- **Package Installation:** Installs necessary packages including `wget`, `gnupg`, `software-properties-common`, and Terraform. The HashiCorp GPG key and repository are added to ensure Terraform is installed from the official source.
- **File Copy:** Copies the `main.tf` file into the container's `/data` directory.
- **Entry Point:** Configures the entry point for the container (empty by default), which defines the command that will run by default when the container starts.
- **CMD:** Sets the default command to `terraform -h`, which displays Terraform help information. This can be overridden by providing a different command when running the container.

### main.tf
This Terraform configuration file defines an AWS VPC resource:

- **Provider Configuration:** Specifies `us-west-2` as the AWS region.
- **VPC Resource:** Creates a VPC with the CIDR block `10.0.0.0/24` and assigns it a tag named "docker-vpc".

## Usage

### Building the Docker Image
To build the Docker image using the Dockerfile-terra:

```bash
docker image build -t terraform-img:1.0 -f Dockerfile-terra .
```

## Running the Docker Container
- To run the container with the default command:
```bash
docker container run --name terraform1 terraform-img:1.0 
```

- To run the container with an interactive bash shell:
```bash
docker container run --name terraform2 terraform-img:1.0 /bin/bash 
```

- To run the container in detached mode and attach later:
```bash
docker container run --name terraform3 -dit terraform-img:1.0 
docker container attach terraform3
```

- To start the container in interactive mode with a bash shell on Windows:
```bash
winpty docker container run --name terraform5 -it terraform-img:1.0 /bin/bash
```

## Bind Mount Volume
To mount your AWS credentials inside the container:
- **Linux:**
```bash
docker container run -it --name terraform4 -v ~/.aws:/root/.aws terraform-img:1.0 /bin/bash
```

- **Windows:**
```bash
winpty docker container run -it --name terraform5 -v C:\Users\amose\.aws\:/root/.aws terraform-img:1.0 /bin/bash
```

## Terraform Commands
Once inside the container (in the /data directory), you can run Terraform commands:
```bash
terraform init
terraform validate
terraform plan
terraform apply --auto-approve
```


