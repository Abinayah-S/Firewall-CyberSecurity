# UFW Firewall Configuration and Management

## Objective:

This project demonstrates practical configuration and testing of the Uncomplicated Firewall (UFW) on a Linux system. The objective is to configure firewall rules that selectively allow and block network traffic on specific ports, understand how firewalls filter traffic at the network layer, and validate rule effectiveness through systematic testing. This exercise provides foundational knowledge in network security, access control, and security policy implementation.

## Project Overview:

A firewall acts as a security gatekeeper between your system and external networks, making decisions about which traffic is permitted based on configurable rules. This project implements stateful firewall rules using UFW, blocks unnecessary ports (specifically Telnet on port 23 as a security demonstration), and ensures critical services like SSH remain accessible. By completing this task, we establish a baseline understanding of how firewalls protect systems from unauthorized access and network-based attacks.

## Prerequisites:

1. A Linux operating system with kernel support for netfilter (virtually all modern Linux distributions). UFW is compatible with Ubuntu, Debian, Fedora, and other major distributions.

2. UFW (Uncomplicated Firewall) installed on your system. Most Linux distributions include UFW by default, but you can install it using your package manager.

3. Sudo or root access to execute firewall configuration commands. Firewall management requires elevated privileges because it modifies kernel-level network filtering rules.

4. Basic command-line terminal experience and familiarity with running commands with sudo privileges.

5. A local or remote SSH connection to your system for testing purposes (optional but recommended for validating SSH rules).


## Installation and Setup Procedures:

### Step 1: Verify UFW Installation

### Step 2: Check Current Firewall Status

### Step 3: Enable the Firewall

### Step 4: Add a Rule to Block Telnet Traffic

### Step 5: Explicitly Allow SSH Traffic

### Step 6: View All Configured Rules


## Testing and Verification:

### Testing Port 23 Block

Verify that your firewall successfully blocks Telnet traffic on port 23. Attempt a connection to this port.

Expected result: The connection hangs or times out after several seconds, indicating the firewall is blocking the connection attempt. This timeout occurs because the firewall silently drops packets destined for port 23 rather than explicitly rejecting them (this is configurable).

### Testing SSH Access

Verify that SSH remains accessible after firewall configuration.

This shows that SSH is listening on port 22. You can also test SSH connectivity from another machine on your network.

A successful connection confirms your SSH allow rule is functioning correctly.

## Files Included in This Repository:

README.md: This comprehensive guide documenting the entire setup process, testing methodology, and firewall concepts.

SETUP_REPORT.md: Detailed execution report documenting all commands run, their outputs, and verification results.

commands.txt: Complete command reference listing every firewall command executed during this project in sequential order.

screenshots/ directory: Contains snapshots of terminal output showing rule creation, testing, and verification stages of this configuration exercise.

## Learning Outcomes:

Upon completing this project successfully, you understand how firewalls function as network security gatekeepers. You can configure basic firewall rules using UFW to allow or block traffic on specific ports. You understand the difference between stateful and stateless firewalls and why stateful filtering provides better security. You can test firewall configurations to verify they function as intended. You understand why certain protocols like Telnet represent security risks and how to prevent their use. You recognize that firewall management requires careful planning to avoid unintended consequences.

## Conclusion:

This project documentation is provided for educational purposes as part of a cybersecurity internship program.
