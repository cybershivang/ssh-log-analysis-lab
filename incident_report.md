# Incident Report: SSH Authentication Attacks

**Analyst:** Shivang
**Data source:** Linux SSH auth log (sample log with simulated attacks) and real log from my own Kali VM
**Tools:** grep, cut, sort, uniq, journalctl

## 1. Summary
Analysis of the SSH log found four suspicious source IPs. One of them
(203.0.113.77) logged in successfully after repeated failures, which is a
possible account compromise and needs immediate response.

## 2. Findings

| ID | Severity | Source IP | Target | Activity | Time window | MITRE |
|---|---|---|---|---|---|---|
| INC-1 | CRITICAL | 203.0.113.77 | deploy | 6 failed logins, then successful login | 13:00:10 to 13:01:30 | T1110.001, T1078 |
| INC-2 | HIGH | 203.0.113.50 | root | 8 failed logins on one account (brute force) | 02:30:10 to 02:30:17 | T1110.001 |
| INC-3 | HIGH | 198.51.100.7 | 6 accounts (admin, test, oracle, ubuntu, git, ftp) | 1 failed login per account (password spraying) | 05:00:01 to 05:00:06 | T1110.003 |
| INC-4 | MEDIUM | 192.0.2.99 | 6 non-existent users | Invalid user probing (enumeration) | 06:00:01 to 06:00:06 | T1087.001 |
| INC-5 | LOW | 10.0.0.99 | alice | Successful publickey login at 03:12, outside working hours; no failures from this IP | 03:12:00 | T1078 |

Normal activity (not alerted): bob had 1 failed login from 10.0.0.12, which looks like a typo.

## 3. How it was detected
- Brute force: `grep "Failed password" auth.log | cut -d' ' -f11 | sort | uniq -c | sort -rn`
  (one IP with a high count of failures on a single account)
- Password spraying: same IP, many different usernames, few attempts each
- Enumeration: `grep "Invalid user" auth.log | cut -d' ' -f10 | sort | uniq -c | sort -rn`
- Compromise: `grep "Accepted" auth.log | grep "203.0.113.77"`
  (successful login from an IP that failed repeatedly)
- Off-hours: `grep "Accepted" auth.log | cut -d' ' -f3,9,11 | sort`

## 4. Timeline (INC-1, most serious)
- 13:00:10 to 13:00:15: 6 failed password attempts for `deploy` from 203.0.113.77
- 13:01:30: successful password login for `deploy` from the same IP

## 5. Recommended actions
1. INC-1: Treat the `deploy` account as compromised. Reset its password, kill active sessions, review what it did after 13:01:30.
2. Block 203.0.113.77, 203.0.113.50, 198.51.100.7 and 192.0.2.99 at the firewall.
3. Disable SSH password login and use key-based authentication.
4. Do not allow direct root login over SSH.
5. Install fail2ban to auto-block repeated failures.
6. INC-5: Confirm with alice that the 03:12 login was her. If not, escalate.

## 6. Real log from my Kali VM
Source IP 127.0.0.1 (my own machine, lab only):
- 5 non-existent usernames tried: fakeuser, admin, test, oracle, kali
- 9 failed logins between 17:39:02 and 17:42:10 (about 3 minutes)
- 17:43:31: successful login for `shivang` from the same IP
- Pattern matches enumeration followed by success after failures (T1087.001, T1110, T1078)

## 7. Limitations
- Analysis was done manually with command-line tools, no real-time alerting
- Sample log data is simulated and small
