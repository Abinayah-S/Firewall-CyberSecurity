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

Before configuring UFW, confirm it is installed on your system. Run the following command to check the installed version:

If UFW is not installed, install it:

### Step 2: Check Current Firewall Status

Before making any changes, verify your firewall's current operational state. This baseline documentation is critical for understanding what rules existed before your modifications.

```bash
sudo ufw status
```

This command returns either "active" (firewall is running and enforcing rules) or "inactive" (firewall is installed but not running). If inactive, your system currently has no firewall protection enabled.


### Step 3: Enable the Firewall

Enable UFW to activate firewall protection on your system. When you enable UFW, it automatically adds a default rule to allow SSH connections (port 22), ensuring you do not lock yourself out of remote access.

```bash
sudo ufw enable
```

The system prompts you with a warning that enabling the firewall "may disrupt existing SSH connections." This warning exists because inexperienced users sometimes block port 22, losing remote access. UFW handles this automatically, but the warning is a safety reminder. Press Y and Enter to confirm activation.


### Step 4: Add a Rule to Block Telnet Traffic

Telnet (port 23) is an outdated remote access protocol that transmits all traffic including login credentials in cleartext, making it a significant security vulnerability. Blocking this port demonstrates understanding of why certain legacy services should be disabled:

```bash
sudo ufw deny 23/tcp
```

This command creates a deny rule for TCP traffic on port 23. Breaking down the syntax: `ufw` is the command, `deny` is the action (reject traffic), `23` is the port number, and `/tcp` specifies the protocol. The rule applies to inbound traffic by default.

### Step 5: Explicitly Allow SSH Traffic

While UFW allows SSH by default, explicitly adding an allow rule demonstrates understanding of selective rule creation and makes your security posture intentional rather than accidental:

```bash
sudo ufw allow 22/tcp
```

This rule explicitly permits inbound TCP traffic on port 22 (SSH). In production environments, you might further restrict SSH to specific source IP addresses using rules like `sudo ufw allow from 192.168.1.100 to any port 22`, but for this exercise we allow SSH from any source.

### Step 6: View All Configured Rules

Display your complete ruleset with line numbers to verify rules were added correctly and prepare for testing and rule removal:

```bash
sudo ufw status numbered
```

Your output should display multiple rules including the default SSH rule (if created by UFW automatically), your explicit SSH allow rule, and your Telnet deny rule. Each rule shows a number, action (ALLOW/DENY), FROM source, TO destination, and the protocol.

## Testing and Verification:

### Testing Port 23 Block

Verify that your firewall successfully blocks Telnet traffic on port 23. Attempt a connection to this port:

```bash
telnet localhost 23
```

Expected result: The connection hangs or times out after several seconds, indicating the firewall is blocking the connection attempt. This timeout occurs because the firewall silently drops packets destined for port 23 rather than explicitly rejecting them (this is configurable).

If you receive an error that telnet is not installed, install it temporarily for testing:

```bash
sudo apt install telnet
```

### Alternative Testing Using netcat

If telnet is unavailable, use netcat (nc) to test port connectivity. First install netcat:

```bash
sudo apt install netcat-openbsd
```

Then attempt a connection:

```bash
nc -zv localhost 23
```

The `-z` flag performs a port scan without sending data, and `-v` provides verbose output showing connection success or failure.

### Testing SSH Access

Verify that SSH remains accessible after firewall configuration:

```bash
sudo ss -tulpn | grep :22
```

This command shows that SSH is listening on port 22. You can also test SSH connectivity from another machine on your network:

```bash
ssh username@your_server_ip
```

A successful connection confirms your SSH allow rule is functioning correctly.


## Key Concepts Explained:

### Why Block Port 23 (Telnet)

Telnet transmits all data including authentication credentials in plaintext without encryption. Any network observer can capture credentials using packet sniffing tools. Modern remote access uses SSH (port 22), which encrypts all traffic. Blocking port 23 removes an obvious attack vector and discourages use of this insecure protocol. This demonstrates understanding of protocol security and risk-based rule prioritization.

### Common Firewall Mistakes

Setting overly permissive default policies (allow all traffic by default) without explicit deny rules creates security gaps. Blocking critical services like SSH without understanding consequences can cause lockouts. Misconfiguring stateful firewall rules by not allowing return traffic for outbound connections can break legitimate functionality. Failing to document rule purposes makes future maintenance difficult. Not regularly auditing firewall rules leads to accumulation of obsolete rules.

### How Firewalls Improve Network Security

Firewalls implement the principle of least privilege by controlling access to only necessary services and ports. They prevent unauthorized access attempts from reaching vulnerable services by filtering at the network layer before packets reach application software. Firewalls provide visibility into traffic patterns through logging capabilities. They protect against certain attack types like port scanning and network reconnaissance by silently dropping unauthorized packets. In combination with intrusion detection systems, firewalls provide layered security defense.

## Important Security Considerations

When configuring firewalls in production environments, always verify SSH access remains available before enabling the firewall. Test your configuration thoroughly in non-critical environments first. Document every rule and its purpose for future reference. Implement principle of least privilege by creating deny rules for unnecessary ports. Regularly review and audit firewall rules to remove obsolete entries. Monitor firewall logs for suspicious activity patterns.

## Troubleshooting

If you lose SSH access after enabling the firewall, you can typically recover by accessing your system through alternate means (physical console, recovery mode) and running `sudo ufw disable` to restore connectivity while you correct the configuration.

If UFW does not recognize your commands, verify it is installed and you are using the correct command syntax. Run `sudo ufw help` for command reference.

If rules appear not to take effect, verify the firewall status is active using `sudo ufw status`. Rules only apply when the firewall is actively running.

## Files Included in This Repository:

README.md: This comprehensive guide documenting the entire setup process, testing methodology, and firewall concepts.

SETUP_REPORT.md: Detailed execution report documenting all commands run, their outputs, and verification results.

commands.txt: Complete command reference listing every firewall command executed during this project in sequential order.


## Learning Outcomes:

Upon completing this project successfully, you understand how firewalls function as network security gatekeepers. You can configure basic firewall rules using UFW to allow or block traffic on specific ports. You understand the difference between stateful and stateless firewalls and why stateful filtering provides better security. You can test firewall configurations to verify they function as intended. You understand why certain protocols like Telnet represent security risks and how to prevent their use. You recognize that firewall management requires careful planning to avoid unintended consequences.

## References and Resources:

UFW Official Documentation: https://manpages.ubuntu.com/manpages/focal/man8/ufw.8.html

Netfilter/iptables Documentation: https://netfilter.org/

Linux Foundation Network Security: https://training.linuxfoundation.org/

OWASP Network Segmentation: https://owasp.org/www-community/controls/Network_Segmentation

SSH Protocol Security: https://www.rfc-editor.org/rfc/rfc4251

## Conclusion:

This project documentation is provided for educational purposes as part of a cybersecurity internship program.
