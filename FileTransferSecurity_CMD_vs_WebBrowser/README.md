# HTTP File Transfer: Modern Security Implications

## Introduction

While working on the GoldenEye room on TryHackMe, I needed to transfer a file from my Kali VM to my host machine. Out of habit, I set up a simple Python HTTP server to serve the file. However, I quickly ran into an issue—Google Chrome outright blocked the download due to security policies.

The command I used on my Kali VM was:

`python3 -m http.server <port>`

This starts a simple HTTP server to deliver files. However, when I attempted to download a legitimate text file from my Kali VM, Google Chrome flagged it as "Not secure" and prevented the download.

![googleChromeHostDoesNotAllowHTTPDownload](Images/googleChromeHostDoesNotAllowHTTPDownload.png)

Instead of downloading the file, Chrome displayed its contents, allowing me to copy and paste it manually but not save it directly. Additionally, my Kali VM displayed a `404` error, indicating an unsuccessful download attempt.

![kaliVMshowDownloadNotSuccess](Images/kaliVMshowDownloadNotSuccess.png)

This made me wonder: Do all modern systems enforce this restriction, or is it specific to web browsers? I decided to test different transfer methods and compare the results.

### Experiment: Testing Different Transfer Methods

#### Setup

Kali VM running `python3 -m http.server <port>`

Verified connectivity between the Kali VM and the host machine (set as NAT on VMware Workstation Pro 17)

## Testing Different Methods

1. Using `wget` in PowerShell

Running `wget` in PowerShell successfully downloaded the file, bypassing the browser restriction.

![usePS-ToBypassHTTPSDownloadRestriction](Images/usePS-ToBypassHTTPSDownloadRestriction.png)

The HTTP response code was `200`, confirming a successful download.

![kaliVMshowDownloadSuccessAfterUsingWGET-onPS](Images/kaliVMshowDownloadSuccessAfterUsingWGET-onPS.png)

1. Using `wget` in Command Prompt (cmd)

Similarly, running wget from the Command Prompt successfully downloaded the file.

![useCMD-ToBypassHTTPSDownloadRestriction](Images/useCMD-ToBypassHTTPSDownloadRestriction.png)

![useCMD-ToBypassHTTPSDownloadRestriction2](Images/useCMD-ToBypassHTTPSDownloadRestriction2.png)

![kaliVMshowDownloadSuccessAfterUsingWGET-onPS-AndCMD](Images/kaliVMshowDownloadSuccessAfterUsingWGET-onPS-AndCMD.png)

1. Using `wget` in Ubuntu WSL Terminal

Downloading the file using `wget` in the Ubuntu WSL terminal also worked without issue.

![useUbuntuWSL](Images/useUbuntuWSL-Terminal-ToBypassHTTPSDownloadRestriction.png)

![kaliVMshowDownloadSuccessAfterUsingWGET-onUbuntuWSL-Terminal](Images/kaliVMshowDownloadSuccessAfterUsingWGET-onUbuntuWSL-Terminal.png)

## Summary of Results

| Transfer Method            | Download Success |
| :------------------------: | :--------------: |
| Google Chrome              | Blocked |
| Command Prompt (wget)      | Allowed |
| PowerShell (wget)          | Allowed |
| Ubuntu WSL Terminal (wget) | Allowed |

Despite modern browsers enforcing strict security policies, CLI tools had no issues retrieving the file. This suggests that while browser-based security is improving, alternative file transfer methods remain viable.

## Security Implications

### For Pentesters & Attackers

+ Embedding malicious commands inside seemingly harmless scripts (e.g., a PowerShell script from a "trusted" source).

+ Providing "troubleshooting steps" in a fake help forum post, tricking users into running commands.

+ Social engineering techniques, like posing as IT support and instructing a victim to run a command.

+ Using malicious `.lnk` (shortcut) or `.bat` files that execute hidden commands when opened.

+ Leveraging CLI tools allows bypassing browser restrictions.

+ Setting up an HTTPS server can avoid security warnings from browsers.

### Defensive Takeaways

+ Enforce HTTPS for internal tools and file transfers to prevent insecure downloads. This can be done using a self-sign CA that signed and trusted within the organization.
  + **Pros:** Secure encryption, protection against MITM attacks.
  + **Cons:** Extra maintenance (you must distribute and trust the internal CA manually).

+ Monitor network traffic for suspicious HTTP downloads, especially from command-line utilities.
  + So why monitor HTTP downloads if you enforce HTTPS?
    + Some legacy systems or misconfigured services may still use HTTP.
    + Attackers might intentionally use HTTP to bypass HTTPS-only security controls.

+ Implement endpoint security that scans downloaded files, beyond browser-based protections.
  + IDS (Intrusion Detection System) → Detects threats but doesn’t actively block them.
  + IPS (Intrusion Prevention System) → Detects and blocks threats in real-time.
  + Firewalls → Can restrict network traffic but typically don’t analyze file contents deeply.
  + EDR (Endpoint Detection and Response) → Provides real-time threat detection on endpoints, scanning files even if downloaded via CLI.

## Conclusion

This simple test highlights how security policies evolve over time. While browsers are improving their defenses against unsafe downloads, alternative methods remain effective. Organizations should not rely solely on browser security but should also monitor network and endpoint activity to mitigate risks.

What are your thoughts?

Have you encountered similar restrictions or workarounds? Let’s discuss!
