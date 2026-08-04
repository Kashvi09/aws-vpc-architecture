# aws-vpc-architecture
# Multi-Tier VPC Architecture

> Status: 🚧 In Progress

## Overview
This project is a multi-tier network architecture on AWS, built to demonstrate deep-networking skills that a serverless project doesn't touch — public/private subnet design, secure routing, load balancing, and auto-scaling.

An Application Load Balancer sits in public subnets and receives internet traffic, forwarding it to a group of EC2 instances running in private subnets with no direct internet exposure. An Auto Scaling Group manages instance count based on load, and AWS Systems Manager (SSM) Session Manager provides secure access to the private instances without opening SSH ports to the internet.

Beyond the working architecture, this project is a hands-on exploration of VPC/subnet design, NAT gateways, security groups vs. NACLs, load balancing, and auto-scaling — including real issues encountered and resolved along the way.

## Architecture

## Infrastructure as Code
The entire architecture is defined in `infrastructure/template.yaml` and deployed via:
`aws cloudformation deploy --template-file infrastructure/template.yaml --stack-name vpc-architecture-stack --capabilities CAPABILITY_NAMED_IAM`

## AWS Services Used
| Service | Purpose |
|---|---|
| VPC | Custom-CIDR (10.0.0.0/16) isolated network for this project |
| Subnets | 2 public (10.0.0.0/24, 10.0.1.0/24) + 2 private (10.0.10.0/24, 10.0.11.0/24), spread across ap-south-1a and ap-south-1b for AZ redundancy |
| Internet Gateway | Attached to the VPC; provides the internet route used by public subnets |
| Route Tables | public-route-table (0.0.0.0/0 → IGW, associated with public subnets); private-route-table (local-only, no internet route, associated with private subnets) |
| NAT Gateway | Provides outbound-only internet access for private subnets, sitting in a public subnet; paired with an Elastic IP |
| Security Groups | alb-sg (allows inbound HTTP from internet); ec2-sg (allows inbound HTTP only from alb-sg) |
| IAM Role | ec2-ssm-role — grants EC2 instances permission to be managed via SSM Session Manager |
| Launch Template | Blueprint for EC2 instances — Amazon Linux 2023, t2.micro/t3.micro, ec2-sg, ec2-ssm-role, user data installs Apache |
| Auto Scaling Group | vpc-architecture-asg — spans both private subnets, desired 1 / min 1 / max 2 |
| Target Group | vpc-architecture-tg — HTTP:80, health check path `/`, registered EC2 targets from the ASG |
| Application Load Balancer | vpc-architecture-alb — internet-facing, spans both public subnets, alb-sg attached, HTTP:80 listener forwarding to the target group |
| Auto Scaling Policy | Target tracking on Average CPU Utilization, target 50% — automatically scales the ASG based on real load instead of a fixed capacity |
| CloudWatch Alarms | alb-5xx-alarm (custom, on HTTPCode_ELB_5XX_Count); AlarmHigh/AlarmLow (auto-created by the target tracking policy) |

## Cost Breakdown
Every resource in this project stays within AWS's free tier. Full per-resource breakdown → [`docs/cost-breakdown.md`](./docs/cost-breakdown.md)

| Category | Services | Monthly Cost |
|---|---|---|
| Networking foundation | VPC, subnets, route tables, Internet Gateway | $0 |
| Outbound connectivity | NAT Gateway + Elastic IP | ~$0.045/hr + ~$0.045/GB processed while running; **not free-tier** — deleted between sessions to avoid ongoing charges |
| Compute | EC2 (via ASG), Launch Template, Security Groups, IAM Role | $0 while within free-tier 12-month window (750 hrs/month t2.micro/t3.micro combined); keep desired capacity low (1-2) to control hours used |
| Load balancing | Application Load Balancer, Target Group | ~$0.0225/hr (~$16/month if left running) + negligible LCU charge at test volume; **not free-tier** — delete between sessions unless moving straight into next milestone |
| Scaling & monitoring | Target tracking scaling policy, CloudWatch alarms | $0 — scaling policy is free; alarms are free tier eligible (well within the 10 free-tier alarm limit) |

## Setup / Deployment Guide

## Milestone Log

Detailed write-ups (why each decision was made, what was built, lessons learned) live in [`/docs/milestones`](./docs/milestones):

- [Milestone 1: VPC Foundation — CIDR, Subnets, Internet Gateway, Route Tables](./docs/milestones/milestone-1-vpc-foundation.md)
- [Milestone 2: NAT Gateway — Outbound Internet for Private Subnets](./docs/milestones/milestone-2-nat-gateway.md)
- [Milestone 3: EC2 + Auto Scaling Group (Private Subnets)](./docs/milestones/milestone-3-ec2-asg.md)
- [Milestone 4: Application Load Balancer](./docs/milestones/milestone-4-alb.md)
- [Milestone 5: Auto Scaling Policies + CloudWatch Monitoring](./docs/milestones/milestone-5-scaling-monitoring.md)
- [Milestone 6: CloudFormation Rewrite (Infrastructure as Code)](./docs/milestones/milestone-6-cloudformation.md)

## Known Limitations

## Future Improvements

## Interview Questions & Answers