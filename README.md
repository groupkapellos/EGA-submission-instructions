# EGA Data Submission – Upload & Metadata Linking Guide

This repository provides a practical guide for submitting sequencing data to the European Genome-phenome Archive, including file upload, encryption, and metadata linking.

## Overview

This guide covers:

- Secure connection to the EGA INBOX
- File encryption using Crypt4GH
- Uploading sequencing files
- Monitoring upload status
- Linking files to Samples and Runs
- Creating a Dataset
  
## Useful Links
[Submission Portal](https://submission.ega-archive.org/)\
[Crypt4GH Documentation](https://ega-archive.org/submission/data/file-preparation/crypt4gh/)\
[INBOX Upload Guide](https://ega-archive.org/submission/data/uploading-files/inbox/)\
[Tutorial Video](https://www.youtube.com/watch?v=iriB3MbEKG8)

---

<details>
<summary>1. Authentication (SSH Key Setup) - already configured for Kapellos lab</summary>

If you are using a shared lab account (e.g., PI account), authentication may already be configured.

EGA requires SSH authentication for secure uploads.

Generate SSH key (Windows)

```
ssh-keygen -t rsa -b 4096 -C "ega_submission"
```

Default files created:

C:\Users\<USERNAME>\.ssh\id_rsa\
C:\Users\<USERNAME>\.ssh\id_rsa.pub

>[!WARNING]
>Never share your private key (id_rsa).

### Register Public Key in EGA

Log in to EGA Submission Portal\
Go to Profile → Edit → Public Keys\
Copy contents of id_rsa.pub/
Paste and save

</details>

---

<details>
<summary>Connecting to EGA INBOX</summary>

### Option A: FileZilla (GUI)

**Using FileZilla**

Settings:

`Protocol: SFTP`\
`Host: inbox.ega-archive.org`\
`Logon Type: Key file`\
`User: EGA username`\
`Key file: id_rsa`\

<img width="886" height="471" alt="Screenshot 2026-05-04 153647" src="https://github.com/user-attachments/assets/61aa3530-0528-4bd4-8d1a-cdf0b25ec2f9" />


> [!NOTE]
> FileZilla may be unstable for large uploads.

### Option B: SFTP (Recommended)

More reliable for large datasets.

```
sftp -i ~/.ssh/id_ed25519 <username>@inbox.ega-archive.org
```

Example using PowerShell:

```
sftp -i ~/.ssh/id_ed25519 your_username@inbox.ega-archive.org
```

**Useful SFTP Commands**
- `lcd D`:              # set local directory\
- `cd encrypted`        # navigate to encrypted folder in EGA\
- `put file.c4gh`       # upload file\
- `ls`                  # list files in EGA folder\
- `lls`                 # list files in local directory

</details>

🔒 File Encryption

EGA requires files encrypted with Crypt4GH.

Option A: Automatic Encryption

Upload raw files to:

/to-encrypt

EGA will:

Encrypt files
Generate checksums
Option B: Manual Encryption (Used in this workflow)

Install Crypt4GH:

pip install crypt4gh
Create Public Key File

Save as ingestion.pubkey:

-----BEGIN CRYPT4GH PUBLIC KEY-----
SUtKgXbC5tBCzM69wvGvFl5qY5OR/+20s5ZyNSebRFw=
-----END CRYPT4GH PUBLIC KEY-----
Encrypt Files
crypt4gh encrypt --recipient_pk ingestion.pubkey < input.fastq.gz > output.fastq.gz.c4gh

⚠️ Do not overwrite input files.

📤 Uploading Files

Upload encrypted files to:

/encrypted

Using SFTP:

put output.fastq.gz.c4gh

⚠️ Uploads may be slow and require manual handling depending on network stability.

⏳ File Processing Status

After upload, EGA processes files and computes checksums.

Check status:
👉 https://submission.ega-archive.org/files

Status Meaning
Processing → wait
Error → contact helpdesk
Available → ready for metadata linking
🧬 Metadata Structure

EGA submission hierarchy:

Study
 ├── Samples
 ├── Experiments
 ├── Runs
 ├── Analyses (optional)
 └── Dataset
📊 Sample Metadata

Example file: samples.csv

alias,title,description,biological_sex,subject_id,phenotype,biosample_id,case_control,organism_part
B1,mixed,multiplexed pool B1,...,unknown,many,mixed,B1,both,mixed
Notes
alias must match Run metadata
Multiplexed samples → use subject_id = many
🧾 Run Metadata

Example file: runs.csv

sample,file1
B1,/file_R1.fastq.gz.c4gh
B1,/file_R2.fastq.gz.c4gh
Rules
File names must match exactly those in INBOX
Include .c4gh extension
One row per file
⚠️ Analyses Section

Not used in this project.

Reason:

Data already processed / multiplexed
No raw analysis objects submitted
📦 Dataset Creation

After linking:

Create Dataset
Attach Runs
Define access policy
Submit
⚠️ Common Pitfalls
FileZilla connection drops
Slow or manual uploads required
Confusion between /to-encrypt and /encrypted
File size display (~49 KB) is not meaningful
Metadata mismatches (sample/file names must be exact)
✅ Submission Checklist
 SSH key configured
 Files encrypted (.c4gh)
 Files uploaded to INBOX
 Status = Available
 Samples uploaded
 Runs linked correctly

