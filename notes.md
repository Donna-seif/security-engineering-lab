## Day 3 — 2026-09-09

**Did:** Created an EC2 security group in Terraform with SSH open to
0.0.0.0/0, scanned it with tfsec, saw it flagged, restricted the
ingress CIDR, re-scanned clean.

**Learned:** tfsec catches open-ingress rules automatically — this is
exactly the kind of finding I'd write up in a real security review.

**Stuck on:** (leave blank if nothing, or note it — e.g. "tfsec install
script needed sudo, had to look up why")

**Next:** Try the same exercise but with an S3 bucket policy instead
of a security group.

## Day 4 — 2026-09-15

**Did:** Worked through authentication vs. authorization,
using a bank-statement URL example to reason through an access control bug.

**Learned:**
- Authentication = confirms who you are. Authorization = confirms what
  you're allowed to do, once identity is confirmed.
- RBAC (role-based access control): design permission templates per
  role, issue copies to whoever holds that role, instead of custom
  grants per person. In AWS this is IAM Groups vs. attaching policies
  to individual users.
- Privilege creep: when access is edited-in-place across role changes
  instead of revoked-and-reissued, permissions silently accumulate.
  Revoke-and-reissue avoids this.
- IDOR (Insecure Direct Object Reference): app returns a resource just
  because a valid-looking ID was supplied, without checking the
  requester actually owns/can access that specific object. Falls under
  OWASP's "Broken Access Control" category (currently #1).
- BOLA (Broken Object Level Authorization): the API-specific name for
  the same bug, #1 on the OWASP API Security Top 10.
- The trap: a role check (`user.role == "customer"`) can look like a
  valid authorization check in a quick code review, while still being
  vulnerable — because it checks the right *category* of user but not
  the right *specific object*. The real fix compares the resource's
  owner field against the authenticated session's own identity.


**Next:** How to actually implement the ownership check in code —
where it belongs and how to avoid missing it on any one endpoint.


## Day 5 — 2026-09-20

**Did:** Created a publicly accessible S3 bucket in Terraform to explore
the infrastructure equivalent of the access-control problem studied
with IDOR — access not being properly restricted, just at the storage
layer instead of the app layer.

**Learned:** Scanned the bucket with checkov, which caught the public
access; fixed it by restoring the account's default access-block
settings (block_public_acls, block_public_policy, ignore_public_acls,
restrict_public_buckets all back to true) and removing the public
bucket policy. Also learned GuardDuty and Security Hub aren't
available on AWS's restricted free account plan — read about what
they do instead of hands-on today.

**Stuck on:** GuardDuty/Security Hub blocked by free account plan
restrictions — deferred, not a blocker for the exercise itself.

**Next:** Continue into Kubernetes security basics (RBAC, network
policies) using the kind cluster from day 1.


## Day 6 — 2026-09-22

**Did:** Created a namespace in my kind cluster, a ServiceAccount, and
a Role + RoleBinding restricting it to read-only pod access (get/list
only, no delete).

**Learned:** Verified the restriction with `kubectl auth can-i` —
listing pods returned yes, deleting returned no. Reinforced core
Kubernetes building blocks (nodes, pods, namespaces) and confirmed
Kubernetes RBAC follows the same least-privilege pattern as AWS IAM —
same concept, different system. Read about NetworkPolicy conceptually
(not hands-on today — kind's default networking doesn't enforce
NetworkPolicy without a separate CNI like Calico).

**Stuck on:** none

**Next:** possibly revisit NetworkPolicy hands-on later with Calico
installed; continue phase 1 cloud security work.
