# Cloudformation Security Codex Rules for LLM-Driven Infrastructure Generation

As a security-aware infrastructure engineer, generate secure YAML configurations using Cloudformation that inherently prevent common cloud security misconfigurations across all major cloud providers (AWS, Azure, GCP, and others). Focus on implementing defense-in-depth principles, least privilege access, and secure-by-default configurations regardless of the target cloud platform. Use inline comments to clearly highlight critical security controls, compliance requirements, and any security assumptions made in the infrastructure code. Adhere strictly to cloud security best practices from frameworks like CIS Benchmarks, cloud provider security frameworks (AWS Well-Architected, Azure Security Benchmark, Google Cloud Security Command Center), and industry compliance standards.

**Avoid Hardcoded Values**: Never hardcode sensitive values like passwords or API keys. Use external secret management services appropriate for the target cloud platform (e.g., AWS Secrets Manager, Azure Key Vault, Google Secret Manager).

---

### Risk: Excessive Permissions
**Summary:** Granting more permissions than necessary to resources or identities leads to broader attack surfaces and privilege escalation risks across cloud platforms.
**Mitigation Rule:** Define IAM policies (AWS IAM, Azure RBAC roles, GCP IAM roles) with explicit `Allow` and `Deny` statements, strictly adhering to the principle of least privilege. Attach policies directly to specific resources or roles, avoiding wildcard permissions (`*`) unless absolutely necessary and justified with inline comments.

### Risk: Unencrypted Data at Rest and In Transit
**Summary:** Storing or transmitting sensitive data without encryption can lead to data breaches if compromised.
**Mitigation Rule:** Enforce server-side encryption by default for all storage services (e.g., S3 Bucket Encryption, EBS Encryption, RDS Encryption, Azure Storage Encryption, GCP Storage Encryption) using managed encryption keys (KMS, Key Vault, Cloud KMS). Require all network traffic between services and to/from endpoints to use TLS 1.2+ for data in transit.

### Risk: Publicly Accessible Resources
**Summary:** Exposing resources like storage buckets, databases, or compute instances to the public internet without justification creates significant attack vectors.
**Mitigation Rule:** Configure all newly provisioned storage buckets, databases, and compute instances to be private by default. For storage services, enable Block Public Access settings (e.g., AWS S3 Block Public Access). Ensure no public IPs are assigned to database instances or critical application servers unless explicitly required and fronted by a Web Application Firewall (WAF).

### Risk: Insecure Network Exposure
**Summary:** Overly permissive network rules or publicly exposed management interfaces can allow unauthorized access to infrastructure.
**Mitigation Rule:** Design networks using private subnets within VPCs/VNets. Implement granular security group (AWS), network security group (Azure), or firewall rules (GCP) to restrict inbound and outbound traffic to only essential ports and source/destination IP ranges. Never expose administrative ports (e.g., SSH 22, RDP 3389) directly to the internet; use bastion hosts, VPNs, or secure access methods.

### Risk: Logging and Monitoring Deficiencies
**Summary:** Lack of comprehensive logging and monitoring can delay detection of security incidents and impede forensic analysis.
**Mitigation Rule:** Mandate the enabling of comprehensive audit logging for all critical CloudFormation-deployed resources (e.g., CloudTrail for AWS, Azure Activity Logs and Diagnostic Settings for Azure, Cloud Audit Logs for GCP). Configure log destinations to secure, centralized logging services, and establish monitoring and alerting for security-relevant events and misconfigurations.

### Risk: Insecure API Gateway/Load Balancer Configuration
**Summary:** Misconfigured API Gateways or Load Balancers can expose backend services, allow unauthenticated access, or fail to enforce proper security controls.
**Mitigation Rule:** Configure all API Gateway endpoints and Load Balancers (ALB, NLB, Azure Application Gateway, GCP Load Balancer) to enforce HTTPS/TLS 1.2+ listener policies. Implement request throttling, integrate with Web Application Firewalls (WAFs) for OWASP Top 10 protection, and apply strong authentication/authorization mechanisms (e.g., IAM authorizers, JWT validation) for all endpoints.

### Risk: Missing Resource Tagging for Governance/Security
**Summary:** Absence of consistent tagging hinders resource identification, cost attribution, and security policy enforcement, leading to governance gaps.
**Mitigation Rule:** Require a standardized set of tags for all deployable resources within CloudFormation templates, including `Environment`, `Application`, `Owner`, `CostCenter`, and `Confidentiality` (e.g., Public, Internal, Confidential). Use tags to facilitate automated policy enforcement, resource identification, and cost allocation.

### Risk: Insecure Service Principals/Managed Identities
**Summary:** Granting overly broad permissions to service accounts or managed identities (used by applications) can lead to horizontal privilege escalation within the cloud environment.
**Mitigation Rule:** When defining service roles (AWS IAM Roles), managed identities (Azure), or service accounts (GCP), apply the principle of least privilege by granting only the minimum necessary permissions required for the application or service to function. Avoid broad permissions like `*` or `Admin` policies, and restrict access to specific resources where possible.

### Risk: Vulnerable Compute Images/Runtimes
**Summary:** Deploying instances or containers with unpatched software vulnerabilities can be exploited by attackers, leading to system compromise.
**Mitigation Rule:** Specify the use of the latest stable and patched machine images (AMIs, Azure VM Images, GCP Compute Images) or container images from trusted, official sources. Avoid using outdated or custom images without a robust patch management process. Prioritize serverless or managed services to offload patching responsibilities where appropriate.