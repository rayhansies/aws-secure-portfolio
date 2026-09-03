\# AWS IAM Security Lab



\## Overview



This project demonstrates secure AWS Identity and Access Management design using least-privilege permissions, IAM groups, multi-factor authentication, and controlled access to Amazon S3.



The objective was to create a restricted IAM identity that could read content from a specific S3 bucket while being prevented from modifying the environment.



\## Architecture



IAM User  

↓  

IAM Security Lab Group  

↓  

Custom Least-Privilege Policy  

↓  

Amazon S3



\## IAM Resources



\### IAM User



`iam-security-lab-user`



The user was configured with:



\- AWS Management Console access

\- Multi-factor authentication

\- No AWS access keys

\- Permissions inherited through an IAM group



\### IAM Group



`IAM-Security-Lab-Group`



The user receives permissions through this group rather than through policies attached directly to the user.



\### Custom Policy



`IAM-Security-Lab-ReadOnly-S3`



The customer-managed policy provides limited access to the portfolio S3 bucket.



\## Allowed Permissions



The user can:



\- List S3 buckets

\- List objects in the portfolio bucket

\- Read objects from the portfolio bucket



Relevant permissions:



\- `s3:ListAllMyBuckets`

\- `s3:ListBucket`

\- `s3:GetObject`



\## Restricted Permissions



The user cannot:



\- Upload objects

\- Delete objects

\- Modify existing objects

\- Change bucket configuration

\- Modify IAM configuration

\- Create long-term programmatic credentials as part of this lab design



\## Security Controls



\### Least Privilege



Permissions are restricted to only the actions required for read-only S3 access.



\### Group-Based Access



Permissions are assigned through an IAM group rather than directly to the user.



\### Multi-Factor Authentication



MFA is enabled for AWS Management Console authentication.



\### No Access Keys



The IAM user has no long-term AWS access keys.



\### Resource-Level Restrictions



Read access is restricted to:



`amirsaleh-security-portfolio`



\## Validation



\### Successful Test



The IAM user successfully:



\- Signed into AWS using MFA

\- Accessed Amazon S3

\- Listed the portfolio bucket

\- Viewed objects including `index.html`, `css/`, and `js/`



\### Denied Test



The IAM user attempted to upload:



`iam-deny-test.txt`



AWS returned:



`Access denied`



This confirmed that `s3:PutObject` was not permitted and that the least-privilege policy was operating as designed.



\## Skills Demonstrated



\- AWS IAM

\- Least-Privilege Access

\- IAM Users

\- IAM Groups

\- Customer-Managed Policies

\- Policy JSON

\- MFA

\- Amazon S3

\- Access Control Testing

\- Security Validation

