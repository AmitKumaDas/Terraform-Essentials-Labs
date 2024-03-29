# Terraform-Essentials-Labs

![image](https://github.com/AmitKumaDas/Terraform-Essentials-Labs/assets/152639378/12f2e34f-1ab0-4b2a-9db9-90b75af6450c)

# Terraform Essentials Lab Cheat Sheet

### Terraform Labs Pre-requisites
1. Basic understanding of Linux Commands.
2. Basic knowledge of a Cloud platform such as AWS.
3. Good to have an AWS-Free Tier Account for Practice.
4. Copy and paste a few details of your Allocated Region to a text file that we frequently use in labs as mentioned below:
     - Region (**EX:** us-east-1)
     - Availability Zones (**EX:** us-east-1a, us-east-1b)
     - AMI IDs (**EX:** Amazon Linux, RedHat, Ubuntu)
     - VPC ID, Subnet ID, Security group ID, KeyPair Name.

## Table Of Contents
* [Lab-1: Creating an EC2 Instance in AWS and Installing Terraform](https://github.com/AmitKumaDas/Terraform-Essentials-Labs/blob/main/README.md#lab-1-creating-an-ec2-instance-in-aws-and-installing-terraform)
* [Lab-2: AWS EC2 instance creation using Terraform Variables](https://github.com/AmitKumaDas/Terraform-Essentials-Labs/blob/main/README.md#lab-2-aws-ec2-instance-creation-using-terraform-variables)
* [Lab-3: Using Output Feature](https://github.com/AmitKumaDas/Terraform-Essentials-Labs/blob/main/README.md#lab-3-using-output-feature)
* [Lab-4: Understanding local values, functions and data sources](https://github.com/AmitKumaDas/Terraform-Essentials-Labs/blob/main/README.md#lab-4-understanding-local-values-functions-and-data-sources)
* [Lab-5: Remote State using Amazon Simple Storage Service](https://github.com/AmitKumaDas/Terraform-Essentials-Labs/blob/main/README.md#lab-5-remote-state-using-amazon-simple-storage-service)
* [Lab-6: Launching VPC and EC2 Instance]
* [Lab-7: Launching Auto-Scaling services]
* [Lab-8: Creating IAM Users, Groups using Terraform.]
* [Lab-9: Creating AWS resources using terraform modules]
* [Lab-10: Terraform Cloud]
* [Summaries of Each Lab in Terraform]
* [Frequently used Terraform Commands with Explanation](
* [Reference Links]

## Lab-1: Creating an EC2 Instance in AWS and Installing Terraform

To begin, log in to AWS Console.

### Task-1: Installing Terraform on Ubuntu 20.04 operating system

* Manually Launch a `t2.micro` instance with OS version as `Ubuntu 22.04 LTS` in North Virginia (us-east-1) Region.
* Use tag "`Name : TerraformServer`"
* Create a new Keypair with the Name `Terraform-Keypair-YourName`
* In security groups, include ports `22 (SSH)` and `80 (HTTP)`.
* Configure Storage: 10 GiB
* Launch the Instance.
* Once Launched, Connect to the Instance using `MobaXterm` or `Putty` or `EC2 Instance Connect` with username "`ubuntu`".

Once the EC2 is ready, follow the below Commands to perform lab:
```
sudo hostnamectl set-hostname TerraformServer
bash
```
```
sudo apt update
```
```
sudo apt install wget unzip -y
```
```
wget https://releases.hashicorp.com/terraform/1.6.3/terraform_1.6.3_linux_amd64.zip
```
To know the latest Terraform version - [Install Terraform](https://developer.hashicorp.com/terraform/downloads)
```
unzip terraform_1.6.3_linux_amd64.zip
```
```
ls
sudo mv terraform /usr/local/bin
```
```
rm terraform_1.6.3_linux_amd64.zip
```
```
ls
terraform
```
```
terraform -v
```

### Task-2: Install Required Packages and login to Ubuntu server using Credentials. 
```
sudo apt-get install python3-pip -y
```
```
sudo pip3 install awscli
```
```
aws configure
```
* When it prompts for Credentials, Enter the Keys as example shown below
  
| `Access Key ID.` | `Secret Access Key ID.` |
| ------------------ | ------------------------- |
| AKIAIOSFODNN7EXAMPLE | wJalrXUtnFEMI/K7MDENG/bPxRfiCYEXAMPLEKEY |

##### Note: If Credentials are not available generate from AWS IAM Service
Once LoggedIn check the account access
```
aws s3 ls
```
`Or` Use below command to check whether it is authenticated.
```
aws iam list-users
```
### Task-3: Now we are ready to perform the labs
Create directory as Lab1 & create local file
```
mkdir Lab1 && cd Lab1
```
```
vi local.tf
```
```
resource "local_file" "myfile" {

  filename = "/home/ubuntu/test.txt"
  content  = "wel come to terraform"
}
```
```
terraform init
```
```
terraform plan
```
```
terraform apply
```
To check whether the file is created follow the command

```
cd /home/ubuntu
ls
```
```
cat test.txt
```
Once Verified, you can destroy the File by changing it to the Lab1 Directory
```
cd Lab1
```
```
terraform destroy
```
```
cd ~
rm -rf Lab1
```
### Task-4: Launching your first AWS EC2 instance using Terraform 
```
cd ~
mkdir EC2-lab && cd EC2-lab/
```
```
vi example.tf
```
Add the given lines, by pressing "INSERT"

##### Note: 
1. Replace your allocated `Region` and `AMI ID` of the same Region.
2. `"Yourname-EC2-1"` with Your Name.
```
provider "aws" {
  profile = "default" # This line is not mandatory.
  region  = "us-east-2"
}

resource "aws_instance" "example" {
  ami           = "ami-07efac79022b86107"
  instance_type = "t2.micro"
  tags = {
    Name = "Yourname-EC2-1"
  }
}
```
Save the file using "ESCAPE + :wq!"
```
terraform init
```
```
terraform fmt
```
```
terraform validate
```
```
terraform plan
```
```
terraform apply
```
```
#List the files
ls 
```
To see what is saved in `terraform.tfstate` use the below command.

```
cat terraform.tfstate
```
Now, Let's Replace the AMI ID with `Amazon Linux AMI 2023` from the same region and see what happens.

`Note:` To get the `Amazon Linux AMI 2023` go to EC2 Instances, click on Launch, and select the `Amazon Linux AMI 2023` AMI and copy the ID and paste in File.
```
vi example.tf
```
Replace the AMI ID, by pressing "INSERT"  
```
provider "aws" {
  profile = "default"
  region  = "us-east-2"
}

resource "aws_instance" "example" {
  ami           = "ami-02a89066c48741345"
  instance_type = "t2.micro"
  tags = {
    Name = "Yourname-TF-1"
  }
}
```

Save the file using "ESCAPE + :wq!"

Now in Terraform Plan Verify the Changes.
```
terraform plan
```
```
terraform apply
```
Once Applied then go to the console and see that the Previous EC2 is being destroyed and new EC2 is getting created.
    
Also verify that the OS is `Amazon Linux AMI 2023`

Again when you cat the `terraform.tfstate` file it shows a new AMI ID.
```
cat terraform.tfstate
```
Use the `terraform destroy` command for cleaning the infrastructure used in this lab
```
terraform destroy
```
Finally, verify that the resources are deleted in the Console.
```
cd ~
rm -rf EC2-lab
```

## Lab-2: AWS EC2 instance creation using Terraform Variables


### Task-1: Create EC2 instance using variables 
```
mkdir variables-lab && cd variables-lab/
```
Now Create four Files ie. `provider.tf,` `vars.tf,` `terraform.tfvars,` `instance.tf.`
```
vi provider.tf
```
Add the given lines, by pressing "INSERT" 
```
provider "aws"{
  access_key=var.AWS_ACCESS_KEY
  secret_key=var.AWS_SECRET_KEY
  region=var.AWS_REGION
}
```
Save the file using "ESCAPE + :wq!"
```
vi vars.tf
```
Add the given lines, by pressing "INSERT" also replace your `Region`
```
variable "AWS_ACCESS_KEY"{}
variable "AWS_SECRET_KEY"{}
variable "AWS_REGION"{
  default = "us-east-2"
}
```
Save the file using "ESCAPE + :wq!"
```
vi terraform.tfvars
```
Add the given lines, by pressing "INSERT" Also replace them with your `AccessKey` and `Secret Access Keys` that you created and downloaded earlier.
```
AWS_ACCESS_KEY="< Insert your AWS Access Key >"
AWS_SECRET_KEY="< Insert your AWS Secret Key >"
```
Save the file using "ESCAPE + :wq!"
```
vi instance.tf
```
Add the given lines, by pressing "INSERT" Also replace your `region's AMI ID` and `YourName`
```
resource "aws_instance" "terraform_example"{
  ami = "ami-07efac79022b86107"
  instance_type="t2.micro"
  tags = {
    Name = "Variables-Lab2-YourName"
  }
}
```
Save the file using "ESCAPE + :wq!"
```
terraform init
```
```
terraform plan
```
```
terraform apply
```
Once applied, go to the Console and check that the new EC2 is created using Variables.

Use the `"terraform destroy"` command to clean the infrastructure used in this lab
```
terraform destroy -auto-approve
```

### Task-2: Implementing map variables that dynamically fetch AMI based on the Linux distro selected
```
vi instance.tf
```
Delete all the existing lines and Add the given code, by pressing "INSERT" and replacing `YourName`

`Note:` To delete all the lines at a time use the commands `Esc+gg+dG` and once cleared then add the new lines.
```
resource "aws_instance" "terraform_example"{
  ami = var.AMIS[var.Linux_distro]
  instance_type="t2.micro"
  tags = {
    Name = "YourName-Lab2-Task2"
  }
}
```
Save the file using "ESCAPE + :wq!"

Now, Also make changes in the `Vars.tf` file.
```
vi vars.tf
```
Delete all existing lines and Add the given lines, by pressing "INSERT" Also ensure to replace your `Region,` `AMi IDs` of the same region for `redhat,` `ubuntu` and `amazon`

`Note:` To delete all the lines at a time use `Esc+gg+dG`
```
variable "AWS_ACCESS_KEY"{}
variable "AWS_SECRET_KEY"{}
variable "AWS_REGION"{
  default = "us-east-2"
  }

variable "Linux_distro"{
  #description = "Please Enter the Linux distro (redhat, ubuntu, amazon)"
  default = "amazon"
  }

variable "AMIS"{
  type=map(string)
  default={
   redhat="ami-0ba62214afa52bec7"
   ubuntu="ami-0fb653ca2d3203ac1"
   amazon="ami-0231217be14a6f3ba"
  }
}
```
Save the file using "ESCAPE + :wq!"
```
terraform fmt
```
```
terraform validate
```
```
terraform plan -var 'Linux_distro=redhat' -out myplan
```
```
terraform apply myplan
```
Use the `terraform destroy` command to clean the infrastructure used in this lab
```
terraform destroy
```
Once Done remove the `EC2-lab` Directory.
```
cd ~
rm -rf variables-lab

```


## Lab-3: Using Output Feature 

### Task-1: Using output feature of Terraform to get the IP Address of EC2 Instance
```
mkdir output-variable-lab && cd output-variable-lab
```
```
ls
```
Create the instance.tf file
```
vi instance.tf
```
```
provider "aws" {
  region     = var.AWS_REGION
}

resource "aws_instance" "example" {
  ami           = var.AMIS[var.AWS_REGION]
  instance_type = "t2.micro"
  provisioner "local-exec" {
    command = "echo ${aws_instance.example.private_ip} >> private_ips.txt"
  }
}
```
Save the changes
```
vi output.tf
```
```
output "Public_ip" {
  description = "Public IP of the instance"
  value = aws_instance.example.public_ip
}

output "Private_ip" {
  sensitive = true
  description = "Private IP of the instance"
  value = aws_instance.example.private_ip

}
```
Save the file

In `vars.tf` ensure to replace your `region` and Include your `region's Ubuntu AMI ID` to the list.
```
vi vars.tf
```
```
variable "AWS_REGION" {
  default = "us-east-2"
}

variable "AMIS" {
  type = map(string)
  default = {
    us-east-2 = "ami-089c26792dcb1fbd4"
    us-west-2 = "ami-00448a337adc93c05"
    eu-west-1 = "ami-0e309a5f3a6dd97ea"
  }
}
```
Once replaced, save the changes.
```
terraform init
```
```
terraform fmt
```
```
terraform validate
```
```
terraform plan
```
```
terraform apply
```
```
ls 
cat private_ips.txt 
```
```
terraform output Public_ip
```
```
terraform output Private_ip
```
Use the `terraform destroy` command to clean the infrastructure used in this lab.
```
terraform destroy
```
Once done, remove the directory and Zip file using the `rm -rf` command below.
```
cd ~
rm -rf output-variable-lab
```



## Lab-4: Understanding local values, functions and data sources

### Task 1: Local Values 
```
mkdir lab4 && cd lab4
```
```
mkdir task1 && cd task1
```
```
vi local.tf
```
Add the given lines, by pressing "INSERT" and replace "ENTER THE AMI VALUE" with actual values
```
provider "aws" {
  region     = "us-east-1"
}

locals {
  custom_tags = {
    Team = "DevOps"
    Company = "CloudThat"
  }
}
resource "aws_instance" "instance1" {
   ami = "******ENTER THE AMI VALUE********"
   instance_type = "t2.micro"
   tags = local.custom_tags
}

resource "aws_instance" "instance2" {
   ami = "******ENTER THE AMI VALUE********"
   instance_type = "t2.small"
   tags = local.custom_tags
}

resource "aws_ebs_volume" "db_ebs" {
  availability_zone = "us-east-1a"
  size              = 8
  tags = local.custom_tags
}
```

Save the file using "ESCAPE + :wq!"
```
terraform init
```
```
terraform plan
```
```
terraform apply
```
```
terraform destroy
```

### Task 2: Functions
```
cd ..
```
```
mkdir task2 && cd task2
```
```
vi functions.tf
```
Add the given lines, by pressing "INSERT" 
```
provider "aws" {
  region     = var.region
}

variable "region" {
  default = "ap-south-1"
}

variable "tags" {
  type = list
  default = ["ec2-1","ec2-2"]
}

variable "ami" {
  type = map
  default = {
    "us-east-1" = "ami-0fc5d935ebf8bc3bc"         ******ENTER THE AMI VALUE********
    "us-west-1" = "ami-060d3509162bcc386"         ******ENTER THE AMI VALUE********
    "ap-south-1" = "ami-09ba48996007c8b50"        ******ENTER THE AMI VALUE********
  }
}

resource "aws_instance" "application-servers" {
   ami = lookup(var.ami,var.region)
   instance_type = "t2.micro"
   count = 2

   tags = {
     Name = element(var.tags,count.index)
   }
}
```

Save the file using "ESCAPE + :wq!"
```
terraform init
```
```
terraform plan
```
```
terraform apply
```
```
terraform destroy
```

### Task 3: Data sources

```
cd ..
```
```
mkdir task3 && cd task3
```
```
vi data.tf
```

Add the given lines, by pressing "INSERT" 
```
provider "aws" {
  region     = "us-east-2"
}

data "aws_ami" "ami" {
  most_recent = true
  owners = ["amazon"]


  filter {
    name   = "name"
    values = ["amzn2-ami-hvm*"]
  }
}

resource "aws_instance" "ec2instance" {
    ami = data.aws_ami.ami.id
   instance_type = "t2.micro"
}
```

Save the file using "ESCAPE + :wq!"

```
terraform init
```
```
terraform plan
```
```
terraform apply
```
```
terraform destroy
```
```
cd ~
rm -rf lab4
```


## Lab-5: Remote State using Amazon Simple Storage Service 

### Task-1: Manually Create a S3 Bucket using AWS Console 

* Create a new S3 bucket in your Allocated Region by name: `youryame-terraform` (S3 allows lowercase only)
* While creating,
    - Select `"ACLs enabled"`
    - Uncheck `"block public access"` and select `"I acknowledge that the current settings might result in this bucket and the objects within becoming public"`
    - `"Enable versioning"`
    - Then, click on `Create bucket`
* Once done, To cross-check whether the Bucket is created or not, run the below command in CLI.
```
aws s3 ls 
```
### Task-2: Configure Remote State
```
cd ~
mkdir S3-Lab && cd S3-Lab
```
```
mkdir remote-state-lab && cd remote-state-lab
```
```
ls
```
Create a New Configuration File 
```
vi instance.tf
```
Add the below given lines, by pressing "INSERT"  
```
resource "aws_instance" "terraform-remoteState" {
  ami           = var.AMIS[var.AWS_REGION]
  instance_type = "t2.nano"
  provisioner "local-exec" {
    command = "echo ${aws_instance.terraform-remoteState.private_ip} >> private_ips.txt"
  }
}

output "ip" {
  value = aws_instance.terraform-remoteState.public_ip
}
```
Save the file using "ESCAPE + :wq!"
```
vi vars.tf
```
In `vars.tf` file ensure to replace your `region` and Include your `region's Ubuntu AMI ID` to the list.
```
variable "AWS_REGION" {
  default = "us-east-2"
}

variable "AMIS" {
  type = map(string)
  default = {
    us-east-2 = "ami-0e83be366243f524a"
    us-west-2 = "ami-02e7fad8336aa2c57"
    eu-west-1 = "ami-029f9476"
  }
}
```
Once done, save the file and follow further steps.
```
vi provider.tf
```
```
provider "aws" {
  region = var.AWS_REGION
}
```
Now, Create a New Configuration File for storing "`terraform.tfstate`" file in the backend. (ie. `Amazon S3.`)

```
vi backend.tf
```
Add the given lines, by pressing "INSERT" and add your `bucket's region` and `Bucket Name`
```
terraform {
  backend "s3" {
    region = "<Replace your s3 bucket region>"
    bucket = "<Replace your s3 bucket name>"
    key    = "terraform/remotestate"
  }
}
```
Save the file using "ESCAPE + :wq!"
```
cat backend.tf
```
```
terraform init
```
```
terraform fmt
```
```
terraform validate
```
```
terraform plan
```
```
terraform apply
```
* Go to the S3 bucket and click on `terraform` > `remotestate` > In Properties Copy the `Object URL` and paste it in Browser.
  (By default it shows Access Denied)
* To view the content of the file, in S3 Bucket tab, Click on `permission` and click on `Edit` under `Access control list (ACL)` > `Everyone (public access)` > Check `"Read"` then check `I understand the effects of these changes on this object` and then Click on `Save changes`
* Refresh the Object URL Page in the browser (or again Copy-paste the `object URL` into the web browser).
* Now, You should be able to access the state file and View the resources.
  (It shows the attributes of a single resource in the Terraform state of `aws_instance.terraform-remoteState`.)

Use the `terraform destroy` command to clean the infrastructure used in this lab, 
```
terraform destroy
```
Once done, Remove the directory and Zip file using "`rm -rf`"
```
cd ~
rm -rf S3-Lab
```
**Note:** Also Ensure to delete the `S3 Bucket` (To delete, first empty the Bucket and then Delete it.)
