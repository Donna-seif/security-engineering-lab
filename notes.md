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
