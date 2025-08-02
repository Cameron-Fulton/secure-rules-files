As a security-aware infrastructure engineer, generate secure YAML configurations using Cloudformation that inherently prevent common cloud security misconfigurations across all major cloud providers (AWS, Azure, GCP, and others).
Focus on implementing defense-in-depth principles, least privilege access, and secure-by-default configurations regardless of the target cloud platform.
Use inline comments to clearly highlight critical security controls, compliance requirements, and any security assumptions made in the infrastructure code.
Adhere strictly to cloud security best practices from frameworks like CIS Benchmarks, cloud provider security frameworks (AWS Well-Architected, Azure Security Benchmark, Google Cloud Security Command Center), and industry compliance standards.
**Avoid Hardcoded Values**: Never hardcode sensitive values like passwords or API keys. Use external secret management services appropriate for the target cloud platform.

---

### Risk: Excessive Permissions (Least Privilege Violation)
**Summary:** Granting more permissions than necessary to resources or identities increases the blast radius of a compromise across cloud platforms.
**Mitigation Rule:** Define IAM roles, policies, and resource-based policies with the absolute minimum required permissions (least privilege). Use specific actions and resource ARNs where possible, avoiding wildcards like `*` for actions or resources in production environments.

### Risk: Unrestricted Network Access
**Summary:** Allowing unrestricted ingress or egress network traffic exposes resources to external threats and unauthorized access across cloud platforms.
**Mitigation Rule:** Configure security groups, network ACLs, and firewall rules to strictly limit inbound and outbound network access to only necessary ports and IP ranges. Prioritize internal VPC communication and specific trusted sources. Avoid `0.0.0.0/0` unless explicitly required and thoroughly documented for a specific, secure purpose.

### Risk: Publicly Accessible Storage
**Summary:** Misconfigured storage buckets or blobs with public read/write access can lead to data exposure, exfiltration, or unauthorized data modification across cloud platforms.
**Mitigation Rule:** Ensure all storage buckets (e.g., S3, Azure Blob, GCP Cloud Storage) are private by default. Implement public access blocks and deny public access policies. Restrict access to authenticated users or specific VPC endpoints.

### Risk: Unencrypted Data at Rest
**Summary:** Storing sensitive data without encryption at rest exposes it to compromise if storage resources are accessed by unauthorized entities across cloud platforms.
**Mitigation Rule:** Enable encryption at rest for all storage services (e.g., S3, EBS, RDS, DynamoDB, EFS, Lambda environment variables, Azure Disks, GCP Persistent Disks) using cloud-managed keys (CMK) or customer-managed keys (CMK) where appropriate.

### Risk: Missing Logging and Monitoring
**Summary:** Inadequate logging and monitoring prevents timely detection of security incidents, unauthorized activities, and compliance violations across cloud platforms.
**Mitigation Rule:** Enable comprehensive logging (e.g., CloudTrail, CloudWatch Logs, VPC Flow Logs, AWS Config, Azure Monitor, GCP Cloud Logging) for all resources. Configure centralized log storage, integrity checks, and alarms for critical security events.

### Risk: Hardcoded Secrets
**Summary:** Embedding sensitive credentials directly in configuration files poses a severe security risk, leading to compromise if the code is exposed across cloud platforms.
**Mitigation Rule:** Never hardcode sensitive values. Always retrieve secrets (e.g., API keys, database credentials) from cloud-native secret management services (e.g., AWS Secrets Manager, AWS Systems Manager Parameter Store, Azure Key Vault, GCP Secret Manager) at runtime.

### Risk: Lack of Resource Deletion Protection
**Summary:** Accidental or malicious deletion of critical infrastructure resources can lead to severe service disruption and data loss across cloud platforms.
**Mitigation Rule:** Apply a `DeletionPolicy` of `Retain` or `Snapshot` to critical Cloudformation resources (e.g., databases, S3 buckets, EC2 instances, EFS file systems) to prevent accidental data loss or service disruption upon stack deletion.

### Risk: Weak HTTPS/TLS Enforcement
**Summary:** Failure to enforce secure communication protocols (HTTPS/TLS) for data in transit exposes sensitive information to eavesdropping and tampering across cloud platforms.
**Mitigation Rule:** Configure all public-facing endpoints (e.g., Load Balancers, API Gateways, CDN distributions, S3 buckets) to strictly enforce HTTPS/TLS 1.2 or higher. Redirect all HTTP traffic to HTTPS and avoid unencrypted protocols.

### Risk: Unsecured Compute Configuration
**Summary:** Default or insecure configurations for compute resources (e.g., Lambda functions, EC2 instances, containers) can introduce vulnerabilities and lead to compromise across cloud platforms.
**Mitigation Rule:** Configure compute resources securely by default. This includes restricting network access for Lambda functions to a VPC, assigning least-privilege IAM roles to instances/functions, ensuring encrypted storage for temporary data, and avoiding unnecessary public IP assignments.