
## Amazon EC2 Documentation  

## 1. Overview of Amazon EC2  
Amazon Elastic Compute Cloud (EC2) provides scalable compute capacity in the cloud. It is designed to make web-scale cloud computing easier for developers by providing virtual servers, known as instances, that can be configured, launched, and managed easily.

---

## 2. EC2 Purchasing Options  

### Spot Instances  
- **Best for:** Non-critical workloads and batch processing.  
- **Biggest Discount:** Spot instances provide up to 90% discount compared to On-Demand pricing.  
- **Limitation:** Spot instances can be terminated by AWS with a two-minute warning when the capacity is needed for other purposes.  
- **Example Use Case:** Running a data analysis batch job or CI/CD workloads.

---

### Reserved Instances (RIs)  
- **Best for:** Long-term workloads, such as applications running continuously.  
- **Reservation Period:** You can reserve an instance for **1 year or 3 years** to benefit from significant cost savings compared to On-Demand pricing.  
- **Payment Options:**  
  - All upfront  
  - Partial upfront  
  - No upfront  
- **Example Use Case:** Hosting a production server for a web application.

---

### Dedicated Hosts  
- **Best for:** Compliance-driven workloads or workloads that require physical server visibility and control.  
- **Visibility:** Allows visibility into the underlying physical cores and network sockets.  
- **Custom Licensing:** Supports the use of server-bound software licenses (e.g., Oracle databases).  
- **Example Use Case:** Running licensed database software on dedicated hardware for compliance purposes.

---

### On-Demand Instances  
- **Best for:** Short-term or unpredictable workloads that require flexibility.  
- **Pricing:** Pay for compute capacity by the hour or second without long-term commitments.  
- **Example Use Case:** Development and testing environments.

---

## 3. Managing Traffic for EC2 Instances  

To control inbound and outbound traffic, use **Security Groups**:  
- **Description:** Virtual firewalls that control traffic for EC2 instances.  
- **Configuration:**  
  - Inbound rules define the traffic allowed to reach the instance.  
  - Outbound rules define the traffic allowed to leave the instance.  
- **Fact:** Security Groups can be attached to **multiple EC2 instances**, not just one.  

**Example:**  
Allow SSH traffic to your instance:  
```bash
Type: SSH
Protocol: TCP
Port Range: 22
Source: Your IP address (e.g., 203.0.113.0/24)
```

---

## 4. EC2 Instance Types for Specific Workloads  

### High-Performance Computing (HPC)  
- **Recommended Type:** **Compute Optimized (C5) instances** or instances with Elastic Fabric Adapter (EFA) for HPC workloads.  
- **Example Use Case:** Scientific simulations, financial modeling, or big data analysis.

### In-Memory Database  
- **Recommended Type:** **Memory Optimized (R5/R6) instances** are ideal for critical applications using in-memory databases like Redis or Memcached.  
- **Example Use Case:** Real-time analytics or caching.

### High-Frequency OLTP Database  
- **Recommended Type:** **Storage Optimized (I3/I4) instances** are designed for high-speed transactions and OLTP workloads.  
- **Example Use Case:** Migrating an OLTP database for an e-commerce platform.

---

## 5. Automating EC2 Instance Initialization  

When launching EC2 instances that require software installation or OS package updates:  
- **Solution:** Use **User Data scripts** during instance launch.  
- **How it Works:** User Data scripts are executed during the instance's first boot.  
- **Example Script:** Installing Apache HTTP Server on launch:  
  ```bash
  #!/bin/bash
  yum update -y
  yum install -y httpd
  systemctl start httpd
  systemctl enable httpd
  ```

---

## 6. Migration with Compliance Requirements  

For migrating on-premises applications with strict compliance requirements:  
- **Recommended Option:** Use **Dedicated Hosts**.  
- **Advantages:**  
  - Provides physical server isolation.  
  - Supports custom server-bound licensing.  
- **Example Use Case:** Enterprise-grade applications requiring compliance with government or industry regulations.

---

## 7. Summary of EC2 Use Cases  

| **Use Case**                                  | **Instance Type/Option**     | **Details**                                                                 |
|-----------------------------------------------|------------------------------|-----------------------------------------------------------------------------|
| Batch processing, non-critical jobs           | Spot Instances               | Up to 90% discount, may be terminated by AWS.                              |
| Long-term, continuous workloads               | Reserved Instances (RIs)     | Cost-effective for 1-3 year commitments.                                   |
| High-Performance Computing (HPC)             | Compute Optimized (C5)       | Designed for HPC workloads.                                                |
| In-memory database (Redis, Memcached)         | Memory Optimized (R5/R6)     | High memory-to-CPU ratio.                                                  |
| High-frequency OLTP databases                 | Storage Optimized (I3/I4)    | High-speed transactions and OLTP workloads.                                |
| Compliance-driven workloads                   | Dedicated Hosts              | Provides server isolation and supports custom licensing.                   |
| Short-term, unpredictable workloads           | On-Demand Instances          | No upfront payment; pay-as-you-go.                                         |

---

## 8. Additional Information  

- **Elastic Load Balancing (ELB):** Distributes incoming application traffic across multiple EC2 instances for high availability.  
- **Elastic IPs:** Use Elastic IPs for consistent public IPs that can be reassigned if an instance fails.  
- **CloudWatch:** Monitor EC2 instances with CloudWatch for metrics such as CPU usage, disk activity, and network traffic.  
- **Auto Scaling:** Dynamically adjusts the number of instances based on demand.  

---

## 9. AWS CLI Example: Launching an EC2 Instance  

```bash
aws ec2 run-instances \
    --image-id ami-0abcdef1234567890 \
    --count 1 \
    --instance-type t2.micro \
    --key-name MyKeyPair \
    --security-group-ids sg-903004f8 \
    --subnet-id subnet-6e7f829e \
    --user-data file://setup-script.sh
```

In this example:  
- `ami-0abcdef1234567890`: Amazon Machine Image (AMI) ID.  
- `t2.micro`: Instance type.  
- `setup-script.sh`: A file containing the User Data script.  

---

By leveraging these features and options, you can optimize your Amazon EC2 usage for cost-efficiency, scalability, and performance.
