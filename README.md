# EGA data submission - upload & metadata linking guide

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

### If you are using a shared lab account (e.g., PI account), authentication may already be configured.

EGA requires SSH authentication for secure uploads.

Generate SSH key (Windows)

```bash
ssh-keygen -t rsa -b 4096 -C "ega_submission"
```

Default files created:

C:\Users\<USERNAME>\.ssh\id_rsa\
C:\Users\<USERNAME>\.ssh\id_rsa.pub

>Never share your private key (id_rsa).

### Register Public Key in EGA

Log in to EGA Submission Portal\
Go to Profile → Edit → Public Keys\
Copy contents of id_rsa.pub/
Paste and save

</details>

---

<details>
<summary>2. Connecting to EGA INBOX</summary>

### Option A: FileZilla (GUI)

**Using FileZilla**

Settings:

`Protocol: SFTP`\
`Host: inbox.ega-archive.org`\
`Logon Type: Key file`\
`User: EGA username`\
`Key file: id_rsa`\

<img width="886" height="471" alt="Screenshot 2026-05-04 153647" src="https://github.com/user-attachments/assets/61aa3530-0528-4bd4-8d1a-cdf0b25ec2f9" />


> FileZilla may be unstable for large uploads.

### Option B: SFTP (Recommended)

More reliable for large datasets.

```PowerShell
sftp -i ~/.ssh/id_ed25519 <username>@inbox.ega-archive.org
```

Example using PowerShell:

```PowerShell
sftp -i ~/.ssh/id_ed25519 your_username@inbox.ega-archive.org
```

**Useful SFTP Commands**
- `lcd D`:              # set local directory\
- `cd encrypted`        # navigate to encrypted folder in EGA\
- `put file.c4gh`       # upload file\
- `ls`                  # list files in EGA folder\
- `lls`                 # list files in local directory

</details>

---


<details>
<summary>3. File Encryption</summary>

### EGA requires files encrypted with Crypt4GH.

### Option A: Automatic Encryption

Using FileZilla, upload raw files to:

/to-encrypt

EGA will:

1. Encrypt files
2. Generate checksums
   
### Option B: Manual Encryption (Used in previous workflow - and recommended)

The next step, `Install Crypt4GH`, must be completed in a Linux environment.

Install Crypt4GH:

```bash
pip install crypt4gh
```

Save the EGA Ingestion Public Key

Use the public key provided below to encrypt the files for EGA ingestion. You do not need to generate a new key.

Create the file using a text editor such as nano:

nano ingestion.pubkey

Paste the following key exactly as shown:

```bash
-----BEGIN CRYPT4GH PUBLIC KEY-----
SUtKgXbC5tBCzM69wvGvFl5qY5OR/+20s5ZyNSebRFw=
-----END CRYPT4GH PUBLIC KEY-----
```

Save the file by pressing Ctrl+O, then Enter, and exit with Ctrl+X.

The resulting ingestion.pubkey file will be used in the Crypt4GH encryption command.


Encrypt Files

```PowerShell
crypt4gh encrypt --recipient_pk ingestion.pubkey < input.fastq.gz > output.fastq.gz.c4gh
```

> Do not overwrite input files. I'd recommend adding a copy of the files first in a folder called "to encrypt" and the encrypted ones in another folder called "encrypted"

</details>

---

<details>
<summary>4. Uploading Files</summary>

### Upload encrypted files to:

/encrypted

Or using SFTP:

```PowerShell
put output.fastq.gz.c4gh
```

> Uploads may be slow and require manual handling depending on network stability.

### File Processing Status

After upload, EGA processes files and computes checksums.\
[Check status](https://submission.ega-archive.org/files)

**Status Meaning:**
- Processing → wait
- Error → contact helpdesk
- Available → ready for metadata linking
  
</details>

---

<details>
<summary>5. Metadata Structure</summary>

### EGA submission hierarchy:

Study\
 ├── Samples\
 ├── Experiments\
 ├── Runs\
 ├── Analyses (optional)\
 └── Dataset

### Sample Metadata

Example file: [sample.csv](https://github.com/groupkapellos/EGA-submission-instructions/blob/main/sample.csv)

> Alias must match Run metadata


### Run Metadata

Example file: [runs.csv](https://github.com/groupkapellos/EGA-submission-instructions/blob/main/runs.csv)

**Rules**
- File names must match exactly those in INBOX
- Include .c4gh extension
- One row per file

### Analyses Section

The Analyses section in EGA is intended for processed or derived data files (e.g., alignment files, variant calls, or other downstream results).\
In this submission, only sequencing files (FASTQ) are provided, and therefore analyses objects are not required.

Reason:
- Data already processed / multiplexed
- No raw analysis objects submitted

 </details>

 ---

## Submission Checklist
- [x] SSH key configured
- [x] Files encrypted (.c4gh)
- [x] Files uploaded to INBOX
- [x] Status = Available
- [x] Samples (csv) uploaded
- [x] Runs (csv) linked correctly


