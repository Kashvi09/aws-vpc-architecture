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

## Cost Breakdown

| Category | Services | Monthly Cost |
|---|---|---|

## Setup / Deployment Guide

## Milestone Log

Detailed write-ups (why each decision was made, what was built, lessons learned) live in [`/docs/milestones`](./docs/milestones):

## Known Limitations

## Future Improvements

## Interview Questions & Answers