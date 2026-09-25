## Finding: SSH open to the world (Security Group)
**What it was:** An EC2 security group with ingress on port 22 from 0.0.0.0/0.
**How I found it:** tfsec, run against the Terraform config.
**Why it matters:** Open SSH is one of the most common real-world attack
surfaces — automated scanners probe for it constantly.
**Fix:** Restricted ingress to a specific CIDR, re-scanned clean.

## Finding: Publicly accessible S3 bucket
**What it was:** An AWS S3 bucket configured to be publicly readable
by anyone.
**How I found it:** Checkov, run against the Terraform config.
**Why it matters:** Publicly accessible buckets are a common
real-world exposure — automated scanners actively probe for them, and
several well-known breaches have started exactly this way.
**Fix:** Restored the default block-public-access settings and
removed the public bucket policy, then re-scanned clean.

## Control built: Kubernetes RBAC least-privilege access
**What I built:** A ServiceAccount, a Role scoped to read-only pod
access (get/list only), and a RoleBinding attaching the Role to the
ServiceAccount.
**How I verified it:** Ran `kubectl auth can-i list pods --as=...` →
yes, and `kubectl auth can-i delete pods --as=...` → no, confirming
the restriction actually held.
**Why it matters:** Same least-privilege pattern as AWS IAM, applied
to Kubernetes' own permission system — limits the blast radius if
this service account's credentials were ever compromised.