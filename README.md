# terraform-prepation Exam

______________________________________________________________________________________________________________
## Nana Course
______________________________________________________________________________________________________________

What's Terraform?
 automate and manage your Infrastructure, your platform , and services that run on that platform.
 It's Open source and it's declrative language"= you don't have to define every step of how this automation and managment is done only 
      you just define the end result and Terraform will figure out how to execute it" 
  
Terraform Architecture? Has 2 Main components
  1- Terraform Core :uses 2 input sources to figures out the Plan of what needs to be created/updated/destroyed
      (by comparing the current vs desired)state.
      (a) TF-Config: that you define what you need to create or provisioning (desired state)
      (b) Terraform State: where terraform keeps the up-to-date state of how the current setup of the infra looks like (current state)
  2- Providers: where Terrform execute the Plan so it connects to AWS,AZURE,......to have access to their resources and creates what it
         needs Ec2,IAM,VPC,.....
 
 Terraform Commands:
   terraform init: installs providers define in the terraform configuration
   terraform refresh,state: fetches the current state of your infastructure.
   terraform plan: make sure that the desired state == current state. 
   terraform apply: execute the plan
   terraform destroy: destroy the resources/infrastructure
 
 Terraform Installiation:
   brew install terraform 
   terraform -v
   brew upgrade terraform
   
 Connect to aws:
   aws configure list 
   
 Note: 
    Route tables : in aws is like virtual Routers that route and handle the traffic inside/outside your vpc/subnets.
    User Data: is an entry point script that executed on ec2 when the server is initiated 
    
 Modules: to not make all of our configurations in one file but to make it modular so basically break up parts of our configurations into logical groups 
          and packaging them together in folders and this folder called modules. So, the Container for multiple resources to create Module for EC2 
          Instance , used together and packaging them to create it , and we can resue it multiple times 
        - When you define a module you can pass parameter as you want through "Inputs" , and you can access the output/results of the modules by "Outputs"
        - Once you create a module you should install it when you run terraform config file #terraform init

How EKS Cluster is created using Terraform ? 
    1- we have controlplane"master node" that it managed by aws ....>> nothing you should to do
    2- nce we have a master node we want to create "worker nodes" and connect these worker to master nodes to have a complete cluster  
        and then deploy apps.
    3- SO, you need to create a VPC Where the worker nodes will run That are "EC2 instances" you creat or using "node groups" that do 
         some work for you like install all necessary things on ec2 instances.
    4- You Always create a cluster in a specific region "ex: eu-west-3" that have multiple AZs which each of az has one worker node for 
         High Availability And each AZ have (public subnet + private Subnet that have inside it worker nodes), you have 1 RouteTable for 
         a VPC with Internet Gateway to connect to the Internet and allow traffic to/from vpc, And you have 1 RouteTable that routes the 
        Internal traffic inside VPC. you have  another RouteTable for a VPC with "Nat Gateway" that allows workernodes to connect to 
        master node as they exist in different vpc so they connect with each other through "Nat Gateway" not "Internet Gateway".
      - All private subnets associates with this "Nat Gateway" Routetable and All public subnets associates with this "Internet Gateway"
            
 Note:
   - Each Subnet has "default NatGateWay" that you should enable it so it can route traffic inside subnet <enable_nat_gateway = true>,
     Also you should create a common NatGateway that will route all internal traffic for all all private subnets through Common 
     NateGateway that you created.
   - when we create "LoadBalancer " in kubernetes that should be accessable from the internet but this (LB) is created inside private 
     Subnet so it can't access directly from the Internet ,So Kubernetes provision "cloud native loadBalancer" in public Subnet to 
      access (LB) in private Subnet. 
       
   - When you define EKS Cluster you should to define which type of Worker Node you want to connect to ths cluster :
       A) Self Managed - EC2 Instances    
       B) Semi Managed - NodeGroup
       C) Fargate 
       
   - Once The EKS Cluster Is created it needs to authenticates with Mater Processes of kubernetes cluster so you need to configure the 
        provider with the authentication and there's a lot of ways to do that.

  Connect To EKS Cluster using kubectl:
    - install (AWS CLI , kubectl , aws-iam-authenticator)
    - generate config file of your kubbernetes cluster ....>> #aws eks update-kubeconfig  --name  <name-of-cluster>  --region <eks-
      region-you-defined>   
    - #kubectl get nodes
  
 
 
 
 
 
 
 
 
 
 




______________________________________________________________________________________________________________
## KodeKloud Course:
______________________________________________________________________________________________________________

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























