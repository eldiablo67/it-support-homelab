# Case 001: DNS Resolution Failure

## Summary

In a guided Windows 11 virtual-machine lab, an incorrect DNS server was deliberately configured. Default DNS queries timed out. A direct query to the original DNS server succeeded, isolating the client DNS configuration as the fault. Restoring automatic DNS settings restored successful default lookups.

## Environment

- Windows 11 virtual machine; adapter: Ethernet0
- IPv4 address: 172.16.222.128
- Subnet mask: 255.255.255.0
- Default gateway and working DNS server: 172.16.222.2
- DHCP server: 172.16.222.254
- Deliberately incorrect DNS server: 192.0.2.1

## Investigation

1. Recorded the working configuration with `ipconfig /all`. A baseline `nslookup example.com.` used 172.16.222.2 and returned 104.20.23.154.
2. Manually changed the client DNS server to 192.0.2.1.
3. Confirmed the changed DNS address in `ipconfig /all`. The default `nslookup example.com` repeatedly timed out against 192.0.2.1.
4. Ran `nslookup example.com. 172.16.222.2` while the incorrect default DNS setting remained. This returned 104.20.23.154, demonstrating that the original DNS service was reachable and answered the query.
5. Opened Network Connections with `ncpa.cpl`, then restored automatic DNS in the adapter IPv4 properties.
6. Confirmed DNS server 172.16.222.2 in `ipconfig /all` and ran `nslookup example.com.` without a server override. The lookup returned 104.20.23.154.

## Root cause

The client was configured to query 192.0.2.1 rather than its working DNS server, 172.16.222.2.

## Resolution and verification

Restored automatic DNS configuration. The final default lookup succeeded using 172.16.222.2. The case is resolved within the tested scope: DNS lookup recovery. Browser access, customer impact, and general Internet connectivity were not independently verified.

## Evidence notes

Screenshots were captured during the exercise but are not yet included in this repository. See [the evidence checklist](screenshots/README.md).

The baseline screenshot includes both `example.com` and `example.com.` queries. The first displays `example.com.localdomain`; the dotted query displays `example.com`. This report uses the dotted baseline for comparison with final verification.

An initial `ns lookup` typo produced a command-not-recognized error. This was separate from the DNS timeout reproduced with the correctly spelled `nslookup` command.

## Skills practiced

- Reviewing Windows client network configuration
- Reproducing a DNS configuration fault
- Comparing default and explicitly selected DNS servers
- Restoring settings and validating the result
- Documenting evidence and limiting conclusions to observed results
