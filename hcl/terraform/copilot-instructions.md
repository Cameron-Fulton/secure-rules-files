- As a security-aware infrastructure engineer, generate secure HCL configurations using Terraform that inherently prevent common cloud security misconfigurations across all major cloud providers (AWS, Azure, GCP, and others).
- Focus on implementing defense-in-depth principles, least privilege access, and secure-by-default configurations regardless of the target cloud platform.
- Use inline comments to clearly highlight critical security controls, compliance requirements, and any security assumptions made in the infrastructure code.
- Adhere strictly to cloud security best practices from frameworks like CIS Benchmarks, cloud provider security frameworks (AWS Well-Architected, Azure Security Benchmark, Google Cloud Security Command Center), and industry compliance standards.
- **Avoid Hardcoded Values**: Never hardcode sensitive values like passwords or API keys. Use external secret management services appropriate for the target cloud platform.

## Infrastructure Security Best Practices for HCL + Terraform

### Risk: Excessive Permissions / Least Privilege Violation
**Summary:** Granting more permissions than necessary to identities (users, roles, service principals) or resources significantly increases the blast radius of a compromise across all cloud platforms.
**Mitigation Rule:** Configure all IAM policies, roles, and service accounts with the absolute minimum permissions required for their intended function, strictly adhering to the principle of least privilege. Explicitly deny unnecessary actions by default, and avoid wildcard permissions (`*`) unless absolutely unavoidable and justified by inline comments.

### Risk: Unencrypted Data at Rest
**Summary:** Storing sensitive data without encryption in storage services, databases, or disk volumes exposes it to unauthorized access if the underlying infrastructure is compromised, common across all cloud environments.
**Mitigation Rule:** Ensure all data at rest is encrypted by default using server-side encryption with platform-managed keys or customer-managed keys (CMK) for higher control. For storage buckets, databases, and disk volumes, explicitly enable encryption, preferably with CMKs, and enforce it with appropriate bucket policies or database configurations.

### Risk: Publicly Accessible Resources
**Summary:** Exposing sensitive resources like storage buckets, databases, or virtual machines directly to the public internet without proper justification or access controls creates critical attack vectors.
**Mitigation Rule:** Configure all resources to be private by default. For storage services, explicitly block public access settings. For compute instances and databases, deploy them in private subnets and restrict inbound access to specific, necessary IP ranges or other private resources using security groups or network ACLs.

### Risk: Insecure Network Segmentation / Overly Permissive Firewall Rules
**Summary:** Lack of proper network segmentation and overly broad inbound/outbound firewall rules (security groups, network security groups, firewall rules) enable unauthorized lateral movement and expose resources to unnecessary network traffic.
**Mitigation Rule:** Implement robust network segmentation using private subnets, VPCs, or VNets. Restrict inbound and outbound network access using the most granular firewall rules possible, allowing only explicitly required ports and protocols from known, trusted sources. Avoid `0.0.0.0/0` (any) in inbound rules unless for services designed to be publicly accessible, and even then, limit to necessary ports.

### Risk: Lack of Encryption in Transit
**Summary:** Transmitting data between services or to end-users without encryption (e.g., unencrypted HTTP) exposes it to eavesdropping and tampering during transit across various network paths.
**Mitigation Rule:** Enforce encryption in transit for all network communication by utilizing HTTPS/TLS for load balancers, API gateways, and inter-service communication. Configure load balancers to redirect HTTP traffic to HTTPS, and ensure databases and message queues enforce SSL/TLS connections.

### Risk: Insufficient Logging and Monitoring
**Summary:** The absence of comprehensive logging, auditing, and real-time monitoring makes detecting, investigating, and responding to security incidents extremely difficult or impossible across cloud platforms.
**Mitigation Rule:** Enable extensive logging for all cloud resources, including API activity (e.g., CloudTrail, Azure Activity Logs, Cloud Audit Logs), network flow logs (VPC Flow Logs, NSG Flow Logs, VPC Flow Logs), and resource-specific logs (e.g., S3 access logs, database query logs). Route these logs to a centralized logging solution and configure security alerts for anomalous activities.

### Risk: Hardcoded Secrets / Insecure Credentials Management
**Summary:** Embedding sensitive information like API keys, database credentials, or private keys directly within HCL code or configuration files leads to major security breaches if the code repository is compromised.
**Mitigation Rule:** Never hardcode sensitive values directly in Terraform configurations. Utilize cloud-native secret management services (e.g., AWS Secrets Manager, Azure Key Vault, Google Secret Manager) to store and retrieve all sensitive credentials and API keys securely at runtime. Ensure proper IAM policies govern access to these secret stores.

### Risk: Outdated / Unpatched Base Images
**Summary:** Deploying instances or containers based on outdated or unpatched base images introduces known vulnerabilities into the infrastructure, creating exploitable weaknesses before runtime.
**Mitigation Rule:** Always specify the latest secure and patched base images (e.g., AMIs, VM images, container images) provided by cloud providers or trusted sources. Integrate image scanning processes into CI/CD pipelines and reference image IDs or versions known to be secure and up-to-date in Terraform configurations where applicable.