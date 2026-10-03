<div align="center">

# 🔐 Web Application Penetration Testing Lab

### Week 04 — Black-Box Web Application Security Assessment

**Networkwalks Cybersecurity Internship · Batch B083F**

<br>

![Kali Linux](https://img.shields.io/badge/Kali%20Linux-Rolling-557C94?style=for-the-badge\&logo=kalilinux\&logoColor=white)
![Web Security](https://img.shields.io/badge/Web%20Security-Pentesting-1F6FEB?style=for-the-badge)
![SQL Injection](https://img.shields.io/badge/SQL%20Injection-Tested-D73A49?style=for-the-badge)
![Status](https://img.shields.io/badge/Assessment-Completed-2EA44F?style=for-the-badge)

</div>

---

## 📌 Project Overview

This repository documents the completion of **Week 04** of the **Networkwalks Cybersecurity Internship — Batch B083F**.

The exercise involved an **authorized black-box web application penetration test** against the designated training target:

```text
https://medirozahospital.com
```

The assessment was performed from an external, unauthenticated attacker's perspective and focused on identifying vulnerabilities, demonstrating their practical impact, and documenting appropriate remediation measures.

The assessment followed a continuous attack path from an initial authentication weakness to the exposure of confidential application and business data.

---

<div align="center">

## 🎯 Assessment Objectives

</div>

* Assess the security of the externally accessible patient portal.
* Test authentication and input-handling mechanisms.
* Identify and validate SQL injection vulnerabilities.
* Demonstrate the impact of authentication bypass.
* Assess protection applied to confidential PDF documents.
* Evaluate the strength of document passwords.
* Identify accidentally exposed legacy resources and backups.
* Analyse the security impact of exposed sensitive information.
* Document findings with reproducible technical evidence.
* Provide practical remediation and verification recommendations.

---

<div align="center">

## 🧪 Assessment Scope

</div>

| Category        | Details                                         |
| --------------- | ----------------------------------------------- |
| Assessment Type | Black-box Web Application Penetration Test      |
| Target          | `https://medirozahospital.com`                  |
| Client          | Mediroza General Hospital                       |
| Programme       | Networkwalks Cybersecurity Internship           |
| Batch           | B083F                                           |
| Week            | 04                                              |
| Access Level    | Unauthenticated / External Attacker Perspective |
| Authorization   | Written authorization provided                  |
| Environment     | Controlled training environment                 |

### Out of Scope

* Social engineering
* Denial-of-Service testing
* Unrelated third-party systems or infrastructure

All activities were restricted to the authorized assessment scope.

---

<div align="center">

# 🗺️ Attack Chain

</div>

```text
                    ┌──────────────────────────┐
                    │  Unauthenticated User   │
                    └────────────┬─────────────┘
                                 │
                                 ▼
                    ┌──────────────────────────┐
                    │   Patient Login Page     │
                    │ /patient/login.php       │
                    └────────────┬─────────────┘
                                 │
                                 ▼
                    ┌──────────────────────────┐
                    │     SQL Injection        │
                    │ Authentication Bypass    │
                    └────────────┬─────────────┘
                                 │
                                 ▼
                    ┌──────────────────────────┐
                    │   Authenticated Portal   │
                    │     Access Obtained      │
                    └────────────┬─────────────┘
                                 │
                                 ▼
                    ┌──────────────────────────┐
                    │  3 Protected PDF Reports │
                    │      Downloaded          │
                    └────────────┬─────────────┘
                                 │
                                 ▼
                    ┌──────────────────────────┐
                    │ PDF Hash Extraction &    │
                    │ Password Recovery        │
                    └────────────┬─────────────┘
                                 │
                                 ▼
                    ┌──────────────────────────┐
                    │   PDF Decryption         │
                    │ Confidential Data Access  │
                    └────────────┬─────────────┘
                                 │
                                 ▼
                    ┌──────────────────────────┐
                    │ Legacy /old/ Directory   │
                    │     Discovered            │
                    └────────────┬─────────────┘
                                 │
                                 ▼
                    ┌──────────────────────────┐
                    │ Exposed SQL Database      │
                    │       Backup              │
                    └────────────┬─────────────┘
                                 │
                     ┌───────────┴───────────┐
                     ▼                       ▼
             ┌───────────────┐      ┌────────────────┐
             │ Staff / Payroll│      │ Shareholder    │
             │ Information    │      │ Information    │
             └───────────────┘      └────────────────┘
```

---

<div align="center">

# 🧰 Tools & Technologies

</div>

| Tool               | Purpose                                            |
| ------------------ | -------------------------------------------------- |
| `curl`             | HTTP requests and authenticated resource retrieval |
| Browser            | Manual web application testing                     |
| `pdf2john`         | PDF password hash extraction                       |
| `pdfcrack`         | PDF password recovery                              |
| `qpdf`             | PDF decryption and validation                      |
| `pdftotext`        | Extracting text from decrypted documents           |
| `exiftool`         | Document metadata analysis                         |
| SQL analysis tools | Reviewing exposed database backup content          |

---

<div align="center">

# 🔎 M1 — Initial Access

</div>

## SQL Injection & Authentication Bypass

The patient login functionality was tested for input-handling vulnerabilities.

```text
https://medirozahospital.com/patient/login.php
```

Initial testing demonstrated that user-controlled input was reaching the backend SQL query without appropriate parameterisation.

A malformed input produced a database syntax error, providing evidence of SQL injection.

The authentication mechanism was subsequently bypassed using a classic SQL injection authentication-bypass technique.

The successful request resulted in:

```text
HTTP 302 Redirect
Location: portal.php
PHP Session Cookie Issued
```

This demonstrated that an authenticated session could be established without supplying the legitimate account password.

### Impact Demonstrated

* Authentication control bypass
* Access to protected patient functionality
* Unauthorised access to patient reports
* SQL injection became the entry point for the subsequent attack chain

### Evidence

```text
M1_01_SQLi_Authentication_Bypass.png
```

---

## Patient Portal Access

After authentication bypass, the patient portal became accessible.

The portal exposed three protected pathology/laboratory report downloads.

### Evidence

```text
M1_02_Patient_Portal_Three_Reports.png
```

The three report files were subsequently retrieved using the authenticated session.

### Evidence

```text
M1_03_Report_Download_Commands.png
M1_04_Downloaded_PDF_Files.png
```

---

<div align="center">

# 🔓 M2 — PDF Protection Assessment

</div>

The downloaded reports were protected using PDF password encryption.

The files could not initially be opened without their respective passwords.

PDF password hashes were extracted for controlled password-strength testing.

```text
pdf2john
```

### Evidence

```text
M2_01_PDF_Hash_Extraction.png
```

---

## Password Recovery

Dictionary-based password cracking was performed against the extracted PDF hashes.

All three document passwords were successfully recovered.

The recovered credentials demonstrated that the document protection relied on weak or predictable password choices.

### Evidence

```text
M2_02_Report1_Password_Cracked.png
M2_03_Report2_Report3_Passwords_Cracked.png
```

> Sensitive passwords are intentionally not reproduced in this public repository.

---

## PDF Decryption

After password recovery, the reports were decrypted using `qpdf`.

The resulting documents were validated as readable PDF files and their contents were successfully extracted.

### Evidence

```text
M2_04_PDFs_Decrypted_Successfully.png
M2_05_Decrypted_Report_Content.png
```

### Security Impact

This demonstrated that the document encryption provided limited practical protection when combined with predictable passwords.

The attack chain therefore progressed from:

```text
SQL Injection
      ↓
Authentication Bypass
      ↓
Patient Portal Access
      ↓
Report Download
      ↓
Password Recovery
      ↓
PDF Decryption
      ↓
Confidential Data Access
```

Sensitive patient information is not reproduced in this repository.

---

<div align="center">

# 🗄️ M3 — Legacy Database Exposure

</div>

Further analysis identified an exposed legacy directory:

```text
/old/
```

The directory contained an old SQL database backup:

```text
mediroza_db_backup_2019.sql
```

The backup was accessible through the web server and contained sensitive organisational information.

---

## Staff & Payroll Data

The database contained a `staff` table with records including:

* Staff names
* Job titles
* Departments
* Contact information
* National identification information
* Monthly salary information
* Joining dates

The assessment identified **30 staff records**.

### Evidence

```text
M3_01_Exposed_Staff_Database_Backup.png
```

---

## Shareholder Information

A separate `shareholders` table contained ownership-related information including:

* Shareholder names
* Share percentages
* Shares held
* Share classes

The assessment identified **10 shareholder records**.

### Evidence

```text
M3_02_Shareholder_Data_Exposure.png
```

Sensitive personal and business information is intentionally redacted or summarised in this repository.

---

<div align="center">

# ⚠️ Findings Summary

</div>

| #  | Finding                                         | Affected Resource                  | Risk     |
| -- | ----------------------------------------------- | ---------------------------------- | -------- |
| 01 | SQL Injection leading to Authentication Bypass  | `/patient/login.php`               | Critical |
| 02 | Weak Password Protection on Patient PDF Reports | `patient_report_1-3.pdf`           | High     |
| 03 | Public Exposure of Legacy Database Backup       | `/old/mediroza_db_backup_2019.sql` | Critical |

The findings form a connected attack chain rather than isolated vulnerabilities.

```text
Finding 01
SQL Injection
     │
     ▼
Finding 02
Patient Report Access
     │
     ▼
Finding 03
Legacy Database Exposure
```

---

<div align="center">

# 🛠️ Key Remediation Recommendations

</div>

### SQL Injection

* Replace dynamically constructed SQL queries with parameterised/prepared statements.
* Validate all authentication inputs server-side.
* Remove verbose database errors from production responses.
* Use least-privilege database accounts.
* Prevent username enumeration through generic authentication errors.

### Patient Documents

* Avoid predictable document passwords.
* Use strong, randomly generated passwords where password-based protection is required.
* Prefer authenticated, access-controlled document delivery over publicly retrievable files.
* Remove unnecessary metadata from patient-facing documents.

### Legacy Backup Exposure

* Remove database backups from web-accessible directories.
* Disable directory listing.
* Store backups in protected, non-public locations.
* Encrypt backups at rest.
* Implement backup retention and secure deletion procedures.
* Regularly scan web roots for accidental backup exposure.

---

<div align="center">

# 📸 Evidence Index

</div>

| Evidence                                      | Description                                    | Milestone |
| --------------------------------------------- | ---------------------------------------------- | --------- |
| `M1_01_SQLi_Authentication_Bypass.png`        | Successful SQL injection authentication bypass | M1        |
| `M1_02_Patient_Portal_Three_Reports.png`      | Authenticated patient portal                   | M1        |
| `M1_03_Report_Download_Commands.png`          | Report download commands                       | M1        |
| `M1_04_Downloaded_PDF_Files.png`              | Downloaded PDF files                           | M1        |
| `M2_01_PDF_Hash_Extraction.png`               | PDF hash extraction                            | M2        |
| `M2_02_Report1_Password_Cracked.png`          | Report 1 password recovery                     | M2        |
| `M2_03_Report2_Report3_Passwords_Cracked.png` | Reports 2 & 3 password recovery                | M2        |
| `M2_04_PDFs_Decrypted_Successfully.png`       | Successful PDF decryption                      | M2        |
| `M2_05_Decrypted_Report_Content.png`          | Decrypted report access                        | M2        |
| `M3_01_Exposed_Staff_Database_Backup.png`     | Staff database exposure                        | M3        |
| `M3_02_Shareholder_Data_Exposure.png`         | Shareholder data exposure                      | M3        |

> The unsuccessful John the Ripper attempt is intentionally excluded from the final evidence set because it did not contribute to the successful password recovery process.

---

<div align="center">

# 📁 Repository Structure

</div>

```text
NETWORKWALKS-B083F-WK4-WEB-PENTEST/
│
├── README.md
│
├── report/
│   └── Week04-Penetration-Testing-Report.pdf
│
├── evidence/
│   ├── M1/
│   │   ├── M1_01_SQLi_Authentication_Bypass.png
│   │   ├── M1_02_Patient_Portal_Three_Reports.png
│   │   ├── M1_03_Report_Download_Commands.png
│   │   └── M1_04_Downloaded_PDF_Files.png
│   │
│   ├── M2/
│   │   ├── M2_01_PDF_Hash_Extraction.png
│   │   ├── M2_02_Report1_Password_Cracked.png
│   │   ├── M2_03_Report2_Report3_Passwords_Cracked.png
│   │   ├── M2_04_PDFs_Decrypted_Successfully.png
│   │   └── M2_05_Decrypted_Report_Content.png
│   │
│   └── M3/
│       ├── M3_01_Exposed_Staff_Database_Backup.png
│       └── M3_02_Shareholder_Data_Exposure.png
│
└── .gitignore
```

---

<div align="center">

# 📚 Key Learning Outcomes

</div>

### 💉 Web Application Security

Understanding how unsafe database input handling can compromise authentication mechanisms.

### 🔐 Authentication Security

Understanding the importance of secure authentication logic and generic error handling.

### 📄 Document Security

Learning how weak document passwords can undermine otherwise encrypted files.

### 🗂️ Information Disclosure

Understanding the risks associated with forgotten directories, exposed backups, and directory listing.

### 🔍 Digital Forensics

Using metadata and document analysis to identify additional security-relevant information.

### 🛡️ Risk-Based Reporting

Translating technical vulnerabilities into business impact, risk ratings, remediation actions, and retest criteria.

---

<div align="center">

# 🔐 Ethical Use & Responsible Disclosure

All activities documented in this repository were performed against an **authorized cybersecurity training target** as part of the Networkwalks internship.

No testing was intentionally performed against unrelated systems.

Sensitive information recovered during the assessment has been **redacted, summarised, or omitted** from this public repository.

The techniques documented here must only be used against systems for which explicit authorization has been provided.

<br>

![Authorized Testing](https://img.shields.io/badge/Testing-Authorized-2EA44F?style=for-the-badge)
![Responsible Disclosure](https://img.shields.io/badge/Data-Redacted-F9A825?style=for-the-badge)

</div>

---

<div align="center">

# 👤 Author

### M. Thangamani

**Cybersecurity Intern · Networkwalks**

**Batch B083F**

**Week 04 — Web Application Penetration Testing**

<br>

![Cybersecurity](https://img.shields.io/badge/Focus-Cybersecurity-1F6FEB?style=flat-square)
![Internship](https://img.shields.io/badge/Internship-Networkwalks-557C94?style=flat-square)
![Status](https://img.shields.io/badge/Status-Completed-2EA44F?style=flat-square)

</div>
