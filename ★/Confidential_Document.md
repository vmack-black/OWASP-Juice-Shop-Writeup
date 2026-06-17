## OWASP Juice Shop Write-up: Confidential Document

### 📌 Challenge Overview

### 📊 Details

* **Challenge Title:** Confidential Document
* **Category:** Sensitive Data Exposure
* **Difficulty:** ⭐ (1/6 - Beginner)
* **Objective:** Locate and access a highly confidential corporate document hidden within the application's file infrastructure.

---

### ❓ Why This Matters

Web applications frequently serve static assets (such as terms of service, privacy policies, or images) from a dedicated folder on the server, often named `/assets` or `/ftp`. If this directory is not properly isolated or secured, an attacker can manipulate URLs to browse through the directory's contents—a flaw known as **Directory Browsing** or **Improper Directory Indexing**.

When developers accidentally leave internal company documentation, design blueprints, or acquisition summaries inside publicly accessible folders, it presents a major security risk. Attackers don't need complex exploits to access this data; they simply need to know (or guess) the path to the file.

---

### 🛠️ Tools Used

* **Standard Web Browser:** Google Chrome, Mozilla Firefox, or Microsoft Edge.

---

### 🚀 Methodology and Solution

To solve this challenge, we will discover where public legal documents are stored and leverage that directory structure to uncover hidden corporate data.

#### Step 1: Find a Publicly Exposed File Path

1. Open your browser and navigate to your local OWASP Juice Shop homepage (e.g., `http://localhost:3000`).
2. Open the application menu (the "hamburger" icon or sidebar) and navigate to the **About Us** page.
3. Scroll through the text until you find a hyperlink regarding the shop's terms. It will look like: *"Check out our boring terms of use if you are interested in such lame stuff"*.
4. Hover over or copy this link. You will notice it points directly to a file hosted in an FTP folder:
`http://localhost:3000/ftp/legal.md`

#### Step 2: Attempt Directory Escalation

1. Go to your browser's address bar.
2. Remove the filename (`legal.md`) from the URL to target the root directory wrapper instead.
3. Navigate directly to: `http://localhost:3000/ftp`

#### Step 3: Extract the Confidential File (The Solution)

1. Since directory browsing is inadvertently left active on this endpoint, the browser will render a complete file tree layout listing all assets contained within that folder.
2. Look through the list of exposed markdown files. You will spot an interesting document named **acquisitions.md**.
3. Click on **acquisitions.md** or navigate directly to:
`http://localhost:3000/ftp/acquisitions.md`
4. The file contents outlining confidential corporate takeovers will load onto your screen, and the challenge success banner will instantly slide down from the header!

---

### 🧠 Technical Explanation

The flaw here is a combination of **Sensitive Data Exposure** and a misconfigured web server routing mechanism.

The application utilizes an `/ftp` folder to stage public files, but the server rules are not locked down tightly. When a user requests the parent directory `/ftp/` without defining a specific file, an insecurely configured server generates an automated index layout displaying everything inside. Because the confidential file `acquisitions.md` was stored carelessly in this public directory, it became completely vulnerable to simple URL manipulation.

---

### 🛡️ Remediation and Best Practices

Protecting an application from static asset exposure requires enforcing rigid access filters:

* 🔒 **Disable Directory Indexing:** Ensure your web server configuration explicitly disables automated directory listing (e.g., using `Options -Indexes` in Apache or turning off `autoindex` rules in Nginx). If a user accesses a folder without a file, the server should return a `403 Forbidden` status code.
* 🛡️ **Segregate Sensitive Storage:** Never mix internal documentation, system logs, or developer templates in the same file directories handling consumer-facing static web assets. Internal documents should be secured behind custom server authentication loops.
* 🧹 **Enforce Explicit Access Checks:** If assets must be delivered dynamically, wrap the endpoint behind a control script that reads session cookies and maps access tokens, completely rejecting any unauthorized guest requests attempting to pull files via raw URLs.

---
