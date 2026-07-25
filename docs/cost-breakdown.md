# Cost Breakdown

| Service | Purpose | Free Tier Eligible? | Possible Cost | Delete After Project? |
|---|---|---|---|---|
| VPC | Custom-CIDR (10.0.0.0/16) isolated network | Yes — always free, no tier limit | $0 | No — VPCs incur no cost sitting idle, safe to keep |
| Subnets (x4) | 2 public + 2 private, split across ap-south-1a/1b | Yes — always free, no tier limit | $0 | No — free regardless of count or duration |
| Internet Gateway | Attached to VPC; enables internet route for public subnets | Yes — always free to create/attach | $0 | No — free while attached; only data transfer out is billed, and that's usage-based, not a standing cost |
| Route Tables (x2) | public-route-table (IGW route) + private-route-table (local-only) | Yes — always free, no tier limit | $0 | No — free regardless of count |