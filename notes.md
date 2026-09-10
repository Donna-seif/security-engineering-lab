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
