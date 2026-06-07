# THM-Phishing-email-in-action
Walkthrough &amp; Analysis  This document provides a comprehensive analysis and detailed walkthrough of the TryHackMe Phishing challenge tasks based on the analyzed artifacts. Each section breaks down the objective, the specific phishing techniques observed, a technical analysis of the indicators of compromise (IoCs), and the core findings.

## Task 2: Cancel Your Order (Phishing Email in Action)

### 1. Overview & Objective
In this task, an analyst evaluates an email designed to look like an automated transaction receipt or invoice confirmation. The objective is to identify the entities involved and determine if the notification is legitimate or a social engineering attempt.

### 2. Technical Analysis & Investigation
* **The Hook:** The email claims that a payment of **$120.00 USD** has been sent to an entity called **"Amazing Stuff"** for an "Amazing Stuff Gift Card (EMAIL DELIVERY)" consisting of 4 units priced at $30.00 each.
* **Urgency & Psychology:** Receipt-based phishing relies on the "shock factor." When a user receives an invoice for an expensive item they never bought, their immediate instinct is to panic and find a way to cancel the order or dispute the transaction, lowering their operational security guard.
* **Key Indicators:**
  * **Generic Greeting:** The email addresses the recipient using a generic **"Hello Customer,"** instead of a personalized name. Legitimate payment processors or merchants typically use the full name registered to the account.
  * **Discrepancies in Identification:** The listed merchant is **Amazing Stuff**, and a suspicious contact email address (`helpcenter@...`) is attached to it. 
  * **Sender Disguise:** The actual underlying email routing path points to a highly suspect infrastructure (`ppmxtrolling...com`), exposing that the message did not originate from an authorized financial processor.

### 3. Key Findings & Answers
* **Question:** Who is listed as the Merchant in the email body?
* **Answer:** `Amazing Stuff`
<img width="838" height="693" alt="image" src="https://github.com/user-attachments/assets/7cd73101-a0c6-404e-991f-74af41f2fe1d" />

## Task 3: Track Your Package (Hyperlink Tracking)

### 1. Overview & Objective
This task transitions into the underlying source code (HTML) of a phishing email to discover how malicious actors track email delivery, open rates, and user interactions via embedded tracking mechanisms and hyperlinks.

### 2. Technical Analysis & Investigation
* **The Hook:** Phishing emails frequently include package tracking buttons (e.g., "Track your package") to entice corporate or retail users expecting deliveries.
* **HTML Source Code Breakdown:** Inspecting the raw message headers and HTML structure reveals a sophisticated tracking configuration:
  * **Hyperlink Masking:** The text displayed to the user reads **"Track your package..."**, but the raw HTML anchor tag (`<a href="...">`) directs the user to an completely unrelated external root domain.
  * **Root Domain Analysis:** The hyperlink destination targets `devret.xyz`. To safely document and communicate this threat without accidentally triggering a browser connection, security analysts utilize **defanging** techniques (e.g., changing protocols or placing brackets around domains).
  * **Tracking Pixels:** The source code contains an image tag (`<img src="http://devret.xyz/Creatives/Tracking.png" ...>`) configured as a **tracking pixel**. This tiny, often invisible image loads automatically from the attacker's server the moment the email client renders the HTML. This alerts the spammer that the email address is active and the message has been viewed.

### 3. Key Findings & Answers
* **Question:** What root domain does the hyperlink in the above example point to? Be sure to defang the URL.
* **Answer:** `devret[.]xyz` (or `http://devret[.]xyz`)

<img width="859" height="608" alt="image" src="https://github.com/user-attachments/assets/531bc988-fdbe-45e5-b9f3-e83191c0becd" />

## Task 4: Download Document Here (Phishing Techniques Used)

### 1. Overview & Objective
This section covers the core psychological and structural methodologies employed by threat actors during an email campaign. It focuses specifically on the final mechanism used to steal corporate or personal user assets.

### 2. Technical Analysis & Investigation
* **Phishing Framework Taxonomy:**
  * **Artificial Urgency:** Creating a narrow window for action (e.g., "Your account will be deleted in 24 hours") to force the victim into making split-second mistakes.
  * **Brand Impersonation:** Layering trusted, well-known corporate designs, logos, and layouts (like Microsoft, Adobe, or Netflix) to build a false sense of familiarity and safety.
  * **Link Redirection:** Using a multi-stage chain of compromised URLs, shortened links, or open redirects to hide the final malicious destination from basic secure email gateways (SEGs).
  * **Credential Harvesting:** The deployment of a sophisticated, look-alike **fake login portal** hosted on an attacker-controlled server. When the victim attempts to authenticate on this fraudulent interface, their username, password, and session tokens are captured and exfiltrated directly into the attacker's database.

### 3. Key Findings & Answers
* **Question:** The attacker deployed a fake portal to capture and exfiltrate user credentials. What is this type of attack called?
* **Answer:** `Credential harvesting`

<img width="780" height="393" alt="image" src="https://github.com/user-attachments/assets/73657994-6134-4d13-82c5-ec1532537310" />

## Task 5: Your Account is on Hold (Brand Impersonation)

### 1. Overview & Objective
This challenge presents a classic instance of high-fidelity brand impersonation targeting popular subscription infrastructure. The objective is to bypass visual deception and locate the actual sending origin hidden behind display configurations.

### 2. Technical Analysis & Investigation
* **The Hook:** An urgent notice alerting the user that their **"Netflix ID Suspended"** or **"Your account is on hold."** This prompts an immediate click to update payment details.
* **Deception vs. Reality:**
  * **Display Name Spoofing:** The threat actor manipulates the email user interface header to display a legitimate-looking name: **"Netflix billing"**.
  * **Header Inspection:** Behind the friendly display name lies the true sender routing address. Looking closely at the SMTP `From:` field reveals a complete mismatch with official corporate infrastructure. Instead of an official `@netflix.com` domain, the envelope sender is routed through a standard, consumer webmail account provider (`...@yahoo.com`).

### 3. Key Findings & Answers
* **Question:** What is the actual sender email address hidden behind the Netflix billing display name?
* **Answer:** `...@yahoo.com` (Note: Full address extracted from raw email header details or walkthrough hints)

<img width="756" height="490" alt="image" src="https://github.com/user-attachments/assets/8156d572-cbb0-4002-8c37-82bd57c4a99b" />

## Task 6: Your Recent Purchase (Advanced Delivery Mechanisms)

### 1. Overview & Objective
This section explores the technical delivery mechanisms of email distributions, examining mass concealment strategies and the analysis of hidden attachment tracking redirects.

### 2. Technical Analysis & Investigation
* **Mass Communication Visibility:**
  * **BCC (Blind Carbon Copy):** An email header field used by threat actors to copy an extensive list of target addresses simultaneously without revealing the recipient pool to each individual victim. It maximizes delivery footprints while maintaining a clean, targeted appearance for the individual recipient.
* **Malicious URL Redirection Analysis:**
  * Looking closely at the provided indicators, clicking a tracking link doesn't go straight to the payload. Instead, it leverages trusted, high-reputation domains as intermediaries to bypass security filters.
  * **Redirect Chain Example:** `https://t.tumblr.com/redirect?z=https://apps.los-games.mansiiea.com/?...` 
  * The threat actor utilizes a legitimate Tumblr redirect service path to proxy the user to a secondary malicious site (`apps.los-games.mansiiea.com`), which hosts a highly obfuscated base64 encoded token sequence string designed to track campaign telemetry.

### 3. Key Findings & Answers
* **Question 1:** What does the acronym BCC stand for?
* **Answer 1:** `Blind Carbon Copy`
* **Question 2:** What is the file extension of the attachment?
* **Answer 2:** `.xlsm` (or `.xls` based on macro-enabled Excel maldoc delivery observed in task telemetry screenshots)

<img width="772" height="672" alt="image" src="https://github.com/user-attachments/assets/0c1814a5-c39a-4b79-8ea9-a8b05b290dc6" />

## Task 7: Scheduled Shipment (Maldoc Execution & Persistence)

### 1. Overview & Objective
The final stage of the walkthrough analyzes a malicious document interaction, detailing how an email attachment moves from a user click to execution on an endpoint system.

### 2. Technical Analysis & Investigation
* **The Weaponized Document:** Phishing campaigns frequently deliver macro-enabled Microsoft Excel sheets disguised as delivery or shipping schedules.
* **Execution Flow:**
  * When the user opens the spreadsheet and bypasses the native protective view warning ("Enable Content"), a hidden Visual Basic for Applications (VBA) macro executes automatically in the background.
  * This macro acts as a stager, dropping or extracting a compiled executable directly onto the local endpoint file system.
* **Endpoint File Path Analysis:** The execution trace points directly to a hidden user folder: `C:\Users\admin\AppData\Roaming\regasms.exe`.
  * **Evasion & Masquerading:** The malware names its payload `regasms.exe`, attempting to impersonate the legitimate Microsoft .NET Assembly Registration Utility (`RegAsm.exe`). Placing it inside the local execution directory (`AppData\Roaming`) allows it to bypass basic directory restriction controls and maintain persistent user-level execution permissions.

### 3. Key Findings & Answers
* **Question:** What is the name of the executable that the Excel attachment attempts to run?

<img width="734" height="651" alt="image" src="https://github.com/user-attachments/assets/27d86bbb-5ee4-49d1-889a-349fa47ed99c" />




