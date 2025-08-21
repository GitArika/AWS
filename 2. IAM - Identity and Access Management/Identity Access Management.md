IAM (Identity Access Management) refers to a set of policies, process and technologies for identity management and access control for users and resources within an organization.

The mainly goal is to allow or deny a identity to access a service.

### IAM Components

1. **Identity management**: Create, modify or exclude identity access rules for services.
2. **Authentication**: Verify the identity of a user or device before granting access to resources. This typically involves methods such as passwords, fingerprint, or multi-factor authentication.
3. **Authorization**: Define which resources and information a user can access after authentication. It is based on access policies and permissions.
4. **Auditing and Reporting**: Monitoring and recording access activities for compliance and security analysis.

### Benefits of IAM

- **Enhanced Security**: Reduces the risk of unauthorized access and data breaches.
- **Compliance**: Helps meet regulatory and security standards.
- **Operational Efficiency**: Automates access management processes, reducing administrative burden.
- **User Experience**: Simplifies the login and access process for end users.

IAM is essential for protecting sensitive information and ensuring the integrity and confidentiality of data in corporate environments.
  

#### **1. Create an IAM User**

**Scenario**: You want to create a developer account that only has access to an S3 bucket named dev-logs.  

**Steps:**

1. Log in to the AWS Management Console.

2. Navigate to **IAM** > **Users** > **Add User**.

3. Enter the username developer and select **Programmatic Access**.

4. Attach a custom policy (JSON shown below).

**Example Policy (Grant Access to Specific S3 Bucket):**


```json
{

  "Version": "2012-10-17",

  "Statement": [

    {

      "Effect": "Allow",

      "Action": "s3:*",

      "Resource": [

        "arn:aws:s3:::dev-logs",

        "arn:aws:s3:::dev-logs/*"

      ]

    }

  ]

}
```


This policy allows the user developer to perform any action (s3:*) on the bucket dev-logs.  

####  **2. Create an IAM Group**

**Scenario**: You want a group of users (e.g., the “Developers” team) to have read-only access to all S3 buckets.

**Steps:**

1. Go to **IAM** > **Groups** > **Create Group**.

2. Name the group Developers.

3. Attach a managed policy like **AmazonS3ReadOnlyAccess**.

**Managed Policy Example:**

Amazon provides a pre-defined policy named AmazonS3ReadOnlyAccess:  
```json
{

  "Version": "2012-10-17",

  "Statement": [

    {

      "Effect": "Allow",

      "Action": [

        "s3:Get*",

        "s3:List*"

      ],

      "Resource": "*"

    }

  ]

}
```
  
**Usage:**

• Add individual IAM users (alice, bob) to the Developers group. All members inherit read-only permissions for S3 buckets.

#### **3. Define Custom IAM Policies**

**Scenario**: Grant a specific user access to start and stop EC2 instances in a particular region.

**Example Policy (EC2 Instance Management in us-east-1):**

```json
{

  "Version": "2012-10-17",

  "Statement": [

    {

      "Effect": "Allow",

      "Action": [

        "ec2:StartInstances",

        "ec2:StopInstances"

      ],

      "Resource": "arn:aws:ec2:us-east-1:123456789012:instance/*"

    }

  ]

}
```
  
**Explanation:**

• This policy allows the user to start/stop EC2 instances only in the us-east-1 region and for the specific account 123456789012.
  

#### **4. Combine Users, Groups, and Policies**

**Scenario**: Create a team with two roles:

1. **Admin**: Full access to AWS resources.

2. **Auditor**: Read-only access to resources.

**Steps:**

1. Create two groups: Admins and Auditors.

2. Attach policies:

• Admins group: Use the AdministratorAccess managed policy.

• Auditors group: Use the ReadOnlyAccess managed policy.

3. Add users to appropriate groups:

• Add john.doe to Admins.

• Add jane.smith to Auditors.  

#### **Best Practices**

1. **Use Groups for Permissions**: Instead of assigning policies to users directly, attach policies to groups for better management.

2. **Grant Least Privilege**: Start with minimal permissions and expand as needed.

3. **Enable MFA**: Require Multi-Factor Authentication for users with access to sensitive resources.

4. **Audit Regularly**: Use AWS IAM Access Analyzer to review unused permissions and tighten access.

#### **Testing Policies**

You can validate your policies using the **IAM Policy Simulator**:

1. Navigate to the **Policy Simulator** in the AWS Management Console.

2. Test actions (e.g., ec2:StartInstances) for your users or groups to ensure permissions are working as expected.


### **Roles Documentation**

This documentation explains IAM Roles and their usage in real-world scenarios. IAM Roles are essential for granting temporary permissions to resources, services, or external identities without using long-term credentials.

#### **What is an IAM Role?**

An IAM Role is an AWS identity with specific permissions that can be assumed by:

• AWS services (e.g., EC2, Lambda).

• Users from another AWS account.

• Federated identities (e.g., via SAML, OpenID Connect).

#### **Key Differences Between Roles and Users:**

• Roles do not have long-term credentials.

• Users authenticate with credentials, while roles are assumed temporarily using **STS (Security Token Service)**.

#### **Real-World Examples**

**1. Role for EC2 to Access S3**

**Scenario**: An application running on an EC2 instance needs to upload logs to an S3 bucket (my-app-logs).

**Steps:**

1. **Create an IAM Role:**

• Go to **IAM** > **Roles** > **Create Role**.

• Select **AWS Service** > **EC2** as the trusted entity.

• Attach a policy that grants write access to the S3 bucket.

2. **Attach the Role to an EC2 Instance:**

• Navigate to **EC2** > **Instances**.

• Select your instance, click **Actions** > **Security** > **Modify IAM Role**.

• Attach the created role.


**Example Policy for the Role:**  

```json
{

  "Version": "2012-10-17",

  "Statement": [

    {

      "Effect": "Allow",

      "Action": "s3:PutObject",

      "Resource": "arn:aws:s3:::my-app-logs/*"

    }

  ]

}
```
  

**Code Example (Python using Boto3):**

  
```python
import boto3

  

s3 = boto3.client('s3')

s3.upload_file('/var/log/my-app.log', 'my-app-logs', 'my-app.log')
```
  
Here, the EC2 instance automatically assumes the role and gets temporary credentials to upload the log file.

**2. Cross-Account Access**

**Scenario**: Grant users in AWS Account B access to read objects in a bucket in AWS Account A.

**Steps:**

1. **Create a Role in Account A:**

• Go to **IAM** > **Roles** > **Create Role**.

• Select **Another AWS Account** as the trusted entity.

• Enter the account ID of Account B.

• Attach an S3 read-only policy.

2. **Example Trust Policy for the Role**:
  
```json
{

  "Version": "2012-10-17",

  "Statement": [

    {

      "Effect": "Allow",

      "Principal": {

        "AWS": "arn:aws:iam::ACCOUNT-B-ID:root"

      },

      "Action": "sts:AssumeRole"

    }

  ]

}
```

3. **Policy for S3 Read-Only Access**:

```json
{

  "Version": "2012-10-17",

  "Statement": [

    {

      "Effect": "Allow",

      "Action": "s3:GetObject",

      "Resource": "arn:aws:s3:::my-shared-bucket/*"

    }

  ]

}
```
  
4. **Assume the Role in Account B**:

• Use the **STS AssumeRole API** to get temporary credentials.

**Code Example (Account B):**  

```python
import boto3

  

sts = boto3.client('sts')

  

response = sts.assume_role(

    RoleArn="arn:aws:iam::ACCOUNT-A-ID:role/CrossAccountS3Access",

    RoleSessionName="SessionFromAccountB"

)

  

credentials = response['Credentials']

  

# Use the temporary credentials to access the S3 bucket

s3 = boto3.client(

    's3',

    aws_access_key_id=credentials['AccessKeyId'],

    aws_secret_access_key=credentials['SecretAccessKey'],

    aws_session_token=credentials['SessionToken']

)

  

response = s3.list_objects(Bucket='my-shared-bucket')

print(response)
```

**3. Lambda Function Role**

**Scenario**: A Lambda function needs access to read data from DynamoDB.

**Steps:**

1. **Create an IAM Role for Lambda:**

• Go to **IAM** > **Roles** > **Create Role**.

• Select **AWS Service** > **Lambda** as the trusted entity.

• Attach a policy to allow DynamoDB read access.

2. **Example Policy for DynamoDB Read-Only Access**:

  
```json
{

  "Version": "2012-10-17",

  "Statement": [

    {

      "Effect": "Allow",

      "Action": "dynamodb:GetItem",

      "Resource": "arn:aws:dynamodb:us-east-1:123456789012:table/MyTable"

    }

  ]

}
```

3. **Assign the Role to the Lambda Function:**

• Navigate to **Lambda** > Your function > **Configuration** > **Permissions**.

• Assign the created role to the Lambda function.

  

**Example Lambda Code:**

```python
import boto3

  

dynamodb = boto3.client('dynamodb')

  

response = dynamodb.get_item(

    TableName='MyTable',

    Key={

        'PK': {'S': '123'}

    }

)

print(response)
```

**Best Practices**

1. **Use Least Privilege**:

• Define roles with only the permissions necessary for the task.

2. **Monitor Role Usage**:

• Use AWS CloudTrail to track when and how roles are assumed.

3. **Avoid Hardcoding Credentials**:

• Always use roles to grant temporary access instead of embedding long-term credentials in code.

4. **Enable Role Session Policies**:

• Use session policies to further restrict permissions when roles are assumed.