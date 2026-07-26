# Milestone 2: NAT Gateway — Outbound Internet for Private Subnets

**Why:** Private subnets are meant to stay inaccessible from the internet, but resources inside them still need a way to make outbound connections — for example, to download updates or packages. A NAT Gateway sits between the private subnet and the internet to provide exactly this: outbound connections are allowed, but no inbound connection from the internet can ever be established through it.

**What was built:**
- 1 Elastic IP, allocated for the NAT Gateway to use as its internet-facing address
- 1 NAT Gateway (`vpc-architecture-nat`), placed in `public-subnet-1`
- Updated `private-route-table`: added a `0.0.0.0/0 → NAT Gateway` route, so both private subnets now route outbound traffic through the NAT

**Lessons Learned**
- The NAT Gateway serves the private subnets but must itself sit in a public subnet, since it needs to reach the Internet Gateway to have any internet access at all.
- NAT Gateway only allows one-way, outbound-initiated connections — no inbound connection can be established through it, regardless of route tables or security groups. An Internet Gateway, by contrast, allows bidirectional traffic if routing, public IP, and security groups all permit it. This directional difference — not just "one is for private, one is for public" — is the real reason NAT is the correct choice here.

**Screenshots**
![Attaching NAT Gateway to private subnet](../../screenshots/milestone-2/private-route-table-nat(1).png)
![Route table with NAT route](../../screenshots/milestone-2/private-route-table-nat(2).png)
