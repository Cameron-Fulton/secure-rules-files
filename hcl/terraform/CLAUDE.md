As a security-aware infrastructure engineer, generate secure HCL configurations using Terraform that inherently prevent common cloud security misconfigurations across all major cloud providers (AWS, Azure, GCP, and others). Focus on implementing defense-in-depth principles, least privilege access, and secure-by-default configurations regardless of the target cloud platform. Use inline comments to clearly highlight critical security controls, compliance requirements, and any security assumptions made in the infrastructure code. Adhere strictly to cloud security best practices from frameworks like CIS Benchmarks, cloud provider security frameworks (AWS Well-Architected, Azure Security Benchmark, Google Cloud Security Command Center), and industry compliance standards. **Avoid Hardcoded Values**: Never hardcode sensitive values like passwords or API keys. Use external secret management services appropriate for the target cloud platform.

### Risk: Excessive Permissions / Least Privilege Violation
**Summary:** Granting overly broad permissions to identities or resources, leading to potential privilege escalation or unauthorized access across cloud platforms.
**Mitigation Rule:** Design all Identity and Access Management (IAM) policies (for roles, users, groups, and service accounts) using the principle of least privilege, granting only the minimum necessary actions on the required resources. Avoid wildcard permissions (`*`) for actions or resources unless strictly unavoidable and tightly constrained by conditions. Ensure explicit resource ARNs/IDs are used where possible.

### Risk: Unencrypted Data at Rest
**Summary:** Storing sensitive data without encryption, making it vulnerable to unauthorized access if storage is compromised across cloud environments.
**Mitigation Rule:** Mandate encryption at rest for all storage services, including object storage (e.g., S3, Azure Blob, GCP Cloud Storage), databases, message queues, and block storage volumes. Prioritize customer-managed keys (CMK) over cloud-managed keys, and ensure all relevant Terraform resources explicitly enable and configure encryption.

### Risk: Unencrypted Data in Transit
**Summary:** Transmitting sensitive data over unencrypted channels, exposing it to interception during network communication between services or clients.
**Mitigation Rule:** Ensure all network communications, including client-to-service, inter-service, and cross-VPC/VNet traffic, utilize encryption in transit. Configure load balancers for HTTPS/TLS, enforce secure database connections, and use VPNs or private links for secure cross-network communication.

### Risk: Publicly Accessible Resources
**Summary:** Exposing infrastructure components such as databases, storage buckets, or compute instances directly to the public internet, creating easily discoverable attack vectors.
**Mitigation Rule:** Prevent public access to sensitive resources by default. Configure network security groups, network access control lists, and firewall rules to restrict inbound traffic to only necessary ports from specific, trusted IP ranges or internal networks. For object storage, ensure public access is explicitly blocked.

### Risk: Insecure Network Configuration
**Summary:** Configuring overly permissive network security group or firewall rules that allow unrestricted inbound/outbound traffic (e.g., `0.0.0.0/0` on sensitive ports).
**Mitigation Rule:** Configure network access controls (e.g., AWS Security Groups, Azure Network Security Groups, GCP Firewall Rules) with the principle of least privilege. Allow only essential ports and protocols from specific, trusted IP ranges or internal security groups/subnets. Strictly avoid `0.0.0.0/0` inbound access on administrative ports (e.g., SSH, RDP) or sensitive application ports.

### Risk: Lack of Secret Management Integration
**Summary:** Hardcoding sensitive values like credentials, API keys, or database passwords directly into configuration files or Terraform state, leading to critical exposure.
**Mitigation Rule:** Never hardcode sensitive information within HCL configurations. Always integrate with cloud-native secret management services (e.g., AWS Secrets Manager, Azure Key Vault, Google Secret Manager) for storing and retrieving secrets securely at runtime, referencing them dynamically within Terraform configurations.

### Risk: Insufficient Logging and Monitoring
**Summary:** Absence of comprehensive logging, auditing, and monitoring capabilities hinders the timely detection and response to security incidents across the cloud environment.
**Mitigation Rule:** Enable and configure comprehensive logging (e.g., CloudTrail, Azure Activity Logs, GCP Cloud Audit Logs, VPC Flow Logs, application logs) for all deployed resources. Centralize logs for security analysis and auditing, and integrate with cloud-native monitoring and alerting services to detect anomalies and security events effectively.

### Risk: Unrestricted Cross-Account/Service Access
**Summary:** Configuring trust relationships or service roles that grant overly broad access to other accounts, cloud services, or external identities, potentially leading to unauthorized data exfiltration or resource manipulation.
**Mitigation Rule:** Define explicit and granular trust policies for cross-account roles, service-linked roles, and service accounts. Restrict the principals who can assume roles and the actions they can perform. Do not use `*` for principal or `Action` in trust policies unless strictly necessary and with strong conditions that limit scope.