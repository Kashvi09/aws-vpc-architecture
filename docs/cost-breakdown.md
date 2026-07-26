# Cost Breakdown

| Service | Purpose | Free Tier Eligible? | Possible Cost | Delete After Project? |
|---|---|---|---|---|
| VPC | Custom-CIDR (10.0.0.0/16) isolated network | Yes — always free, no tier limit | $0 | No — VPCs incur no cost sitting idle, safe to keep |
| Subnets (x4) | 2 public + 2 private, split across ap-south-1a/1b | Yes — always free, no tier limit | $0 | No — free regardless of count or duration |
| Internet Gateway | Attached to VPC; enables internet route for public subnets | Yes — always free to create/attach | $0 | No — free while attached; only data transfer out is billed, and that's usage-based, not a standing cost |
| Route Tables (x2) | public-route-table (IGW route) + private-route-table (local-only) | Yes — always free, no tier limit | $0 | No — free regardless of count |
| NAT Gateway | Outbound internet access for private subnets | No — billed hourly + per GB | ~$0.045/hr + ~$0.045/GB (region-dependent, verify current pricing) | Yes — delete after each session unless actively moving into next milestone |
| Elastic IP (for NAT) | Static IP required by the NAT Gateway | No — free only while attached to a running resource | ~$0.005/hr if left unattached | Yes — release immediately after deleting the NAT Gateway |