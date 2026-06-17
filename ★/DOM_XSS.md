## OWASP Juice Shop Write-up: DOM XSS

### 📌 Challenge Overview

### 📊 Details

* **Challenge Title:** DOM XSS
* **Category:** Cross-Site Scripting (XSS)
* **Difficulty:** ⭐ (1/6 - Beginner)
* **Objective:** Perform a DOM-based Cross-Site Scripting (XSS) attack by injecting an executable script into the application.

---

### ❓ Why This Matters

**DOM-based Cross-Site Scripting (DOM XSS)** occurs entirely within the victim's browser. It happens when an application's client-side JavaScript reads data from an untrusted source (known as a **source**, such as the URL query parameters or an input field) and passes it unsafely to a execution environment (known as a **sink**, such as `innerHTML` or `document.write`).

If an attacker can manipulate the source data, they can force the browser to execute arbitrary JavaScript code. This can lead to session hijacking, credential theft, or unauthorized actions performed on behalf of the user—all without the malicious payload ever needing to touch the backend server.

---

### 🛠️ Tools Used

* **Standard Web Browser:** Google Chrome, Mozilla Firefox, or Microsoft Edge.
* **Browser Developer Tools (DevTools):** To inspect how the injected payload interacts with the Document Object Model (DOM).

---

### 🚀 Methodology and Solution

To solve this challenge, we need to locate a client-side input that reflects our data directly back into the page structure without proper sanitization.

#### Step 1: Identify the Vulnerable Input Field

1. Navigate to your local OWASP Juice Shop homepage (e.g., `http://localhost:3000`).
2. Look at the top navigation bar and locate the **Search** icon or input field.
3. Type a benign identifier string (like `test-search`) into the search bar and press **Enter**.

#### Step 2: Observe the Reflection

1. Look closely at the user interface. You will see text indicating your search results, displaying something like: *Result for test-search*.
2. Notice the browser's address bar. The URL has updated to include a query parameter containing your input: `http://localhost:3000/#/search?q=test-search`.

#### Step 3: Craft the Payload

Because modern frameworks often block basic `<script>` tags when dynamically rendering text, we need to use a standard HTML element bypass technique. An `<iframe>` (inline frame) element executing a JavaScript URI is highly reliable for triggering a DOM-based XSS alert in this specific environment.

* **Payload:** `<iframe src="javascript:alert(\`xss`)">`

#### Step 4: Execute the Exploit (The Solution)

1. Clear your previous search query from the search input field.
2. Paste the crafted payload directly into the search bar:

```html
<iframe src="javascript:alert(`xss`)">

```

3. Press **Enter**.
4. An alert box displaying **xss** will pop up on your screen. Click **OK**, and the success banner will appear at the top of the page confirming the challenge is solved!

---

### 🧠 Technical Explanation

The reason this attack succeeds lies in how the application processes the search parameter.

When you submit a search, a piece of client-side JavaScript reads the value of the `q` parameter from the URL. Instead of treating your payload purely as text, the code takes that raw input string and dynamically appends it directly into the page's HTML structure (using an unsafe sink like `innerHTML`).

Because the input wasn't strictly sanitized or HTML-encoded before being injected, the browser interprets your text string as actual HTML elements. The browser attempts to render the `<iframe>`, reads its `src` attribute, and immediately executes the JavaScript instruction inside it.

---

### 🛡️ Remediation and Best Practices

Fixing DOM XSS requires ensuring that data handled on the client side is never trusted implicitly. Apply the following defensive practices:

* 🔒 **Use Safe Sinks:** Avoid using dangerous methods like `innerHTML`, `document.write()`, or `eval()`. Instead, use safe text assignment properties such as `textContent` or `innerText`, which force the browser to treat all user inputs strictly as strings rather than executable code.
* 🛡️ **Context-Aware Output Encoding:** If you must dynamically insert HTML, ensure the input is sanitized first. Use a trusted, thoroughly tested client-side sanitization library like **DOMPurify** to strip away dangerous tags (like `<script>`, `<iframe>`, or `onerror` attributes) before rendering.
* 🧹 **Implement Content Security Policy (CSP):** Configure a robust server-side HTTP `Content-Security-Policy` header. Restricting script executions to trusted sources and disabling `unsafe-inline` scripts creates an incredibly strong secondary layer of defense that stops injected XSS payloads from running altogether.
