# Write-Up-Quick-Recovery-Safaricom-CTF
INTRODUCTION

For the Quick Recovery challenge in the Safaricom CTF, we tackled the puzzle by first clarifying its scope—moving past the initial "Sneaky Recovery" mix-up to target the core recovery mechanics. We mapped out a systematic enumeration and extraction workflow, utilizing directory fuzzing to hunt down hidden backup artifacts or exposed files, downloading and unpacking any discovered archives, and analyzing the underlying source logic or recovery endpoints. Finally, by leveraging these uncovered paths and hitting the target endpoints with precise HTTP requests using tools like curl, we successfully bypassed the access restrictions and extracted the flag.

METHODOLOGY
1.Reconnaissance & Enumeration
2.Extraction & Source Analysis
3.Exploitation & Manipulation
4.Flag Retrieval & Decoding

TOOLS
