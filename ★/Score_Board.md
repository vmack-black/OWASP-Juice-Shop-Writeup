# OWASP Juice Shop Write-up: Discover the Score Board

## Challenge Overview

### Details

* **Challenge Title:** Score Board
* **Category:** Miscellaneous / Information Disclosure
* **Difficulty:** ⭐ (1/6 - Beginner)
* **Objective:** Find and access the hidden Score Board page within the application.

### Why This Matters

In modern web applications, developers often use client-side routing (like Angular, React, or Vue) to manage different pages. If sensitive or administrative paths are left hardcoded in public JavaScript files without proper server-side authorization, anyone can map out the entire structure of the application. This challenge demonstrates how easily "hidden" features can be uncovered simply by looking at what the server sends to your browser.

---

## Tools Used

* **Standard Web Browser:** Google Chrome, Mozilla Firefox, or Microsoft Edge.
* **Browser Developer Tools (DevTools):** The built-in suite of web development tools accessed by pressing `F12` or `Ctrl+Shift+I` (`Cmd+Option+I` on Mac).

---

## Methodology and Solution

To solve this challenge, we need to inspect the front-end source code. Since the application handles page routing in the browser, the path to the Score Board must be written somewhere in the client-side JavaScript.

### Step 1: Open Browser Developer Tools

1. Navigate to your local OWASP Juice Shop homepage.
2. Right-click anywhere on the page and select **Inspect**, or use the `F12` shortcut to open the **Developer Tools** panel.

### Step 2: Locate the Application Source Code

1. In the DevTools panel, click on the **Sources** tab (in Chrome/Edge) or the **Debugger** tab (in Firefox).
2. On the left-hand side, you will see a file tree of assets loaded by the browser. Look for the main JavaScript bundles. Modern frameworks compile code into minimized files, often named something like `main.js`, `main-es2015.js`, or bundle files located under a `dist` or `assets` folder.

### Step 3: Analyze the Routing Logic

1. Select the primary application file (such as `main.js`).
2. Because this code is minified (compressed into a tight block to save bandwidth), it will look messy. Click the **`{ }` (Pretty Print)** button at the bottom of the code viewer window to automatically format it into clean, readable lines.
3. Use the search shortcut `Ctrl+F` (`Cmd+F` on Mac) to open the search bar within that file.
4. Search for keywords like `path:`, `route`, or `score-board`.

> **What we found:** The routing array explicitly defines a path mapping for the scoreboard:
> `{ path: 'score-board', component: ScoreBoardComponent }`

### Step 4: Access the Hidden Page

1. Go back to your browser's address bar.
2. Append the path string we discovered (`score-board`) to the application's base URL.
* *Example:* `http://localhost:3000/#/score-board`


3. Press **Enter**. The hidden Score Board page will load, and you will receive a notification that the challenge has been successfully solved.

---

## Technical Explanation

Modern single-page applications (SPAs) ship the entire routing map to the user's browser so that the application can transition between pages instantly without reloading.

While the developers omitted the link to the Score Board from the visible navigation menus, the underlying **route definition** still existed within the client-side code package. This concept is known as **Security through Obscurity**—hiding an asset rather than properly securing it. Security through obscurity is a weak defense mechanism because anything sent to the client browser can be inspected, intercepted, and manipulated by anyone who knows how to open DevTools.

---

## Remediation and Best Practices

While an informational score board isn't inherently dangerous, exposing administrative functionalities or unreleased features in this manner poses a severe security risk. To secure applications against this behavior, apply the following practices:

* **Implement Server-Side Access Controls:** Never rely on the client-side user interface to restrict access. The backend server must independently validate whether the session user has the required roles and permissions before serving data or components for a requested route.
* **Enforce Server-Side Route Guards:** Use programmatic guards (such as Angular's `CanActivate`) to prevent unauthorized rendering on the client side, and ensure the backend API completely rejects data requests originating from unauthorized endpoints.
* **Production Code Stripping:** Configure your build pipeline to automatically strip out development-only routes, debugging components, or unreleased feature flags from production deployment bundles.
