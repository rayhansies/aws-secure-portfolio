\# AWS VPC Security Lab



\## Project Overview



This lab demonstrates the design and validation of a segmented AWS Virtual Private Cloud environment using public and private subnets, route tables, an Internet Gateway, security groups, EC2 instances, and VPC Reachability Analyzer.



The goal was to build a small but realistic network architecture that follows the principle of least privilege and proves that only explicitly approved traffic can move between tiers.



\## Architecture



The environment was built in a custom VPC:



\- VPC: `secure-vpc-lab`

\- IPv4 CIDR: `10.20.0.0/16`

\- Region: `us-east-1`



Two subnets were created:



\### Public Subnet



\- Name: `public-subnet-1`

\- CIDR: `10.20.1.0/24`

\- Availability Zone: `us-east-1a`

\- Auto-assign public IPv4: Enabled

\- Associated route table: `public-route-table`



\### Private Subnet



\- Name: `private-subnet-1`

\- CIDR: `10.20.2.0/24`

\- Availability Zone: `us-east-1a`

\- Auto-assign public IPv4: Disabled

\- Associated route table: `private-route-table`



\## Internet Connectivity



An Internet Gateway was created and attached to the VPC:



\- Internet Gateway: `secure-vpc-lab-igw`



The public route table included:



\- `10.20.0.0/16` → local

\- `0.0.0.0/0` → Internet Gateway



The private route table did not include a default route to the Internet Gateway.



This ensured that the public subnet had internet connectivity while the private subnet remained isolated from direct internet access.



\## Security Groups



Two security groups were created to enforce tier-based access.



\### Public Web Security Group



Security group:



`public-web-sg`



Inbound rules:



\- HTTP TCP 80 from `0.0.0.0/0`

\- HTTPS TCP 443 from `0.0.0.0/0`



This security group represents the internet-facing web tier.



\### Private Application Security Group



Security group:



`private-app-sg`



Inbound rule:



\- Custom TCP 8080

\- Source: `public-web-sg`



This means the private application tier only accepts TCP 8080 traffic from resources associated with the public web security group.



Direct SSH access was not allowed.



\## EC2 Validation Instances



Two temporary EC2 instances were launched to validate the architecture.



\### Public Test Instance



\- Name: `public-web-test`

\- AMI: Amazon Linux 2023

\- Instance type: `t3.micro`

\- Subnet: `public-subnet-1`

\- Security group: `public-web-sg`

\- Public IPv4: Assigned



\### Private Test Instance



\- Name: `private-app-test`

\- AMI: Amazon Linux 2023

\- Instance type: `t3.micro`

\- Subnet: `private-subnet-1`

\- Security group: `private-app-sg`

\- Public IPv4: None

\- Private IPv4: `10.20.2.36`



The private EC2 instance successfully launched without a public IP address, confirming that it was not directly internet-facing.



\## Reachability Analyzer Validation



AWS VPC Reachability Analyzer was used to validate allowed and blocked traffic between the two EC2 instances.



\### Test 1: TCP 8080



Path:



`public-web-test` → `private-app-test`



Protocol:



`TCP`



Destination port:



`8080`



Result:



`Reachable`



This confirmed that the public tier could communicate with the private application tier on the explicitly approved application port.



The path analysis showed the private security group permitting inbound TCP 8080 from the referenced public security group.



\### Test 2: TCP 22



Path:



`public-web-test` → `private-app-test`



Protocol:



`TCP`



Destination port:



`22`



Result:



`Not reachable`



AWS identified the blocking condition as:



`ENI\_SG\_RULES\_MISMATCH`



This confirmed that no matching security group ingress rule existed for SSH traffic to the private application instance.



The result validated that the private tier was not broadly accessible from the public tier.



\## Security Outcome



The completed environment demonstrated:



\- Network segmentation between public and private tiers

\- No direct public IP assigned to the private EC2 instance

\- Controlled internet access through an Internet Gateway

\- Separate route tables for public and private subnets

\- Security-group-based east-west traffic control

\- Application access restricted to TCP 8080

\- SSH traffic blocked between tiers

\- Successful validation using AWS Reachability Analyzer

\- Principle of least privilege applied to network access



\## Cleanup



After validation was complete, both temporary EC2 test instances were terminated:



\- `public-web-test`

\- `private-app-test`



The VPC architecture and security configuration were retained for documentation and portfolio purposes.



\## Key Takeaways



This lab reinforced several AWS networking and cloud security concepts:



1\. Public and private subnets are defined by routing behavior, not only by naming.

2\. A private EC2 instance should not require a public IPv4 address for internal application communication.

3\. Security groups can reference other security groups instead of using broad IP-based rules.

4\. Least privilege should be applied to network traffic just as it is applied to IAM permissions.

5\. AWS Reachability Analyzer provides strong evidence that intended traffic paths are allowed and unintended traffic paths are blocked.

6\. Validation is an important part of cloud security engineering. A secure design should be tested rather than assumed.



\## Technologies Used



\- Amazon VPC

\- Amazon EC2

\- AWS Security Groups

\- AWS Route Tables

\- AWS Internet Gateway

\- AWS VPC Reachability Analyzer

\- Amazon Linux 2023

\- AWS Management Console



\## Portfolio Relevance



This project demonstrates practical cloud security skills related to:



\- AWS networking

\- Network segmentation

\- Security group design

\- Least privilege

\- Cloud infrastructure validation

\- Secure multi-tier architecture

\- Troubleshooting network paths

\- AWS security engineering

