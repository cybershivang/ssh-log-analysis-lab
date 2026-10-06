# Detection Logic Used

| Detection | Logic | Threshold I used | Severity | MITRE |
|---|---|---|---|---|
| SSH brute force | Same IP, many "Failed password" on one account | 5 or more failures | HIGH | T1110.001 |
| Password spraying | Same IP, failures across many different usernames | 5 or more different users | HIGH | T1110.003 |
| Username enumeration | Same IP, many "Invalid user" events | 5 or more | MEDIUM | T1087.001 |
| Success after failures | "Accepted" login from an IP that already failed repeatedly | 5 or more failures before success | CRITICAL | T1078 |
| Off-hours login | "Accepted" login outside 08:00 to 20:00 | any | LOW (HIGH if IP also failed) | T1078 |

## Notes on tuning
- A single failed login (like a typo) should not alert. That is why the threshold is 5.
- Spraying vs brute force: brute force is many attempts on ONE account, spraying is few attempts on MANY accounts.
- Off-hours alone is low severity, but off-hours plus earlier failures from the same IP is high.
- 
