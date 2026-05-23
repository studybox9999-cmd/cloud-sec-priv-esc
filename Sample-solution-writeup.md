# CTF Lab Write-Up: AWS Compromise & Lateral Movement
Player: dev_user (Attacker Perspective)

## 1. Executive Summary
During this laboratory exercise, I assumed the role of an attacker (dev_user) starting from an initial foothold in a public subnet. By methodically enumerating the AWS environment, I identified a critical misconfiguration that allowed unauthorized lateral movement from a limited public IAM user into private subnet resources and sensitive Amazon S3 data storage.

---

## 2. Attack Methodology & Technical Steps

### Step 1: Initial Foothold & Environment Reconnaissance
* I began my assessment as dev_user, an IAM identity residing within the public subnet. 
* Objective: Determine the scope of permissions assigned to this user and identify reachable network boundaries.
* I ran basic enumeration commands to check my identity details and verify active credential permissions:
  aws sts get-caller-identity

### Step 2: Discovery & Control of Private Subnet EC2 Instances
* Using the dev_user credentials, I executed discovery commands to probe for internal infrastructure assets across adjacent subnets:
  aws ec2 describe-instances --query "Reservations[*].Instances[*].{InstanceId:InstanceId,PrivateIpAddress:PrivateIpAddress,State:State.Name}" --output table
* Exploit/Finding: I discovered an EC2 instance residing completely inside the private subnet. Due to an overly permissive IAM policy attached to my current identity, I found that I possessed permissions to modify its operational state.
* Verification: I successfully executed state modification commands against the target private EC2 instance, confirming unauthorized control over isolated private network infrastructure:
  aws ec2 stop-instances --instance-ids <target-private-instance-id>
  aws ec2 start-instances --instance-ids <target-private-instance-id>

### Step 3: Service Endpoint & Storage Enumeration
* With infrastructure control established, I scanned for accessible VPC endpoints and internal storage infrastructure to determine if the instance was mapped to any databases or Amazon S3 buckets.
* I checked for VPC endpoints configured within the network environment to map logical connection paths:
  aws ec2 describe-vpc-endpoints --query "VpcEndpoints[*].{VpcEndpointId:VpcEndpointId,ServiceName:ServiceName,State:State}" --output table
* I then listed all S3 buckets available within the global namespace to see what resources my current credentials could interact with:
  aws s3 ls

### Step 4: Data Exfiltration (S3 Access)
* My enumeration revealed two distinct S3 buckets associated with/accessible from the environment.
* Exploit/Finding: Despite operating with what should have been limited or standard user privileges, I successfully listed the contents of the target sensitive bucket without requiring an administrative role:
  aws s3 ls s3://<target-sensitive-bucket-name>/
* Impact: Inside one of the buckets, I located a sensitive .txt file. I executed a download and read command, confirming unauthorized data access and a total breakdown of data segregation boundaries:
  aws s3 cp s3://<target-sensitive-bucket-name>/sensitive-file.txt .
  cat sensitive-file.txt

### Step 5: Tooling & Reference
* To bridge the gap between technical strategy and syntax implementation, I utilized a structured AWS CLI cheat sheet mapping out proper command sequences.
* Reference Tooling: AWS CLI Cheat Sheet PDF (https://github.com/studybox9999-cmd/AWS-cheet-sheet/blob/main/aws_cli_cheatsheet.pdf)

---

## 3. Post-Incident Reflection & Security Analysis

### Core Lesson Learned
The primary takeaway from this exercise is the compounding risk of small configuration errors. A single loose permission or a minor architectural oversight can trigger a cascade of exploits, allowing a low-privilege actor to breach isolated networks (private subnets) and access confidential data storehouses.

### Technical Deep Dive & Loopholes to Investigate
Moving forward, I intend to conduct a root-cause analysis to pinpoint exactly where the architecture failed. I am investigating two primary loopholes:
1. IAM Policy Flaws: Pinpointing the exact policy block (e.g., ec2:* or overly broad resource statements) that allowed a standard public user to interact with instances inside a private subnet.
2. Network/Role Misconfigurations: Analyzing whether an EC2 Instance Profile or a missing VPC Endpoint policy allowed the unauthorized traversal to the S3 buckets.

### Worst-Case Scenario: What an Actual Hacker Could Do
If a malicious actor penetrated the network to this depth, their actions would extend far beyond data reading:
* Privilege Escalation: Attempting to modify their own IAM user policies or manipulate other IAM roles to gain full Administrator Access.
* Malicious Injection: Uploading malware, ransomware, or malicious code directly into the company’s S3 buckets or databases to infect downstream users or applications.
* Data Theft: Mass-downloading corporate intellectual property, customer data, and proprietary code bases.
* Resource Abuse: Weaponizing the infrastructure to host illegal, live web services, distribute malware, or mine cryptocurrency on the company's budget.

### Conclusion (The Blue Team Perspective)
This lab successfully demonstrated how real-world cloud architectures fail. By thinking like an attacker, I now better understand how to spot these vulnerabilities. As a cloud security professional, I will use this knowledge to implement stricter Least Privilege access models, tighten security groups, and ensure proper network isolation to defend critical enterprise assets.
