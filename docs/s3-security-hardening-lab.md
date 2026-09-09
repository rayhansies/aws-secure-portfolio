\# AWS S3 Security Hardening Lab



\## Project Overview



This lab demonstrates how to harden an Amazon S3 bucket using private-by-default access controls, encryption, versioning, and least-privilege validation.



The goal was to build a dedicated S3 security lab bucket, verify that objects could not be accessed publicly, confirm that object versions were preserved, and test whether a limited IAM user could access or modify the hardened bucket.



\## Environment



\- AWS Region: `us-east-1`

\- Bucket name: `amirsaleh-s3-security-hardening-lab`

\- Bucket type: General purpose

\- Object Ownership: Bucket owner enforced

\- ACLs: Disabled

\- Block Public Access: Enabled

\- Versioning: Enabled

\- Default encryption: SSE-S3



\## Security Configuration



\### Block Public Access



Amazon S3 Block Public Access was enabled for the bucket.



All public access controls remained enabled to prevent accidental exposure through:



\- ACLs

\- Bucket policies

\- Access point policies



This ensured that the bucket remained private by default.



\## Object Ownership



Object Ownership was configured as:



`Bucket owner enforced`



This disabled ACL-based access control and ensured that access decisions were handled through IAM and bucket policies.



\## Bucket Policy



No public bucket policy was configured.



The bucket remained private and did not contain any policy granting public access.



\## Default Encryption



Default server-side encryption was enabled using:



`SSE-S3`



This ensured that new objects uploaded to the bucket were automatically encrypted at rest using Amazon S3 managed keys.



\## Versioning



Bucket versioning was enabled.



A test file named:



`security-test.txt`



was uploaded to the bucket.



The file was then modified locally and uploaded again using the same object name.



With `Show versions` enabled, Amazon S3 displayed two separate object versions with unique Version IDs.



This confirmed that versioning preserved previous object states rather than permanently overwriting the original object.



\## Public Access Validation



The direct object URL for `security-test.txt` was opened from an unauthenticated browser session.



The request returned:



`AccessDenied`



This confirmed that knowing the object URL alone was not sufficient to retrieve the file.



The test validated that:



\- Public access remained blocked

\- No public ACL was available

\- No public bucket policy granted access

\- The object remained private



\## Least-Privilege IAM Validation



The existing limited IAM user:



`iam-security-lab-user`



was used to test access to the hardened bucket.



The user did not have permissions for:



`amirsaleh-s3-security-hardening-lab`



When the IAM user attempted to open the bucket, Amazon S3 returned:



`Insufficient permissions to list objects`



The console specifically indicated that the user lacked:



`s3:ListBucket`



The Upload control was also unavailable to the limited user.



No additional permissions were granted during the test.



This validated that permissions previously granted to the IAM user for another bucket did not automatically provide access to this new bucket.



\## Security Outcomes



The completed lab demonstrated:



\- S3 Block Public Access enabled

\- ACLs disabled

\- Bucket owner enforced

\- No public bucket policy

\- SSE-S3 default encryption

\- Bucket versioning

\- Multiple object versions preserved

\- Direct public object access denied

\- Limited IAM user denied bucket listing

\- Limited IAM user unable to upload objects

\- Bucket-specific least-privilege access control



\## Security Analysis



This lab demonstrates a defense-in-depth approach to S3 security.



Multiple controls work together:



`Block Public Access`

prevents accidental public exposure.



`Bucket owner enforced`

removes ACL-based permission management.



`SSE-S3`

provides automatic encryption at rest.



`Versioning`

helps protect data against accidental overwrite or deletion.



`IAM least privilege`

ensures users only have access to explicitly authorized buckets and actions.



The result is a private S3 environment that limits unauthorized access while preserving object history and encrypting stored data.



\## Key Takeaways



1\. S3 buckets should remain private unless public access is explicitly required.

2\. Block Public Access provides an important safeguard against accidental exposure.

3\. Disabling ACLs simplifies access control by relying on policies instead.

4\. Default encryption protects new objects at rest automatically.

5\. Versioning protects against accidental overwrites by preserving prior object states.

6\. IAM permissions should be scoped to specific buckets and actions.

7\. A user with access to one S3 bucket should not automatically have access to another.

8\. Security controls should be validated through real access tests.



\## Technologies Used



\- Amazon S3

\- AWS Identity and Access Management

\- S3 Block Public Access

\- S3 Object Ownership

\- S3 Versioning

\- SSE-S3 encryption

\- AWS Management Console

\- IAM least-privilege policies



\## Portfolio Relevance



This project demonstrates practical cloud security skills related to:



\- Cloud data protection

\- S3 security configuration

\- Encryption at rest

\- Access control

\- Least privilege

\- Public exposure prevention

\- Object versioning

\- Security validation

\- AWS storage hardening

