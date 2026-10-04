# Write-Up-Quick-Recovery-Safaricom-CTF
INTRODUCTION

For the Quick Recovery challenge in the Safaricom CTF, we tackled the puzzle by first clarifying its scope—moving past the initial "Sneaky Recovery" mix-up to target the core recovery mechanics. We mapped out a systematic enumeration and extraction workflow, utilizing directory fuzzing to hunt down hidden backup artifacts or exposed files, downloading and unpacking any discovered archives, and analyzing the underlying source logic or recovery endpoints. Finally, by leveraging these uncovered paths and hitting the target endpoints with precise HTTP requests using tools like curl, we successfully bypassed the access restrictions and extracted the flag.


**METHODOLOGY


1.Reconnaissance & Enumeration
2.Extraction & Source Analysis
3.Exploitation & Manipulation
4.Flag Retrieval & Decoding

**TOOLS USED**

Gobuster / DirBuster / Dirsearch: For fuzzing directories and discovering hidden or unindexed files and backup archives (like .zip, .bak, or .old).

Wget / Curl: For downloading discovered artifacts and making targeted HTTP requests or header manipulations.

Unzip / Tar: For extracting contents from recovered backup archives to analyze application source code or configurations.

Browser Developer Tools: For inspecting page source code, cookies, local storage, and request/response headers.


**RECONNAISSANCE**

During the reconnaissance phase for Quick Recovery, we mapped out the application's file-retrieval and recovery endpoints to understand how files were being requested and served.

Specifically, the reconnaissance revealed:

The Target Endpoint: A file-retrieval API parameter (such as file=) designed to fetch standard documents or templates.

The Vulnerability Clue: The page structure and hints hinted that backup recovery files or sensitive folders were positioned just one directory level "above" the public web root.

The Sandbox Flaw: Testing standard filenames first showed the app readily accepting input, but the lack of path sanitization meant it would also accept traversal sequences (../), allowing us to break out of the intended directory and target backend files.

**ENUMERATION**

**1. Initial Reconnaissance & Connectivity Check**
   
    Verify the target is up and inspect response headers
   
   curl -I http://<target-ip>:<port>/

**2. Directory & File Fuzzing (Gobuster)**

Search for hidden backup archives, scripts, or unindexed recovery endpoints

gobuster dir -u http://<target-ip>:<port>/ -w /usr/share/wordlists/dirb/common.txt -x bak,zip,txt,old,sql,php

 **3. Path Traversal & File Inclusion Testing (Curl)**
    
Test if parameters are vulnerable to directory traversal to read system files or backup logs

curl -s "http://<target-ip>:<port>/index.php?file=../../../../etc/passwd"


**4. Downloading Discovered Recovery Artifacts****
   
  Pull down any identified backup archives or sensitive files found during enumeration
  
  curl -O http://<target-ip>:<port>/backups/recovery.zip
  
  Alternatively using wget:

wget http://<target-ip>:<port>/backups/recovery.zip
**

**5. Extraction & Analysis**

Unpack the recovered archive to inspect application source code or hidden keys
unzip recovery.zip


**Vulnerability Discovery
**
The vulnerability was identified through a combination of directory enumeration and endpoint inspection:

* **Exposed Attack Surface:** Initial fuzzing via Gobuster revealed unindexed paths and forgotten file extensions left in the web root.
* 
* **Parameter Testing:** Further testing of the application's file-fetching functionality exposed a lack of strict input sanitization.
* 
* **Confirmation:** By injecting directory traversal sequences (`../`), the application failed to restrict file access to the intended directory, allowing us to traverse outside the web root and confirm the ability to read sensitive system files and backup artifacts.

**Proof of Compromise

**
<img width="1073" height="812" alt="Screenshot From 2026-10-02 21-29-28" src="https://github.com/user-attachments/assets/134503f5-4785-46da-b11f-abdac097218e" />

** Key Takeaways**

The Quick Recovery challenge highlights critical security lessons regarding web application design, backup hygiene, and access controls:

Secure Backup Handling: Never store sensitive backup archives (.zip, .bak, .old) within the public web root or in predictable, unindexed directories where automated directory fuzzers can easily discover them.

Strict Input Validation & Sanitization: Implement rigorous allow-listing or sanitization for any file-fetching parameters to prevent directory traversal (../) attacks from breaching application boundaries.

Principle of Least Privilege: Ensure that the web server user account runs with minimal permissions, preventing unauthorized access to sensitive system files or restricted backend directories even if a traversal flaw exists.

**
CONCLUSION**


The Quick Recovery challenge served as an excellent practical exercise in web enumeration, path traversal exploitation, and artifact extraction. By systematically fuzzing for hidden paths, identifying improper input validation, and successfully extracting and analyzing the underlying backup archive, we were able to navigate the target constraints and retrieve the flag.

This challenge reinforces the critical importance of secure file-handling practices and strict perimeter defenses in modern web applications.










