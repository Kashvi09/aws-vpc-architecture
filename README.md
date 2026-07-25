# aws-vpc-architecture
# Multi-Tier VPC Architecture

> Status: 🚧 In Progress

## Overview
This project is a multi-tier network architecture on AWS, built to demonstrate deep-networking skills that a serverless project doesn't touch — public/private subnet design, secure routing, load balancing, and auto-scaling.

An Application Load Balancer sits in public subnets and receives internet traffic, forwarding it to a group of EC2 instances running in private subnets with no direct internet exposure. An Auto Scaling Group manages instance count based on load, and AWS Systems Manager (SSM) Session Manager provides secure access to the private instances without opening SSH ports to the internet.

Beyond the working architecture, this project is a hands-on exploration of VPC/subnet design, NAT gateways, security groups vs. NACLs, load balancing, and auto-scaling — including real issues encountered and resolved along the way.

## Architecture

## AWS Services Used
| Service | Purpose |
|---|---|
## AWS Services Used
| Service | Purpose |
|---|---|
| VPC | Custom-CIDR (10.0.0.0/16) isolated network for this project |
| Subnets | 2 public (10.0.0.0/24, 10.0.1.0/24) + 2 private (10.0.10.0/24, 10.0.11.0/24), spread across ap-south-1a and ap-south-1b for AZ redundancy |
| Internet Gateway | Attached to the VPC; provides the internet route used by public subnets |
| Route Tables | public-route-table (0.0.0.0/0 → IGW, associated with public subnets); private-route-table (local-only, no internet route, associated with private subnets) |

## Cost Breakdown
Every resource in this project stays within AWS's free tier. Full per-resource breakdown → [`docs/cost-breakdown.md`](./docs/cost-breakdown.md)

| Category | Services | Monthly Cost |
|---|---|---|
| Networking foundation | VPC, subnets, route tables, Internet Gateway | $0 |

## Setup / Deployment Guide

## Milestone Log

Detailed write-ups (why each decision was made, what was built, lessons learned) live in [`/docs/milestones`](./docs/milestones):

- [Milestone 1: VPC Foundation — CIDR, Subnets, Internet Gateway, Route Tables](./docs/milestones/milestone-1-vpc-foundation.md)

## Known Limitations

## Future Improvements

## Interview Questions & Answers