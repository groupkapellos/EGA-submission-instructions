# EGA Data Submission – Upload & Metadata Linking Guide

This README describes how to:

1. Generate an SSH private key
2. Connect to the EGA INBOX using FileZilla
3. Upload sequencing files
4. Check file processing status
5. Link files to EGA metadata (Runs / Analyses)

---

1. Generate an SSH Key Pair (Windows)
EGA requires SSH key authentication for secure SFTP uploads.

Step 1: Open PowerShell
Press Win + R, type powershell, press Enter.

Step 2: Generate the key
Run:
`ssh-keygen -t rsa -b 4096 -C "ega_submission"` 

When prompted:
File location → press Enter (default)

Passphrase → optional but recommended

This creates:

C:\Users\<USERNAME>\.ssh\id_rsa       ← private key

C:\Users\<USERNAME>\.ssh\id_rsa.pub   ← public key

⚠️ Do NOT share id_rsa (private key).

---

2. Register the Public Key with EGA

Step 1: Log in to the EGA Submitter Portal

Step 2: Go to Profile / Edit

Step 3: Open Public Keys

Step 4: Open id_rsa.pub in a text editor

Step 5: Copy the full content and paste it into EGA

Step 6: Update key

This authorizes your account for SFTP uploads.

---

3. Connect to EGA INBOX Using FileZilla
Required

- FileZilla (FTP client)
- 
- Your EGA username
- 
- Your private key (id_rsa)
  
FileZilla Configuration

Step 1: Open FileZilla

Step 1: Go to File → Site Manager → New Site

Set:
Protocol:	SFTP – SSH File Transfer Protocol

Host:	inbox.ega-archive.org

Logon Type:	Key file

User:	Your EGA username (email)

Key file:	C:\Users\<USERNAME>\.ssh\id_rsa

Click Connect

Accept the host key when prompted


You will be logged into your EGA INBOX home directory.

---

4. Upload Files to the INBOX

Inside the INBOX you will see three folders:

/to-encrypt

/encrypted

/etc

Recommended workflow

Stable connection (default)

Upload unencrypted FASTQ files to:

/to-encrypt

EGA will:

Encrypt files using Crypt4GH

Generate encrypted + unencrypted checksums automatically

Important Notes About File Sizes

Files in /to-encrypt may appear as ~49 kB

This is a placeholder during encryption

The real file size is preserved internally

➡️ File size in INBOX is NOT meaningful

Check File Processing Status

After upload, EGA calculates encrypted checksums (can take up to 48 hours).

Check status here:

👉 https://submission.ega-archive.org/files

Error	Contact EGA Helpdesk

➡️ Only when status = “Available” can files be linked to Runs or Analyses.

---

5. Link Files to Metadata (Next Steps)
