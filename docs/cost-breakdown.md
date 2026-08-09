# Cost Breakdown

| Service | Purpose | Free Tier Eligible? | Possible Cost | Delete After Project? |
|---|---|---|---|---|
| VPC | Custom-CIDR (10.0.0.0/16) isolated network | Yes — always free, no tier limit | $0 | No — VPCs incur no cost sitting idle, safe to keep |
| Subnets (x4) | 2 public + 2 private, split across ap-south-1a/1b | Yes — always free, no tier limit | $0 | No — free regardless of count or duration |
| Internet Gateway | Attached to VPC; enables internet route for public subnets | Yes — always free to create/attach | $0 | No — free while attached; only data transfer out is billed, and that's usage-based, not a standing cost |
| Route Tables (x2) | public-route-table (IGW route) + private-route-table (local-only) | Yes — always free, no tier limit | $0 | No — free regardless of count |
| NAT Gateway | Outbound internet access for private subnets | No — billed hourly + per GB | ~$0.045/hr + ~$0.045/GB (region-dependent, verify current pricing) | Yes — delete after each session unless actively moving into next milestone |
| Elastic IP (for NAT) | Static IP required by the NAT Gateway | No — free only while attached to a running resource | ~$0.005/hr if left unattached | Yes — release immediately after deleting the NAT Gateway |
| EC2 (via ASG) | Application servers in private subnets | Yes — 750 hrs/month t2.micro/t3.micro combined, within 12-month window | $0 now; standard On-Demand pricing after free-tier window ends | No — keep running, but set desired capacity to 0 during extended breaks to conserve free-tier hours |
| Security Groups (alb-sg, ec2-sg) | Instance-level firewall rules | Yes — always free | $0 | No — free regardless of duration |
| IAM Role (ec2-ssm-role) | Grants EC2 permission for SSM Session Manager | Yes — always free | $0 | No — free regardless of duration |
| Launch Template | Blueprint for EC2 launch config | Yes — always free | $0 | No — free regardless of duration |
| Application Load Balancer | Distributes internet traffic to EC2 targets across AZs | No — billed hourly + per LCU | ~$0.0225/hr + LCU (negligible at test volume) | Yes — delete after each session unless actively moving into next milestone |
| Target Group | Tracks EC2 targets + health status for the ALB | Yes — always free | $0 | No — free regardless of duration (deleted automatically as part of ALB teardown if you choose to, or can be left) |
| Target Tracking Scaling Policy | Automatically adjusts ASG capacity based on CPU | Yes — always free | $0 | No — free regardless of duration |
| CloudWatch Alarms (x3) | Alerts on high/low CPU and ALB 5xx errors | Yes — free tier covers first 10 alarms | $0 | No — free regardless of duration |
| CloudFormation (stack management) | Orchestrates creation/deletion of all project resources as one unit | Yes — always free, no charge for the service itself | $0 | No — the stack itself has no cost; deleting it via `delete-stack` removes all underlying billed resources cleanly, which is the actual cost-control benefit of this milestone |
| IAM User (github-actions-deployer-vpc) | CI/CD identity used by GitHub Actions to deploy the stack | Yes — always free | $0 | No — free regardless of duration; keep the user, only rotate/revoke its access key if compromised |
| GitHub Actions | Automates `aws cloudformation deploy` on push to main | Yes — free for this repo (public repo minutes) | $0 | No — nothing to delete; the workflow file stays in the repo permanently |