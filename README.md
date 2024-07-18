DEPLOYING HIGHLY AVAILABLE, SCALABLE AND SECURE WEBSITE ON AWS

Project Description: High Availability, Scalability and
Security of Web Application on AWS
In this project, I have deployed a sample website on AWS,
implementing a robust
architecture to ensure high availability, security, and scalability.
The key components and configurations are as follows:

Load Balancing: To evenly distribute incoming traffic and
enhance fault tolerance, I have employed an AWS Elastic Load
Balancer (ELB). This ensures that user requests are efficiently
handled by the available servers.
Multi-AZ Deployment: The web application is hosted across
multiple Availability Zones (AZs) within a region. This setup
guarantees high availability and resilience, as it mitigates the
impact of an AZ failure.
Custom VPC: A custom Virtual Private Cloud (VPC) has been
created to provide network isolation and enhanced security.
This allows for fine-grained control over the network
environment.
Private Subnets: The servers hosting the website are placed in
private subnets, enhancing security by restricting direct
access from the internet. These subnets are configured to
route traffic through the load balancer, which is the only
publicly accessible endpoint.
Security Groups and NACLs: Security groups and Network
Access Control Lists (NACLs) are configured to control
inbound and outbound traffic at both the instance and subnet
level, ensuring that only legitimate traffic reaches the servers.
Domain and SSL Certificate: A custom domain has been
purchased for the website, providing a professional and
memorable address for users.
Additionally, an SSL certificate has been generated and
implemented to secure the data transmission between the
users and the application, ensuring that all communications
are encrypted and trustworthy.
Access through Load Balancer: The web application is only
accessible through the load balancer’s public DNS, providing a
single entry point and further securing the backend servers.
This architecture not only ensures high availability and fault
tolerance but also secures the application by minimizing direct
exposure to the internet and controlling access strictly through
the load balancer. The addition of a custom domain and SSL
certificate further enhances the user experience by providing a
secure and reliable connection.

In this project, I got a chance to work on the following
technologies:
1. AWS EC
AWS Auto Scaling Group (ASG)
AWS Application Load Balancer (ASG)
AWS VPC
Domain Name System (DNS)
Private Subnet
SSL Certificates
Practical Implementation
Let’s Create Custom VPC (Virtual Private Cloud):

VPC (Virtual Private Cloud): A VPC is a virtual network that closely
resembles a traditional network that you’d operate in your own
data center.

A Custom VPC in AWS is a user-defined Virtual Private Cloud that
allows you to configure and manage your own isolated network
within the AWS cloud.

o Click on create VPC.

o Click VPC and More Because it allows you to add VPC and its
component together.

o IPv4 CIDR Block is nothing but a size on IP address range.
o I am using Multi-AZ deployment that’s why I have choose
number of AZ to 2.
o In my VPC I want to private and two private subnet in each
AZ.
Public Subnet: has a direct route to an internet gateway.
Resources in a public subnet can access the public internet.

Private Subnet: does not have a direct route to an internet
gateway.

Internet Gateway: provide two-way public connectivity to
applications running in AWS Regions and/or in Local Zones.

NAT Gateway: You can use a NAT gateway so that instances in a
private subnet can connect to services outside your VPC but

external services cannot initiate a connection with those
instances.
o Click on create VPC and it will take some time to create VPC.
o Here is our network design.
Route Table: contains a set of rules, called routes, that determine
where network traffic from your subnet or gateway is directed.

Launch Template: holds instance configuration information. It
includes the ID of the Amazon Machine Image (AMI), the

instance type, a key pair, security groups, and other parameters
used to launch EC2 instances.
Auto Scaling Group: contains a collection of EC2 instances that
are treated as a logical grouping for the purposes of automatic
scaling and management.

Application Load Balancer: operates at the request level (layer 7),
routing traffic to targets (EC2 instances, containers, IP addresses,
and Lambda functions) based on the content of the request.

Let’s Create Launch Template:

o Click on create launch template.

o Here I am using Amazon Linux 2 as an AMI.

Amazon Machine Image(AMI): template that contains a software
configuration (for example, an operating system, an application
server, and applications). From an AMI, you launch an instance.

o Here I am using t2.micro as an instance type which is free
one.
Instance Type: where you specify the hardware of the host
computer used for your instance

Key Pair: combination of public and private key that you can use
to securely login on your instance.

o Here You have to select the Custom VPC that we have
created.
In user data type following command:

#!/bin/bash
yum install httpd -y
systemctl start httpd
systemctl enable httpd
mkdir /var/www/html

User data: allows user to specify code, task or commands that
are going to run at the time of creation of EC2.
Let’s Create Auto Scaling Group & Application Load Balancer:
o Click on create Auto Scaling Group
o Here Select Launch Templates that we have created yet.
o Click on next.

o In VPC section, select our Custom VPC.
o In Subnets section select both of our private subnet, because
we want our EC2 servers to run in private subnet.

o Click on next.
o In load balancing option, choose attach new load balancer
option; this will create and attach load balancer with our
ASG.
o Choose Application load balancer as a type.
Bastion host: A bastion host is a server whose purpose is to provide access to a private network
from an external network, such as the Internet.
Here is the private IP address of both the machine in my private subnet:
192.168.152.192 & 192.168.143.

o Make load balancer scheme as a internet-facing; this will make
our load balancer to listen from internet.

o Choose public subnet; because we want to put our load
balancer in public subnet.

o In a scaling policy, I want 2 instances each time, that’s why I
put 2 as a Min desired Capacity.

o In Max desired capacity I put 4; when load increases in our
instances, so it will scale our number of instances to 4.

o Here I choose metric type as an Average CPU Utilization and
target value to 90; that means It will scale EC2 when our
existing EC2 instances got more than 90% of CPU utilization.

o Now click on create Auto Scaling Group.

o Here you can see that Auto Scaling Group has automatically
created two instances for us; because we have defined
desired capacity to 2.
Bastion Host:
o Here I have created one more instance that is known as
bastion host; I have put this instance in public subnet of my
VPC so that I am able to take SSH on it.
o I have transferred my index.html file (file that contains my
website) & .pem file (that allows me to do SSH to my
instances in private subnet) to bastion host using sFTP.
Bastion host: A bastion host is a server whose purpose is to
provide access to a private network from an external network,
such as the Internet.

o Here is the private IP address of both the machine in my
private subnet:
o 192.168.152.192 & 192.168.143.
o Now I have connected to my bastion host through EC
instance connect.

o Then I uses SCP protocol to transfer index.html file to my
both machines inside the private subnet.

scp -i example.pem index.html ec2-user@192.168.152.192:/home/ec2-
user
scp -i example.pem index.html ec2-user@192.168.143.74:/home/ec2-
user

o Then I uses SSH to connect both of the instances and copy
index.html file from /home/ec2-user to /var/www/html/

ssh -i example.pem ec2-user@192.168.152.19 2
sudo cp index.html /var/www/html
exit
ssh -i example.pem ec2-user@192.168.143.7 4
sudo cp index.html /var/www/html
exit
o In one of the machine I have changed title of my website to
ensures load balancing.

o Using load balancer’s DNS name, my website is got accessed.
When I refresh my page, so you can see that my request is
got redirected to another instance; that means my load
balancer works fine!!!
Let’s Add Entry in DNS:

o I want my website to get accessed using my own domain
name “vsphere.work.gd”. so I have created CNAME record
that points http://www.vshpere.work.gd to my load balancer’s DNS
name.

o Now you can see that, my website is got accessed through
my own domain name.

Let’s Generate SSL Certificate:

SSL Certificate: digital certificate that authenticates a website’s
identity and enables an encrypted connection.

Amazon Certificate Manager: AWS Certificate Manager (ACM)
is a service that lets you easily provision, manage, and deploy
public and private Secure Sockets Layer/Transport Layer
Security (SSL/TLS) certificates.

o Currently my website is got accessed using http protocol,
which is probably not secure.

o I have generated SSL certificate for my website from domain
registrar, so that my website runs on HTTPS.

o I have provided .req file to my domain registrar and then i
got certificate keys from it; that i have put in Amazon
Certificate Manager.

o Here you can see, I have successfully imported my SSL
certificate in ACM.

o Now in load balancer’s listeners rules, remove rule for HTTP
and add new rule that works on HTTPS and in Default
SSL/TLS certificate, use ACM as a certificate source and select
our imported certificate and click on save.

o Now you can see that our website is got accessed through
HTTPS protocol.
Implementation
The implementation of the project involved several key steps to
deploy a highly available and secure web application on AWS.
Below is a detailed account of the implementation process:

Setting Up the AWS Environment
AWS Account: Ensure you have an AWS account set up and
ready to use.
IAM Roles: Create necessary IAM roles and policies to
manage access and permissions.
Creating a Custom VPC
VPC Creation: Navigate to the VPC Dashboard and create a
custom VPC with a suitable IP address range (e.g.,
10.0.0.0/16).
Subnets: Create subnets within the VPC:
o Public Subnet: For resources that need internet access
(e.g., NAT Gateway).
o Private Subnets: For hosting the web servers to enhance
security.
Configuring Security Groups and NACLs
Security Groups: Define security groups to control inbound
and outbound traffic for your instances.
o Web Server Security Group: Allow traffic from the load
balancer only.
o Load Balancer Security Group: Allow HTTP/HTTPS traffic
from the internet.
NACLs: Configure Network Access Control Lists (NACLs) to
add an additional layer of security at the subnet level.
Setting Up the Load Balancer
ELB Creation: Create an Elastic Load Balancer (ELB) through
the EC2 Dashboard.
Listeners and Target Groups: Configure listeners for HTTP
and HTTPS. Create target groups and register your web
servers with these target groups.
SSL Certificate: Obtain an SSL certificate from AWS
Certificate Manager (ACM) and attach it to the load balancer
for HTTPS traffic.
Deploying Web Servers
Launch EC2 Instances: Launch EC2 instances in the private
subnets to host your web application.
Web Server Configuration: Install necessary software (e.g.,
Apache, Nginx) and deploy your web application on these
instances.
Auto Scaling: Set up an Auto Scaling Group to automatically
adjust the number of instances based on demand.
Configuring Route Tables and Internet Gateway
Route Tables: Create and associate route tables for each
subnet. Ensure the private subnets have routes to the NAT
Gateway for internet access.
Internet Gateway: Attach an internet gateway to your VPC
and configure the public subnet route table to allow
internet access.
DNS and Domain Configuration
Domain Purchase: Purchase a domain through AWS Route
53 or another domain registrar.
DNS Setup: Create a hosted zone in Route 53 and configure
DNS records to point to the load balancer.
Testing and Validation
Accessing the Application: Test accessing the web
application through the load balancer’s public DNS name
and ensure it routes traffic correctly.
Security Testing: Perform security tests to ensure that the
instances in private subnets are not directly accessible from
the internet.
Load Testing: Conduct load testing to verify that the
application can handle expected traffic volumes.
By following these steps, the web application was successfully
deployed on AWS with a robust architecture that ensures high
availability, security, and scalability. The implementation process
involved configuring various AWS services and components to
create a secure and efficient environment for the application.

Conclusion
This project demonstrates the successful deployment of a highly
available and secure web application on AWS. By leveraging an
Elastic Load Balancer, multi-AZ deployment, and a custom VPC
with private subnets, we ensured fault tolerance, scalability, and
network isolation. Implementing security groups, NACLs, a
custom domain, and an SSL certificate further enhanced the
security and reliability of the application. Overall, this
architecture provides a robust and secure environment, ensuring
a seamless and secure experience for users.

References.....................................................................................................................................................
o Youtube
o Udemy
o AWS Solutions Architect Study Guide v4
