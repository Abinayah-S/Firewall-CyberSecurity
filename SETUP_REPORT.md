# UFW Firewall Configuration Setup Report

**System**: Linux Notebook PC (Ubuntu-based)

**Username**: abinayah

**Firewall Tool**: UFW (Uncomplicated Firewall) 0.36.2

**System Hostname**: abinayah-HP-245-G5-Notebook-PC

## Executive Summary

This report documents the complete configuration, testing, and validation of UFW firewall on a Linux system. The objective was to configure firewall rules that selectively block and allow network traffic on specific ports while maintaining system accessibility and understanding network security principles. All procedures completed successfully with full documentation of commands executed, outputs received, and test validations performed.


## Initial Firewall State:
Before beginning configuration, the firewall status was checked. The UFW version installed was confirmed as 0.36.2 with copyright notice from Canonical Ltd. dating to 2008-2023.

## Phase 1: Pre-Configuration Verification

### Step 1.1: UFW Version Confirmation

**Command Executed**:

sudo ufw version

ufw 0.36.2
Copyright 2008-2023 Canonical Ltd.

Interpretation: UFW is installed and operational on the system. Version 0.36.2 is current and stable for production use.


## Step 1.2: Initial Status Check

**Command Executed**:

sudo ufw status

Status: active

To                         Action      From
--                         ------      ----
22/tcp                     ALLOW       Anywhere
23/tcp                     DENY        Anywhere
22/tcp (v6)                ALLOW       Anywhere (v6)
23/tcp (v6)                DENY        Anywhere (v6)

Interpretation: The firewall was already active on this system with pre-existing rules. Port 22 (SSH) was already configured to allow traffic. Port 23 (Telnet) was already configured to deny traffic. Both IPv4 and IPv6 rules were present.
This indicates either previous firewall configuration or system defaults. Despite this, the configuration exercise proceeded to demonstrate explicit rule management and proper rule removal procedures.


## Phase 2: Firewall Rule Activation and Configuration

## Step 2.1: Enable UFW (Confirmation of Active Status)

**Command Executed**:

sudo ufw enable

Firewall is active and enabled on system startup

Interpretation: The firewall activation command confirmed that UFW is enabled and configured to start automatically when the system boots. The firewall immediately began enforcing configured rules.

## Step 2.2: Attempt to Add Deny Rule for Port 23

**Command Executed**:

sudo ufw deny 23/tcp

Skipping adding existing rule
Skipping adding existing rule (v6)

Interpretation: This is a significant observation. The system reported that the deny rule for port 23 already existed. UFW intelligently recognized that attempting to add a duplicate rule would be redundant and skipped the addition. This demonstrates UFW's built-in duplicate prevention feature, which prevents accidental rule duplication that could occur in automated configuration scripts.


## Step 2.3: Attempt to Add Allow Rule for Port 22

**Command Executed**:

sudo ufw allow 22/tcp

Skipping adding existing rule
Skipping adding existing rule (v6)

Interpretation: Similarly, the allow rule for port 22 already existed on the system. UFW again recognized the redundant rule request and skipped addition. This demonstrates that the system already had proper SSH access configured as a safety measure.

## Step 2.4: View Complete Firewall Rule Configuration

**Command Executed**:

sudo ufw status numbered

Status: active

     To                         Action      From
     --                         ------      ----
[ 1] 22/tcp                     ALLOW IN    Anywhere
[ 2] 23/tcp                     DENY IN     Anywhere
[ 3] 22/tcp (v6)                ALLOW IN    Anywhere (v6)
[ 4] 23/tcp (v6)                DENY IN     Anywhere (v6)


Critical Analysis: This numbered output is the definitive proof of firewall configuration. The output clearly shows four active rules in order of evaluation. Rule 1 and 3 allow inbound TCP traffic on port 22 for IPv4 and IPv6 respectively. Rule 2 and 4 deny inbound TCP traffic on port 23 for IPv4 and IPv6 respectively. The ALLOW IN and DENY IN labels clearly indicate these are inbound rules.

Rule Evaluation Order: UFW evaluates rules in the order shown. When a packet arrives on port 22, it matches rule 1 and is allowed immediately. When a packet arrives on port 23, it matches rule 2 and is denied immediately. This sequential evaluation order is critical to understanding firewall behavior.


## Phase 3: Testing and Validation of Rules


## Step 3.1: Test Port 23 Block Using Telnet

**Command Executed**:

telnet localhost 23

Trying 127.0.0.1...
telnet: Unable to connect to remote host: Connection refused

Test Validation: The telnet client attempted to establish a connection to localhost on port 23. The firewall immediately rejected this connection attempt, resulting in "Connection refused" error. This confirms that the deny rule for port 23 is actively blocking traffic. The connection refusal occurs at the firewall level before any service listening on that port would be consulted.

Security Implication: This test proves that Telnet access is completely blocked. Even if a Telnet service was running on the system, the firewall would prevent external systems from reaching it. This is the intended behavior for blocking unnecessary legacy services.


## Step 3.2: Test Port 23 Block Using Netcat

**Command Executed**:

nc -zv localhost 23

nc: connect to localhost (127.0.0.1) port 23 (tcp) failed: Connection refused

Test Validation: Netcat is an alternative tool for testing network connectivity. The verbose flag (-v) provides detailed output. The test confirms again that port 23 is inaccessible. The "Connection refused" message from netcat provides the same evidence from a different utility, validating that the firewall block is consistent and reliable.


## Phase 4: Rule Removal and System Restoration

## Step 4.1: Delete the Port 23 Deny Rule

**Command Executed**:

sudo ufw delete deny 23/tcp

Rule deleted
Rule deleted (v6)

Interpretation: The delete command successfully removed both the IPv4 and IPv6 deny rules for port 23. The firewall confirmed that two rules were deleted (one for IPv4 and one for IPv6). This demonstrates proper rule lifecycle management from creation through deletion.


## Step 4.2: Verify System State After Rule Deletion

**Command Executed**:

sudo ufw status numbered

Status: active

     To                         Action      From
     --                         ------      ----
[ 1] 22/tcp                     ALLOW IN    Anywhere
[ 2] 22/tcp (v6)                ALLOW IN    Anywhere (v6)

Verification Result: After deleting the deny rules for port 23, only the SSH allow rules remain. The system is in a state where port 22 (SSH) is explicitly allowed while port 23 (Telnet) is now handled by the default deny policy. The firewall still provides protection while the temporary test rule has been removed.


## Step 4.3: Detailed Firewall Configuration Summary

**Command Executed**:

sudo ufw status verbose

Status: active
Logging: on (low)
Default: deny (incoming), allow (outgoing), disable (routed)
New profiles: skip

To                         Action      From
--                         ------      ----
22/tcp                     ALLOW IN    Anywhere
22/tcp (v6)                ALLOW IN    Anywhere (v6)

Configuration Analysis: This verbose output provides complete firewall policy information. The default policy is set to deny all incoming traffic, allow all outgoing traffic, and disable routing. This is a standard secure configuration where the system must explicitly allow inbound traffic while being permissive about outbound traffic. Logging is enabled at low level to capture blocked connections without overwhelming the system with log data. This configuration represents a reasonable balance between security and usability.

## Technical Observations and Findings

## Firewall Behavior Analysis
The firewall exhibited expected stateful behavior throughout testing. When telnet and netcat attempted connections to port 23, the firewall immediately rejected them with "Connection refused" messages. This indicates the firewall is performing active packet inspection and rejection at the network layer. The rejection happens before any application layer service would respond.

## Rule Duplication Prevention
One unexpected but beneficial behavior was UFW's intelligent handling of duplicate rule additions. When the system attempted to add rules that already existed, UFW recognized this and skipped the additions rather than creating duplicates. This is a safety feature that prevents accidental rule multiplication in production environments.

## IPv6 Rule Symmetry
Every IPv4 rule has a corresponding IPv6 rule. This is important because modern systems support both IPv4 and IPv6 traffic. A firewall that only protected IPv4 would leave systems vulnerable to IPv6-based attacks. The system properly created parallel rules for both protocols.

## Rule Ordering and Evaluation
The numbered output clearly shows the order in which rules are evaluated. UFW evaluates rules from top to bottom and applies the first matching rule. This ordering is critical for complex firewall configurations where rule sequence determines security outcomes.

## Security Assessment

## What This Configuration Protects Against
This firewall configuration prevents direct access to port 23, eliminating one common attack vector. Telnet is a legacy protocol that transmits credentials in plaintext, making blocking it a fundamental security practice. The explicit allow rule for port 22 ensures SSH access remains available while the default deny policy prevents access to unknown ports and services.

## Demonstrated Security Understanding
Through the configuration and testing process, the following security principles were demonstrated: The importance of blocking legacy insecure protocols, the value of explicit allow rules for required services, the proper use of default deny policies, and the systematic approach to validating security configurations through testing.

## Limitations of This Configuration
This firewall configuration protects only at the port level. Application-layer attacks that exploit vulnerabilities in services running on open ports would not be blocked. DDoS attacks sending massive traffic volumes might overwhelm firewall capacity. The configuration allows SSH from any source, which could be restricted further by IP address. No logging alerts were configured to notify administrators of blocked connection attempts.


## Key Learnings and Conclusions
Firewall configuration requires careful planning to avoid unintended service disruptions. Systematic testing is essential to validate that firewall rules function as intended. Default deny policies provide better security than default allow. Explicit allow rules make security posture intentional and auditable. IPv6 must be considered alongside IPv4 in modern network security. UFW successfully abstracts complex iptables syntax into manageable commands.
The exercise demonstrated that firewalls function as traffic gatekeepers, making binary allow or deny decisions based on configured rules. Understanding rule order, protocol types, direction (inbound vs outbound), and port numbers is essential for effective firewall management.
