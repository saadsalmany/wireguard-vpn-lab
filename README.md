# WireGuard VPN - Client-to-Site Setup

A project to set up secure remote access to a private AWS network using WireGuard VPN.

## What This Project Does

Allows remote employees to securely access company internal servers without exposing them to the internet. Only authorized VPN clients can reach the private network.

## The Problem I Solved

Remote workers need to access internal company resources (like databases, internal apps, etc.) that are in a private subnet with no public IP. We can't just expose these to the internet because that's a security risk.

## My Solution

Built a WireGuard VPN server in AWS that acts as a secure gateway. Remote clients connect to the VPN, get encrypted access to internal resources, while everything else stays private.

## Architecture

- **VPC:** 10.0.0.0/16
- **Public Subnet:** 10.0.0.0/24 (WireGuard server lives here)
- **Private Subnet:** 10.0.1.0/24 (Internal servers here - no internet access)
- **VPN Overlay Network:** 10.200.0.0/24 (tunnel network for VPN clients)

## What I Built

### AWS Infrastructure
- VPC with public and private subnets
- Internet Gateway for public subnet
- Security groups (allowed UDP 51820 for VPN, SSH for management)
- Route table configuration to route VPN traffic to internal subnet
- EC2 instances (WireGuard server + internal test server)

### WireGuard Configuration
- Generated public/private key pairs for authentication
- Configured server to listen on UDP port 51820
- Set up client config with proper routing (split-tunnel)
- Enabled IP forwarding on the server
- Tested with real SSH connections to private subnet

## How It Works

1. Client connects to WireGuard server's public IP (encrypted tunnel)
2. Client authenticates using cryptographic keys
3. All traffic to internal network (10.0.1.0/24) routes through the tunnel
4. WireGuard server forwards packets to internal subnet
5. Return traffic comes back through the same encrypted tunnel
6. Regular internet traffic stays on client's normal connection (split-tunnel)

## Testing

I verified everything works by:
- ✅ Successful handshake between client and server
- ✅ Ping to WireGuard server tunnel IP (10.200.0.1)
- ✅ Ping to internal server (10.0.1.33) through VPN
- ✅ SSH into internal server from remote client
- ✅ Verified routing table shows correct routes

Average latency through tunnel: ~25ms

## What I Learned

- How VPNs create overlay networks on top of physical networks
- Public/private key cryptography for authentication
- The difference between symmetric and asymmetric encryption
- AWS VPC networking, subnets, route tables, security groups
- IP forwarding and how packets route between networks
- Split-tunnel vs full-tunnel VPN configurations
- Troubleshooting network connectivity issues

## Files in This Repo

- `README.md` - This file
- `documentation.pdf` - Full detailed documentation with diagrams
- `config-templates/` - Sample WireGuard config files (sanitized)

## Tech Stack

- **WireGuard** - Modern VPN protocol
- **AWS** - VPC, EC2, Security Groups, Route Tables
- **Ubuntu 24.04** - Linux OS for both server and client
- **iptables** - For routing configuration

## Security Notes

- Private keys are never committed to this repo (obviously!)
- Internal servers have no public IPs
- Security groups restrict access to VPN subnet only
- All VPN traffic is encrypted with modern ciphers (ChaCha20)

## How to Use This

Check out the full PDF documentation for step-by-step setup instructions, configuration details, and testing procedures.

## Future Improvements

Some ideas for v2:
- Add multiple client support (multiple employees)
- Automation scripts for onboarding new users
- Monitoring/logging setup
- High availability with multiple VPN servers
- Integration with corporate authentication

## Contact

Feel free to reach out if you have questions about this project!

---

*This was a learning project to understand VPN technology, AWS networking, and security best practices.*
