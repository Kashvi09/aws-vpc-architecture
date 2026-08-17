# Interview Questions & Answers

## Milestone 1: VPC Foundation

**Q: Why did we choose a /16 CIDR block instead of something smaller like /24?**
A: A /24 VPC only provides 256 addresses total, which isn't enough to carve out four /24 subnets (that needs at least a /22). /16 was chosen to comfortably fit the current 4-subnet design with a large margin left over for adding subnets, AZs, or even peering other VPCs later without re-architecting.

**Q: Why do public-subnet-1 and private-subnet-1 both sit in the same AZ — isn't that defeating the purpose of splitting them?**
A: Redundancy comes from each tier (public, private) spanning multiple AZs — not from every individual subnet needing its own AZ. Two subnets sharing an AZ is fine as long as each tier has a subnet in a second AZ to fall back on.

**Q: If a private route table accidentally had a 0.0.0.0/0 → IGW route added, would that alone make private EC2 instances reachable from the internet?**
A: Not by itself. Reachability also requires the instance to have a public IP and the security group to permit the traffic. A route table controls whether traffic is directed toward the internet, but relying on it as the sole protection is a single point of failure — defense in depth means multiple independent layers all have to fail for exposure to happen.

## Milestone 2: NAT Gateway

**Q: Why must the NAT Gateway sit in a public subnet, when its whole job is serving private subnets?**
A: The NAT Gateway itself needs outbound internet access to function, and only public subnets (routed to the IGW) provide that — so it must physically sit there even though it serves private subnets.

**Q: What's the practical difference between a NAT Gateway and an Internet Gateway?**
A: An Internet Gateway allows bidirectional traffic — inbound connections are possible if routing, public IP, and security groups all permit it. A NAT Gateway is architecturally one-directional: it performs address translation only for outbound-initiated connections, so no inbound connection can be established through it regardless of other settings.

## Milestone 3: EC2 + Auto Scaling Group

**Q: Why select the ALB's security group as the *source* in the EC2 security group's inbound rule, instead of an IP range?**
A: This means "allow traffic from anything carrying alb-sg" dynamically, regardless of IP — more robust than an IP-based rule since ALB nodes can have changing IPs, but always carry alb-sg. It also ensures only the ALB, not the internet or other instances, can reach the EC2 instances.

**Q: What would happen to Session Manager access if the AMI didn't have the SSM agent pre-installed?**
A: Session Manager simply wouldn't work — the instance would never show as "Managed" in Systems Manager. Installing the agent manually would require internet access, creating a dependency on NAT Gateway connectivity working correctly first — and with no SSH key pair as a fallback, a broken NAT would leave no way to access the instance at all.

## Milestone 4: Application Load Balancer

**Q: What's the difference between the ALB's health check and the ASG's health check?**
A: The ALB's health check determines whether traffic is routed to an instance. The ASG's health check determines whether an unhealthy instance is terminated and replaced. By default, the ASG only checks EC2-level health (is the instance running), not application-level health — that requires explicitly enabling ELB health checks on the ASG.

**Q: If an ALB is only registered with 1 of 2 available public subnets, would it still work?**
A: No — AWS enforces a minimum of 2 Availability Zones at ALB creation time. It's not possible to create an Application Load Balancer with only one subnet/AZ selected.

## Milestone 5: Scaling Policies + Monitoring

**Q: Why does the ASG need an automated scaling policy instead of a manually-set capacity?**
A: A fixed, manually-set capacity might be insufficient during real traffic spikes or wasteful when idle. A target tracking scaling policy adjusts capacity automatically based on actual load (CPU utilization).

**Q: Does a target tracking scaling policy create anything besides the policy itself?**
A: Yes — it automatically creates its own CloudWatch alarms (AlarmHigh and AlarmLow) to trigger scale-out/scale-in. Seeing one of these "In alarm" doesn't necessarily mean something is broken — it can simply reflect an idle instance below the target, with the ASG's minimum capacity acting as a floor preventing any real scale-in.

## Milestone 6: CloudFormation (Infrastructure as Code)

**Q: Why did leftover manually-created resources (an SNS topic, a CloudWatch alarm) cause CloudFormation deployment failures?**
A: Most AWS resource types enforce name uniqueness within an account/region. CloudFormation's early validation checks for these conflicts before creating anything, to avoid a deploy failing partway through and leaving a mix of created and un-created resources.

**Q: Why is `!Ref TargetGroup` inside the ASG's configuration immune to the stale-ARN bug that caused an outage in Milestone 4?**
A: `!Ref` always resolves to whatever resource currently exists within that same CloudFormation stack — it doesn't hold onto an ARN independently. Unlike manually copying an ID between console screens, there's no way for a `!Ref`-based reference to go stale.

## Milestone 7: CI/CD via GitHub Actions

**Q: Why scope the workflow trigger to `paths: infrastructure/template.yaml` instead of every push to main?**
A: Without the path filter, any push to main — even unrelated changes like editing the README — would trigger a full deployment attempt. Scoping to the template file ensures deployments only happen when the infrastructure actually changes.

**Q: What's the real security risk of storing AWS access keys as GitHub secrets, even though they're encrypted?**
A: The secrets themselves aren't retrievable by viewing the repo, even publicly. The real risk is that they're long-lived static credentials — vulnerable to exfiltration through misconfigured workflows (e.g. running untrusted pull requests) or accidental leaks elsewhere. OIDC federation is a more secure alternative: it issues short-lived credentials scoped to a single workflow run instead of storing any static key.

## Milestone 8: VPC Flow Logs

**Q: What's the difference between a VPC Flow Log and a CloudTrail log?**
A: Flow Logs capture network traffic metadata — IP-level connections, ports, protocols, accept/reject decisions. CloudTrail captures API-level account activity — who called which AWS API, when, from where. Flow Logs answer "what network connections happened"; CloudTrail answers "what actions were taken in the account."

**Q: Why log ALL traffic instead of just REJECT, if REJECT is the more "security-interesting" data?**
A: Confirming that only legitimate, expected traffic was accepted is just as important a security signal as seeing what was blocked. ALL gives a complete audit trail rather than only half the picture.