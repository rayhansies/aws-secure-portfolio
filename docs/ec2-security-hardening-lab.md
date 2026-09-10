\# EC2 Security Hardening Lab



\## Overview



This lab demonstrates how to deploy and validate a hardened Amazon EC2 instance inside a private AWS subnet without exposing SSH or a public IP address.



The goal was to create a secure administrative access model using AWS Systems Manager Session Manager instead of traditional SSH access.



The instance was deployed in the existing `secure-vpc-lab` environment and configured with hardened storage, metadata protections, IAM permissions, and private VPC endpoints for Systems Manager connectivity.



\## Architecture



The hardened EC2 instance was deployed with the following architecture:



\- VPC: `secure-vpc-lab`

\- Private subnet: `private-subnet-1`

\- Private CIDR: `10.20.2.0/24`

\- EC2 instance: `ec2-security-hardening-lab`

\- Instance private IP: `10.20.2.79`

\- Public IPv4: None

\- Security group: `ec2-hardening-sg`

\- IAM role: `EC2-Security-Hardening-Role`



Administrative access was provided through AWS Systems Manager Session Manager instead of SSH.



Private Systems Manager connectivity was configured using VPC interface endpoints for:



\- `com.amazonaws.us-east-1.ssm`

\- `com.amazonaws.us-east-1.ssmmessages`

\- `com.amazonaws.us-east-1.ec2messages`



Each endpoint was placed in the private subnet and protected by the `ssm-endpoint-sg` security group.



\## Security Controls Implemented



\### Private EC2 Deployment



The instance was launched inside `private-subnet-1` with public IP assignment disabled.



This prevents direct inbound connectivity from the public internet.



\### No SSH Access



No SSH key pair was assigned to the instance.



The `ec2-hardening-sg` security group contains no inbound SSH rule.



Port 22 was intentionally not opened.



Administrative access is performed through AWS Systems Manager Session Manager.



\### IAM Instance Role



The instance uses the IAM role:



`EC2-Security-Hardening-Role`



The role includes:



\- `AmazonSSMManagedInstanceCore`

\- `CloudWatchAgentServerPolicy`



The trust relationship allows the EC2 service to assume the role using:



`ec2.amazonaws.com`



\### IMDSv2 Enforcement



Instance Metadata Service Version 2 was required.



Configuration:



\- Metadata accessible: Enabled

\- Metadata version: V2 only

\- Token required: Yes

\- Metadata response hop limit: 1

\- Instance tags in metadata: Disabled



This reduces exposure to metadata-related attacks and credential theft techniques.



\### Encrypted EBS Storage



The root EBS volume was configured with encryption enabled.



Configuration:



\- Volume type: gp3

\- Size: 8 GiB

\- Encryption: Enabled

\- KMS key: AWS managed EBS key

\- Delete on termination: Enabled



\### SSM Endpoint Security



A dedicated security group named `ssm-endpoint-sg` was created for the Systems Manager VPC endpoints.



Its inbound configuration allows:



\- Protocol: TCP

\- Port: 443

\- Source: `ec2-hardening-sg`



This limits HTTPS access to the SSM endpoints to only the hardened EC2 security group.



\## Private Systems Manager Connectivity



The EC2 instance was intentionally deployed without a public IP and without a NAT gateway.



Private connectivity to AWS Systems Manager was provided through VPC interface endpoints.



The following endpoints were created:



\### SSM Endpoint



Service: `com.amazonaws.us-east-1.ssm`



\### SSM Messages Endpoint



Service: `com.amazonaws.us-east-1.ssmmessages`



\### EC2 Messages Endpoint



Service: `com.amazonaws.us-east-1.ec2messages`



All endpoints were:



\- deployed in `private-subnet-1`

\- configured with Private DNS enabled

\- assigned the `ssm-endpoint-sg` security group



\## Reachability Validation



AWS Reachability Analyzer was used to validate network access between the hardened EC2 instance and the SSM VPC endpoint.



Test path:



`ec2-security-hardening-lab -> SSM endpoint -> TCP 443`



The analysis returned:



`Reachable`



The validated path confirmed:



\- source EC2 instance: `10.20.2.79`

\- destination SSM endpoint: `10.20.2.21`

\- protocol: TCP

\- destination port: 443

\- outbound traffic allowed by `ec2-hardening-sg`

\- inbound HTTPS allowed by `ssm-endpoint-sg`



This proved the private EC2 instance could reach the Systems Manager endpoint without using the public internet.



\## Session Manager Validation



A browser-based AWS Systems Manager Session Manager connection was successfully established to the EC2 instance.



The session opened directly into the private instance without:



\- a public IP address

\- SSH

\- port 22

\- an SSH key pair



The session user was verified with:



Command: `whoami`



Result: `ssm-user`



The host was verified with:



Command: `hostname`



Result: `ip-10-20-2-79.ec2.internal`



\## SSM Agent Validation



The Amazon SSM Agent was confirmed to be installed and running.



The system boot log showed:



`Amazon SSM Agent v3.3.4624.0 is running`



The service state was verified with:



Command: `sudo systemctl is-active amazon-ssm-agent`



Result: `active`



The service startup configuration was verified with:



Command: `sudo systemctl is-enabled amazon-ssm-agent`



Result: `enabled`



\## Security Outcome



The final configuration achieved a hardened administrative access model with:



\- no public IPv4 address

\- no direct internet exposure

\- no inbound SSH

\- no SSH key pair

\- encrypted root storage

\- IMDSv2 enforced

\- IAM role-based permissions

\- private Systems Manager endpoints

\- security-group-restricted endpoint access

\- successful Reachability Analyzer validation

\- successful AWS Systems Manager Session Manager access

\- active and enabled SSM Agent



This demonstrates a more secure alternative to traditional SSH-based administration for private AWS workloads.



\## Skills Demonstrated



\- Amazon EC2

\- Amazon VPC

\- Private subnets

\- IAM roles

\- AWS Systems Manager

\- Session Manager

\- VPC Interface Endpoints

\- AWS PrivateLink

\- Security Groups

\- EBS Encryption

\- IMDSv2

\- Reachability Analyzer

\- Least-privilege access

\- Secure cloud administration

