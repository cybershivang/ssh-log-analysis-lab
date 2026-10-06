# SSH Log Analysis Lab (SOC Analyst Practice)
Kali Linux | grep, cut, sort, uniq | Regex | MITRE ATT&CK

Analysis of Linux SSH authentication logs to detect attacks and write an
incident report, like a Tier 1 SOC analyst would.

## What's in this repo
- `incident_report.md`: findings, severity, timeline, recommended actions
- `detection_rules.md`: detection logic, thresholds, severity and MITRE mapping
- `report.txt`, `auth.log`: sample log and command output
- `real_*.txt`: output from real SSH events on my own Kali VM
- Screenshots of each step

## Key results
- 4 attacker IPs found in the sample log, 1 CRITICAL (login success after failures)
- Real VM test: 9 failed logins in about 3 minutes across 5 fake usernames, followed by a successful login
- Findings mapped to MITRE ATT&CK: T1110.001, T1110.003, T1087.001, T1078

## Skills shown
Log analysis, attack pattern recognition, severity rating, incident
reporting, Linux command line, MITRE ATT&CK mapping

## Screenshots
![Fake log](02_fake_auth_log.png)
![Detection results](05_detection_results.png)
![Real log analysis](10_real_log_analysis.png)
![Report](11_report_output.png)
![Real enumeration](12_real_enumeration.png)
![Real compromise and timing](13_real_compromise_timing.png)

## Next steps
- Load the same logs into Splunk and build an alert
- Automate the checks with a script
