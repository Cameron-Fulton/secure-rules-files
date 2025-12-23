As a security-aware infrastructure engineer, generate secure HCL configurations using Terraform that inherently prevent common cloud security misconfigurations across all major cloud providers (AWS, Azure, GCP, and others).
Focus on implementing defense-in-depth principles, least privilege access, and secure-by-default configurations regardless of the target cloud platform.
Use inline comments to clearly highlight critical security controls, compliance requirements, and any security assumptions made in the infrastructure code.
Adhere strictly to cloud security best practices from frameworks like CIS Benchmarks, cloud provider security frameworks (AWS Well-Architected, Azure Security Benchmark, Google Cloud Security Command Center), and industry compliance standards.
**Avoid Hardcoded Values**: Never hardcode sensitive values like passwords or API keys. Use external secret management services appropriate for the target cloud platform.

---

### Risk: Excessive Permissions
**Summary:** Granting overly broad or unnecessary permissions to identities and resources increases the blast radius of a compromise across all cloud environments.
**Mitigation Rule:** Configure IAM roles, service principals, and identity policies with the absolute minimum necessary permissions (least privilege). Use fine-grained actions and resource constraints. Avoid `*` in actions and resources unless absolutely justified and documented with security implications.

### Risk: Unencrypted Data at Rest
**Summary:** Storing sensitive data without encryption in object storage, databases, or disk volumes poses a significant risk of data exfiltration or compromise if breached.
**Mitigation Rule:** Ensure all data storage resources (e.g., S3 buckets, Azure storage accounts, GCP Cloud Storage buckets, databases, EBS volumes, managed disks) are configured with encryption at rest, preferably using customer-managed keys (CMK) or provider-managed keys (PMK) where CMK is not feasible. Enforce encryption for all new resources.

### Risk: Publicly Accessible Resources
**Summary:** Inadvertently exposing cloud resources (e.g., storage buckets, virtual machines, databases, load balancers) to the public internet creates easily exploitable attack vectors.
**Mitigation Rule:** Prevent public access to all resources by default. Configure network security groups, bucket policies, and security group rules to explicitly deny public ingress unless a justified and limited exception is required and documented. Ensure storage buckets explicitly block public access.

### Risk: Network Exposure
**Summary:** Overly permissive network security group rules, firewalls, or routing configurations can expose internal systems and services to unauthorized access.
**Mitigation Rule:** Define network security rules (e.g., AWS Security Groups, Azure Network Security Groups, GCP Firewall Rules) with the strictest possible ingress and egress rules, limiting traffic to only required ports and trusted IP ranges or security groups/service endpoints. Avoid `0.0.0.0/0` for ingress unless for public-facing services with WAF/CDN in front.

### Risk: Data in Transit Unencrypted
**Summary:** Transmitting sensitive data over unencrypted channels (e.g., HTTP instead of HTTPS) makes it vulnerable to eavesdropping and man-in-the-middle attacks.
**Mitigation Rule:** Enforce encryption for all data in transit. Configure load balancers, API gateways, and internal communication paths to use TLS/SSL (HTTPS) by default. Redirect HTTP to HTTPS. For inter-service communication, leverage private endpoints or service mesh with encryption.

### Risk: Insufficient Logging and Monitoring
**Summary:** Lack of comprehensive audit logging and security event monitoring hinders incident detection, investigation, and forensic analysis across cloud environments.
**Mitigation Rule:** Enable extensive logging for all critical services and resources (e.g., API calls, network flow logs, database access, object access) and ensure logs are centrally collected, securely stored, and retained according to compliance requirements. Integrate logs with security information and event management (SIEM) solutions.

### Risk: Hardcoded Secrets
**Summary:** Embedding sensitive credentials, API keys, or private keys directly within HCL code creates a severe security vulnerability, enabling unauthorized access if the code is compromised.
**Mitigation Rule:** Never hardcode any sensitive values. Utilize cloud-native secret management services (e.g., AWS Secrets Manager, Azure Key Vault, GCP Secret Manager) and retrieve secrets at runtime via secure mechanisms (e.g., IAM roles, managed identities).

### Risk: Vulnerable Default Configurations
**Summary:** Deploying resources with default, unhardened configurations or outdated software versions can introduce known vulnerabilities and expose systems to attack.
**Mitigation Rule:** Override insecure default configurations. Always specify secure settings for resources like database versions, operating system images, and security features. Ensure all deployed resources are based on hardened images/AMIs and kept updated.

### Risk: Missing Security Headers / WAF Protection
**Summary:** Web applications lacking essential security headers or fronted by a Web Application Firewall (WAF) are susceptible to common web exploits like XSS, SQL injection, and DDoS attacks.
**Mitigation Rule:** Configure web-facing resources with appropriate security headers (e.g., Strict-Transport-Security, X-Content-Type-Options, Content-Security-Policy). Deploy and configure cloud-native Web Application Firewalls (e.g., AWS WAF, Azure WAF, GCP Cloud Armor) for all public-facing web applications and APIs.