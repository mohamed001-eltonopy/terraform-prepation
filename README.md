# terraform-prepation Exam

## KodeKloud Course:

## Test1:
  **What is Immutable Infrastructure?**
     Immutable infrastructure is another paradigm in which it ensures that resources are never modified after they have been deployed. 
     If a change is to be made, a new instance of that resource will be provisioned in place of the old one.
        
  **What does the “terraform show” command use to provide details of the Infrastructure?**
      The "terraform show" command inspects the state file and displays the resource details
      
  **What allows Terraform to make use of a declarative approach?**
    Terraform makes use of state files 
    
## Test2:
    Which block is used to configure settings related to Terraform itself? 
    Terraform
    
    Terraform will download the latest version for all the providers used within the configuration. The version downloaded may or may not work well with
    the configuration developed.
    
    Providers use a plugin-based architecture that is available for most infrastructure platforms within the public Terraform registry.

## Validation Block:
   you can add a validation block inside the variable block Within this validation block,we can define a condition 
   
  variable "aws_private_subnet_ids" {
  description = "VPC private subnet ids."
  type        = list(string)

  validation {
    condition     = length(var.aws_private_subnet_ids) > 1
    error_message = "This application requires at least two private subnets."
  }
}

## Test3:
   A variable name or a label must be unique within the same module or configuration.
   The variable block begins with the "variable" keyword followed by a user defined name/label for the variable.
   If both the “type” and the “default” argument are specified inside the variable block, the given default value must be convertible to the specified 
      type. True or False?    true 
   output values is displayed when you run #terraform apply    OR     #terraform outputs
   
   
 ## Explicit Dependency:
    Is used to make sure that specific resources are created before another in the configuration.
    
     depends_on = [
       aws_instance.db
     ]
## Test4:
   interpolation syntax: A way to reference a variables, attributes of resources and call functions.
   What is the generic way to reference attributes within the terraform expression?
       resource_type.name.attribute
   Both resources and datasources export arguments as readable attributes.
   When a module has multiple configurations for the same provider, which meta-argument can you use to specify the configuration?    providers
   Choose the meta-argument which is not supported by the data block.      lifecycle
   The behavior of __local-only____ data sources is the same as all other data sources, but their result data exists only temporarily during a Terraform
       operation, and is re-calculated each time a new plan is created.

 

















______________________________________
## Hashicorp course zealvora: 

**from vid 12-22**
- you have 2 types of Terraform providers:
  1- HashiCorp Distributed  "It installed automatically using #terraform init"       
  2- 3rd Party   "you must install plugin of this provider manually first and save it under specific path {./terraform/plugins} then run #terraform init"
  
**from vid 23-33**  
- Count parameter:
  the count parameter on resource can simplify configurations and let you scale resources by simply incrementing a number.
  ```
  resource "aws_instance" "test" {
  ami           = data.aws_ami.ubuntu.id
  instance_type = "t3.micro"
  tags = {
    Name = "Dev-Instance"
  }
  count = 2     // the count value and the resource can be scaled accordingly , but the issue is that all will have the same name "Dev-Instance".
  }
  ```
  
  - To solve this issue by using (count.index) that allows us to fetch the index,names from a list of variables of each iteration in the loop.
  ```
  variable "ec2-names" {
      type = list 
      default = ["Dev-Instance","Pre-Instance","Sys-Instance","Prod-Instance"]
  }
  
  resource "aws_instance" "test" {
  ami           = data.aws_ami.ubuntu.id
  instance_type = "t3.micro"
  tags = {
    Name = var.ec2-names[count.index]  // this will create 4 ec2-Instance accroding the list onames with a name for each loop.
  }
  count = 4   
  }
  ```


 - Conditional Expression: 
   Uses the values of a bool expression to scale one of two values.
   ```
   condition ? true_val :  false_val
   ex::   var.istest == true  ? 3 : 0 
   ```
  EXample:  
  ```
  variable "istest" {
      true
  }
  
  resource "aws_instance" "test" {
  ami           = data.aws_ami.ubuntu.id
  instance_type = "t3.micro"
  count = var.istest == true ? 1 : 0    // So if the var.istest is true then create 1 dev-instance if no don't create it 
  }
  
  resource "aws_instance" "test" {
  ami           = data.aws_ami.ubuntu.id
  instance_type = "t3.micro"
  count = var.istest == false  ? 1 : 0       // So if the var.istest is flase then create 1 prod-instance if no don't create it
  }
  ```
  
  - Locals Values:
    Itis used to avoid repeating the same values or expression multiple times within a module without repeating it.
    ```
   locals {
      common_tags = {
         Owner = "DevOps Team"
         service = "backend"
      }
   }
   
   resource "aws_instance" "app-dev" {
   ami = "ami-082b5a644766e0e6f"
   instance_type = "t2.micro"
   tags = local.common_tags
   }
   ```























