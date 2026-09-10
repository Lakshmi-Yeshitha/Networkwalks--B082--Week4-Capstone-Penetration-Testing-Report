# Networkwalks--B082--Week4-Capstone-Penetration-Testing-Report

# W4-Capstone – Penetration Testing Report

**W4 | CYBERSECURITY | NETWORKWALKS**

| Field | Detail |
|-------|--------|
| **Pentester Name (Cybersecurity Professional)** | **Velpula Sai Lakshmi Yeshitha** |
| **Program / Batch** | B082 – Networkwalks |
| **Modules Completed** | W4 – Capstone Project |
| **Client / Target** | Mediroza General Hospital – Authorized Web Application |
| **Permission Secured from Client?** | Yes – Written Authorization |
| **Assessment Type** | Black-box Web Application Security Assessment |
| **Phases Covered** | Reconnaissance, Vulnerability Identification, Controlled Validation & Documentation |
| **Classification** | Confidential |

---

# 1. Liability Disclaimer

I performed these activities only on the authorized **Mediroza General Hospital** web application with proper written permission as part of my Cybersecurity Internship.

This project was completed for educational and training purposes in a controlled environment.

All testing activities were performed within the agreed scope.

> **Disclaimer:** Unauthorized scanning, exploitation, password testing, or access to systems and data is not allowed. Security testing should always be performed only with explicit permission from the system owner.

---

# 2. Introduction

This report covers the practical cybersecurity activity completed during **Week 4 – Capstone Project** of my **Cybersecurity & Ethical Hacking Internship**.

The main objective of this project was to understand the process of conducting a **Black-box Web Application Security Assessment** on an authorized target.

The assessment focused on identifying security weaknesses related to:

- Login Authentication
- Username Enumeration
- SQL Injection
- Access Control
- PDF File Protection
- Password Strength
- File Metadata
- Directory Listing
- Backup File Exposure
- Sensitive Information Exposure

**Target Website**

```text
https://medirozahospital.com
```

The purpose of this exercise was to understand how multiple security weaknesses can be connected together and how security professionals document their findings and recommend appropriate remediation.

---

# 3. Scope and Methodology

## 3.1 Scope

The assessment was limited to the authorized target:

```text
https://medirozahospital.com
```

### Excluded Activities

- Social Engineering
- Denial-of-Service (DoS) Testing
- Testing Outside the Agreed Target
- Unauthorized Access to Unrelated Systems

## 3.2 Methodology

The penetration testing followed four structured phases.

### Phase 1 – Reconnaissance

- Collected basic information about the authorized web application.
- Identified important pages, directories and accessible resources.

### Phase 2 – Vulnerability Identification

- Tested authentication mechanisms.
- Checked user input validation.
- Examined file access controls.
- Reviewed application configuration.

### Phase 3 – Controlled Validation

- Verified discovered vulnerabilities inside the authorized environment.
- Assessed their potential security impact.

### Phase 4 – Documentation

- Recorded findings.
- Collected supporting evidence.
- Assessed risk levels.
- Provided remediation recommendations.

---

# 4. Tools Used

The following tools were used during the assessment.

| Tool | Purpose |
|------|---------|
| **Kali Linux** | Operating system used for cybersecurity testing activities |
| **curl** | Sending HTTP requests and viewing web server responses |
| **Browser Developer Tools** | Inspecting page behaviour and login form requests |
| **Networkwalks Hash Calculator** | Extracting password hashes from PDF files |
| **Networkwalks Password Cracker** | Testing PDF password strength using wordlists |
| **qpdf** | Decrypting authorized password-protected PDF files after password recovery |
| **exiftool** | Reading metadata from PDF files |
| **wget** | Downloading files from the authorized web server |
| **ChatGPT** | Converting raw SQL data into readable tables for analysis |

---

# 5. Activities Performed

## 5.1 Username Enumeration

I tested the login page to understand how the application responded to different usernames.

### Test 1

**Username**

```text
bob
```

**Password**

```text
test123
```

**Response**

```text
Username not found
```

---

### Test 2

**Username**

```text
admin
```

**Password**

```text
test123
```

**Response**

```text
Incorrect password
```

---

### Finding

The application returned different responses depending on whether the username existed.

This indicated that the application could potentially allow **Username Enumeration**.

### What I Learned

- Different login error messages can reveal whether a username exists.
- Attackers can use this information to identify valid user accounts.

---

## 5.2 SQL Injection Validation

I tested the login form for SQL Injection using controlled input.

### Test Input

**Username**

```text
admin'
```

**Password**

```text
test123
```

### Response

The application returned a **database syntax error**.

This indicated that the username input was being processed directly by the database query.

The application appeared to construct a query similar to:

```sql
SELECT *
FROM users
WHERE username='admin''
AND password='test123';
```

### Controlled Validation

I then tested a SQL Injection authentication-bypass payload in the authorized environment.

```text
admin' --
```

The application accepted the input and allowed access without requiring the correct password.

### What I Learned

- SQL Injection occurs when user input is directly inserted into database queries.
- Using parameterized queries helps prevent SQL Injection attacks.

---

## 5.3 Patient PDF Access Validation

After the controlled authentication-bypass test, I observed that the patient portal provided access to three PDF reports.

### Available PDF Files

```text
patient_report_1.pdf
patient_report_2.pdf
patient_report_3.pdf
```

These files contained confidential patient information.

### Finding

The authentication weakness allowed access to files that should have been protected by proper authorization controls.

### What I Learned

- Authentication and access-control weaknesses can expose sensitive documents.
- Sensitive files should only be accessible to authorized users.

---

## 5.4 PDF Password Strength Testing

The PDF files were password protected.

I used the authorized **Networkwalks** tools to assess the strength of the PDF passwords using wordlists.

### Results

| File | Password Identified During Authorized Testing |
|------|-----------------------------------------------|
| **patient_report_1.pdf** | `123456` |
| **patient_report_2.pdf** | `password` |
| **patient_report_3.pdf** | `!@#$%^&` |

### Observation

- Reports **1** and **2** were identified using the built-in wordlist.
- Report **3** required a larger password wordlist.

### Finding

The passwords were weak and could be identified using commonly available password lists.

### What I Learned

- Strong passwords are important for protecting confidential documents.
- Passwords based on common words or simple patterns provide limited security.

---

## 5.5 PDF Metadata Analysis

I analyzed the metadata of the third PDF after authorized password recovery.

### Command Used

```bash
qpdf --password='!@#$%^&' --decrypt patient_report_3.pdf report3_open.pdf
```

I then used:

```bash
exiftool report3_open.pdf
```

### Finding

The metadata contained information similar to:

```text
Author   : j.malik
Comments : DB backup moved to /old before site migration, do not delete
```

This revealed an internal comment referring to an old backup directory.

### What I Learned

- Document metadata can unintentionally expose internal information.
- Metadata should always be reviewed and removed before sharing sensitive documents.

---

## 5.6 Forgotten Backup Folder

Based on the authorized assessment and metadata observation, I checked the referenced **`/old/`** directory.

### Directory URL

```text
https://medirozahospital.com/old/
```

The directory listing was enabled.

The listing exposed a database backup file:

```text
mediroza_db_backup_2019.sql
```

### Finding

A database backup was stored inside a publicly accessible web directory.

### What I Learned

- Sensitive backup files should never be stored inside publicly accessible web directories.
- Backup files should always be protected using proper access controls.

---

## 5.7 Database Backup Analysis

I reviewed the authorized database backup file to understand the potential impact of the exposure.

The backup contained sensitive information related to hospital employees and shareholders.

### Employee Information Included

- Name
- Job Title
- Department
- Email Address
- Phone Number
- National ID Information
- Monthly Salary

### Shareholder Information Included

- Shareholder Name
- Share Percentage
- Share Class

The raw SQL information was converted into readable tables for easier analysis.

### Finding

Sensitive organizational and employee information was stored in an unprotected database backup that was publicly accessible through the web server.

### What I Learned

- Database backups can contain highly sensitive information.
- Backups should always be securely stored.
- Public access to backup files should be completely restricted.

---

# 6. Risk Analysis / Impact

Based on the assessment, I identified the following security findings.

| # | Risk / Finding | Evidence / Observation | Potential Impact | Risk Level |
|---|----------------|------------------------|------------------|------------|
| **1** | Username Enumeration | Different responses for valid and invalid usernames | Attackers may identify valid user accounts | **Medium** |
| **2** | SQL Injection | Database error and controlled authentication bypass | Unauthorized account access | **Critical** |
| **3** | Sensitive PDF Access | Patient reports accessible after authentication weakness | Exposure of confidential documents | **High** |
| **4** | Weak PDF Passwords | Passwords identified using wordlists | Unauthorized document access | **High** |
| **5** | Sensitive PDF Metadata | Internal backup information found in metadata | Information Disclosure | **Medium** |
| **6** | Public Backup Directory | `/old/` directory exposed SQL backup | Unauthorized access to backup data | **Critical** |
| **7** | Sensitive Data in Backup | Employee and shareholder information stored in plain text | Major confidentiality and privacy impact | **Critical** |

### Overall Risk

> **Overall Risk Level: 🔴 CRITICAL**

The findings demonstrated how multiple individual weaknesses could combine to create a much larger security impact.

---

# 7. Attack Chain Summary

The assessment demonstrated the following controlled attack chain:

1. The login page revealed whether usernames existed.
2. The login form was tested for SQL Injection.
3. A controlled SQL Injection test resulted in authentication bypass.
4. The patient portal exposed three PDF reports.
5. The PDF passwords were tested using authorized wordlists.
6. PDF metadata revealed information about an old backup directory.
7. The **`/old/`** directory had directory listing enabled.
8. A database backup was exposed in the directory.
9. The backup contained sensitive employee and shareholder information.

> This demonstrated how a combination of authentication, file security, metadata exposure, and server configuration weaknesses can significantly increase the overall impact of a security incident.

---

# 8. Recommendations

Based on the findings, I recommend the following security improvements.

## 1. Fix Username Enumeration

Use one generic error message for all failed login attempts.

**Example**

```text
Invalid credentials. Please try again.
```

---

## 2. Fix SQL Injection

Use **parameterized queries** or **prepared statements** instead of directly inserting user input into SQL queries.

**Example**

```php
$stmt = $pdo->prepare(
    "SELECT * FROM users WHERE username = ? AND password = ?"
);

$stmt->execute([$username, $password]);
```

---

## 3. Protect Patient Documents

- Store sensitive PDF files outside the public web root.
- Allow access only through server-side authorization checks.

---

## 4. Improve Password Security

- Use strong and unique passwords.
- Enforce secure password policies.
- Avoid common words or predictable patterns.

---

## 5. Remove Sensitive Metadata

Remove unnecessary metadata before sharing documents.

**Example**

```bash
exiftool -all= patient_report_3.pdf
```

---

## 6. Disable Directory Listing

Disable directory indexing on the web server.

**Apache Example**

```apache
Options -Indexes
```

---

## 7. Remove Public Backups

- Never store database backups inside publicly accessible web directories.
- Store backups in secure, access-controlled locations outside the web root.

---

## 8. Protect Sensitive Database Information

Protect employee, financial, and organizational information using:

- Access Controls
- Encryption
- Secure Storage
- Least Privilege Access

---

## 9. Conduct Regular Security Assessments

Perform regular authorized:

- Vulnerability Assessments
- Penetration Tests
- Security Reviews

to identify and fix weaknesses before they can be exploited.

---

# 9. What I Learned

During this project, I learned:

- How to perform a basic **Black-box Web Application Security Assessment**.
- How **Username Enumeration** works.
- How **SQL Injection** can affect authentication.
- How authentication weaknesses can lead to sensitive file exposure.
- How to assess PDF password strength using wordlists.
- How to use **qpdf** for authorized PDF decryption.
- How to use **exiftool** to inspect PDF metadata.
- How directory listing can expose sensitive files.
- How database backups can contain sensitive information.
- How multiple vulnerabilities can form an attack chain.
- How to analyze the security impact of identified vulnerabilities.
- How to provide remediation recommendations.
- How to document penetration testing findings professionally.
- The importance of performing security testing only with proper authorization.

---

# 10. Evidence Collected

The following screenshots were collected during the assessment.

---

## 1. Mediroza Webpage

**Evidence**

- Screenshot showing the authorized Mediroza General Hospital web application homepage used during the assessment.


<img width="1917" height="887" alt="Screenshot 2026-09-10 173626" src="https://github.com/user-attachments/assets/32beba95-193a-458c-bac9-e56f987c7277" />

---


## 2. Patient Portal

**Evidence**

- Screenshot showing successful access to the authorized patient portal after the controlled validation.


<img width="937" height="787" alt="Screenshot 2026-09-10 174214" src="https://github.com/user-attachments/assets/d725b44b-4c8f-4f3b-b76b-2143d7c5322c" />

---


## 3. SQL Warning

**Evidence**

- Screenshot showing the SQL database warning/error generated during the controlled SQL Injection test.


<img width="932" height="790" alt="Screenshot 2026-09-10 174751" src="https://github.com/user-attachments/assets/7a4f5441-b10a-4fe4-bb72-db05bb7e9930" />

---


## 4. Lab Reports

**Evidence**

- Screenshot showing the list of available patient reports within the authorized patient portal.

```text
patient_report_1.pdf
patient_report_2.pdf
patient_report_3.pdf
```


<img width="932" height="826" alt="Screenshot 2026-09-10 174929" src="https://github.com/user-attachments/assets/e180f9fd-70d0-4457-a233-8077944d5db0" />

---


## 5. Hash Table

**Evidence**

- Screenshot showing the password hashes extracted from the protected PDF files using the authorized Networkwalks Hash Calculator.


<img width="903" height="621" alt="Screenshot 2026-09-10 175642" src="https://github.com/user-attachments/assets/c12967cc-278c-40f6-a873-bc0f2561730c" />

---


## 6. Password Cracker

**Evidence**

- Screenshot showing the Networkwalks Password Cracker identifying the PDF passwords using authorized wordlists.


<img width="920" height="675" alt="Screenshot 2026-09-10 175657" src="https://github.com/user-attachments/assets/c0cf213f-c6c4-4ad5-aa1a-93262e9c276b" />

---


## 7. Lab Report 1

**Evidence**

- Screenshot showing **patient_report_1.pdf** opened after successful password recovery during the authorized assessment.


<img width="927" height="841" alt="Screenshot 2026-09-10 175737" src="https://github.com/user-attachments/assets/c09f6e43-0534-4acf-b06e-c5ae39ac6549" />


---


## 8. Lab Report 2

**Evidence**

- Screenshot showing **patient_report_2.pdf** opened after successful password recovery during the authorized assessment.


<img width="946" height="942" alt="Screenshot 2026-09-10 175931" src="https://github.com/user-attachments/assets/198e2cd1-009b-4cb4-8dd0-e16b00581c3e" />

---


## 9. Lab Report 3

**Evidence**

- Screenshot showing **patient_report_3.pdf** opened after successful password recovery during the authorized assessment.


<img width="926" height="901" alt="Screenshot 2026-09-10 182344" src="https://github.com/user-attachments/assets/64dbf2a8-47ab-4c25-a710-aab6f715941d" />


---


## 10. report_3_open.pdf

**Evidence**

- Screenshot showing the decrypted **report_3_open.pdf** file opened for metadata analysis.

- Metadata inspected using:

```bash
exiftool report_3_open.pdf
```


<img width="877" height="628" alt="Screenshot 2026-09-10 193830" src="https://github.com/user-attachments/assets/904a4740-cf35-4e64-b3b0-1d7add5b270b" />

---


## 11. SQL All Data

**Evidence**

- Screenshot showing the contents of **mediroza_db_backup_2019.sql** during the authorized database backup analysis.

- The SQL backup contained employee records, shareholder information, and other organizational data used for security analysis.


<img width="1917" height="1017" alt="Screenshot 2026-09-10 194440" src="https://github.com/user-attachments/assets/88aa78c1-c2b6-4e8b-be1b-d8502b71c338" />


---


## 12. Readable SQL Tables

**Evidence**

- Excel sheet showing the SQL backup converted into readable tables for easier analysis.


[📊 View SQL Excel Sheet](SQL_Table.xlsx)


### Shareholders Information

- Share Percentage
- Share Class


### Staff

- Job Title
- Department
- Monthly Salary(ZAR)

---

# 11. Conclusion

During **Week 4 – Capstone Project** of my **Cybersecurity & Ethical Hacking Internship**, I completed a practical **Black-box Web Application Security Assessment** on an authorized target.

The assessment covered:

- Authentication Testing
- SQL Injection Validation
- Sensitive File Access
- PDF Password Testing
- Metadata Analysis
- Directory Listing
- Database Backup Exposure

This project helped me understand how individual security weaknesses can combine to create a larger attack chain and significantly increase the overall information security risk.

I also learned how to:

- Document penetration testing findings professionally.
- Assess the impact of identified vulnerabilities.
- Recommend practical remediation measures.
- Follow an ethical and authorized approach to security testing.

All activities were performed in a controlled environment with proper written authorization.

---

## Submitted By

| Field | Details |
|--------|---------|
| **Pentester** | **Velpula Sai Lakshmi Yeshitha** |
| **Cybersecurity Mentor** | Waqas Karim, CCIE |
| **Organization** | Networkwalks |
| **Batch** | B082 |
| **Project** | Week 4 – Capstone Project |

---

> **Disclaimer:** This report was produced as part of a controlled educational exercise conducted by **Networkwalks**. All testing activities were performed only with explicit written authorization. These techniques must never be used against any system without prior permission from the system owner.

---
