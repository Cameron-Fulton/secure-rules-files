- As a security-aware infrastructure engineer, generate secure YAML configurations using Cloudformation that inherently prevent common cloud security misconfigurations across all major cloud providers (AWS, Azure, GCP, and others).
- Focus on implementing defense-in-depth principles, least privilege access, and secure-by-default configurations regardless of the target cloud platform.
- Use inline comments to clearly highlight critical security controls, compliance requirements, and any security assumptions made in the infrastructure code.
- Adhere strictly to cloud security best practices from frameworks like CIS Benchmarks, cloud provider security frameworks (AWS Well-Architected, Azure Security Benchmark, Google Cloud Security Command Center), and industry compliance standards.
- **Avoid Hardcoded Values**: Never hardcode sensitive values like passwords or API keys. Use external secret management services appropriate for the target cloud platform (e.g., AWS Secrets Manager, AWS SSM Parameter Store SecureString, Azure Key Vault, Google Cloud Secret Manager).

## Top Infrastructure Security Risks for YAML + Cloudformation

### Risk: Excessive Permissions (IAM)
**Summary:** Over-privileged roles, users, or resource policies grant more access than necessary, leading to potential unauthorized actions and data exposure across all cloud platforms.
**Mitigation Rule:** Enforce the principle of least privilege. Define IAM policies (AWS::IAM::Policy, AWS::IAM::Role) with the most restrictive permissions required for the resource or service's function. Avoid wildcard `*` actions and resources unless absolutely necessary and justified. Utilize condition keys to further scope permissions based on context (e.g., IP address, time of day, source VPC). Prefer attribute-based access control (ABAC) where applicable.

### Risk: Unencrypted Data at Rest
**Summary:** Storing data without encryption in services like object storage, databases, or block storage volumes exposes sensitive information if data stores are compromised.
**Mitigation Rule:** Mandate encryption at rest for all data storage resources (e.g., AWS::S3::Bucket, AWS::RDS::DBInstance, AWS::EC2::Volume, Azure Storage Accounts, GCP Storage Buckets). Configure server-side encryption with cloud-provider managed keys (KMS, Azure Key Vault, GCP KMS) or customer-managed keys (CMK) by default. Ensure CloudFormation templates explicitly specify encryption properties and key IDs where applicable.

### Risk: Publicly Accessible Resources
**Summary:** Unintentionally exposing storage buckets, databases, or network endpoints to the public internet creates a direct attack vector for data breaches and unauthorized access.
**Mitigation Rule:** Configure all resources to be private by default. For storage, enable public access blocking (e.g., AWS::S3::Bucket PublicAccessBlockConfiguration). For databases, set `PubliclyAccessible: false`. For network resources, restrict ingress rules (e.g., AWS::EC2::SecurityGroup, Azure Network Security Group, GCP Firewall Rule) to only required source IPs or private networks, avoiding `0.0.0.0/0` for administrative or sensitive ports.

### Risk: Insecure Network Configuration
**Summary:** Overly permissive network ingress/egress rules or lack of network segmentation can facilitate unauthorized access, lateral movement, and data exfiltration within and outside the cloud environment.
**Mitigation Rule:** Implement strict network segmentation using VPCs/VNets/VPC Networks and private subnets. Define network security rules (Security Groups, NSGs, Firewall Rules) with the principle of least privilege, allowing only essential communication between layers and services. Favor private endpoints (AWS PrivateLink, Azure Private Link, GCP Private Service Connect) for inter-service communication over public routes.

### Risk: Improper Secret Referencing
**Summary:** Storing or referencing sensitive values like API keys, database credentials, or private keys directly in CloudFormation templates, source code, or unencrypted parameters leads to critical security vulnerabilities.
**Mitigation Rule:** Never hardcode or embed sensitive secrets directly in CloudFormation YAML templates or in plaintext parameters. Always use cloud-native secret management services (e.g., AWS Secrets Manager, AWS SSM Parameter Store SecureString, Azure Key Vault, Google Cloud Secret Manager) and reference these values securely using dynamic references, `Fn::ImportValue`, or other secure mechanisms supported by CloudFormation.

### Risk: Missing or Inadequate Logging and Monitoring
**Summary:** Lack of comprehensive logging, auditing, and monitoring for cloud resource activities hinders the detection of security incidents, unauthorized access attempts, and compliance violations.
**Mitigation Rule:** Enable and configure robust logging for all services and resources (e.g., AWS CloudTrail, AWS CloudWatch Logs, Azure Monitor Activity Log, GCP Cloud Audit Logs, Cloud Logging). Ensure logs are centrally stored, encrypted, and have sufficient retention policies. Integrate logging with security information and event management (SIEM) systems for real-time threat detection and alerting.

### Risk: Lack of Resource Deletion Protection
**Summary:** Accidental or malicious deletion of critical infrastructure components or data stores can lead to significant downtime, data loss, or service disruption.
**Mitigation Rule:** Enable deletion protection for critical resources (e.g., AWS::RDS::DBInstance `DeletionProtection`, AWS::S3::Bucket `PublicAccessBlockConfiguration` with `BlockPublicDeletes`, AWS::EC2::Instance `DisableApiTermination`, Azure SQL Database `AllowDataDelete` or soft delete options, GCP Cloud SQL `databaseFlags` for deletion protection) where supported, to prevent unintended removal.

### Risk: Misconfigured Web Application Firewalls (WAF)
**Summary:** Failing to deploy or properly configure WAFs for public-facing web applications leaves them vulnerable to common web exploits like SQL injection, cross-site scripting (XSS), and DDoS attacks.
**Mitigation Rule:** Deploy and configure web application firewalls (e.g., AWS::WAFv2::WebACL, Azure WAF, Google Cloud Armor) in front of all internet-facing web applications. Implement rules to mitigate OWASP Top 10 risks, rate-based rules for DDoS protection, and IP reputation lists. Ensure WAF logs are enabled and integrated with monitoring solutions.

### Risk: Insufficient Data Backup and Disaster Recovery
**Summary:** Absence of automated backup strategies and a tested disaster recovery plan for critical data and applications can result in unrecoverable data loss and prolonged outages during incidents.
**Mitigation Rule:** Implement automated and regular backup strategies for all critical data stores (e.g., AWS::RDS::DBInstance `BackupRetentionPeriod`, AWS::EBS::Snapshot schedules, Azure Backup, GCP Cloud SQL backups). Configure cross-region/cross-zone replication where appropriate for disaster recovery. Define and test recovery procedures for all critical applications.