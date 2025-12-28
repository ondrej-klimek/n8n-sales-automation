# 🤖 AI-Powered Sales Data Auditor & Correction Pipeline

An end-to-end automation system that validates sales data using AI, detects anomalies, and manages a "Human-in-the-Loop" correction process via dynamic forms. Built with **n8n**, **Google Gemini**, and **Google Sheets**.

![Project Header Image](https://placeholder-for-your-main-screenshot.png)
*(Add a screenshot of your 3 workflows here)*

## 🚀 The Problem
Sales teams often manually enter data, leading to critical errors like missing amounts ($0 sales) or missing fields. Manual auditing is slow, and email back-and-forth to fix errors creates data silos and version conflicts.

## 💡 The Solution
I built a fully automated **Auditor-Corrector System** that:
1.  **Watches** for new sales entries in real-time.
2.  **Audits** data validity using an AI Agent (Google Gemini).
3.  **Routes** invalid entries to a "Correction Loop."
4.  **Generates** a personalized, pre-filled form for the sales rep to fix the specific error.
5.  **Updates** the database instantly upon submission, preventing duplication.

## 🛠️ Tech Stack
* **Orchestration:** [n8n](https://n8n.io/)
* **AI Logic:** Google Gemini (PaLM) via LangChain
* **Database:** Google Sheets
* **Communication:** Gmail (HTML & Text emails)
* **Interface:** n8n Forms

## ⚙️ Architecture: The 3-Workflow Pattern
To ensure scalability and prevent trigger conflicts, the system is decoupled into three distinct workflows:

### 1. Data Entry (Input)
* **Trigger:** Public Web Form.
* **Logic:** Generates a sequential `Transaction_ID` (e.g., TX-1005) via JavaScript logic and appends the raw data to Google Sheets.

### 2. The Auditor (Logic Engine)
* **Trigger:** Google Sheets (Row Added).
* **Process:**
    * Filters for rows marked "Pending."
    * **AI Agent:** Analyzes the row. If the Amount is 0 or fields are missing, it flags the row as `INVALID`.
    * **Router:** If Valid, marks as `Checked`. If Invalid, sends an automated email to the rep.
    * **Smart Link Generation:** The email contains a dynamic link that pre-fills the correction form with the specific transaction's ID and data.

### 3. The Corrector (Receiver)
* **Trigger:** Correction Web Form (Pre-filled via URL parameters).
* **Logic:** Receives the corrected data and the hidden `Transaction_ID`.
* **Action:** Locates the exact row in Google Sheets and performs an atomic update, changing the status to `Corrected`.

## 📸 Key Features & Challenges Solved

### 1. Dynamic Pre-Filled Forms
Instead of asking users to type everything again, the system generates a URL like:
`.../form?id=TX-1002&amount=0&rep=Mike`
The form renders with these values pre-filled, reducing friction and user error.

### 2. AI Decision Routing
The AI doesn't just "chat"; it outputs structured JSON data (`{"status": "INVALID", "reason": "..."}`), which determines the workflow path programmatically.

### 3. Infinite Loop Prevention
Implemented a "Circuit Breaker" pattern (Step 0 checks) to ensure the AI doesn't repeatedly audit the same processed rows.

## 🚀 How to Run This

1.  **Prerequisites:**
    * Self-hosted n8n or n8n Cloud account.
    * Google Cloud Console Project (with Sheets & Gmail APIs enabled).
    * Google Gemini API Key.
2.  **Import:**
    * Download the `.json` files from this repo.
    * Import them into n8n.
3.  **Setup:**
    * Create a Google Sheet with columns: `Transaction_ID`, `Date`, `Sales_Rep_Name`, `Rep_Email`, `Client_Name`, `Sale_Amount`, `Notes`, `Analysis_Status`, `Correction_Status`.
    * Connect your Google & Gmail credentials.
    * Update the "Auditor" email node with the URL from your "Corrector" form.

## 🎥 Demo

[Link to Loom Video or GIF showing the process]

---
## License ##

**© 2025 Ondrej Klimek. All Rights Reserved.**

This project is free to use for personal, educational, and portfolio review purposes.

**Commercial use, redistribution, or modification for business purposes is strictly prohibited without my written permission.**

If you would like to use this workflow in your business or hire me to build a similar solution, please contact me at: **klimekondrej01@gmail.com**
**IMPORTANT** - in the subject, include the following words and phrases: *permission*, *AI Sales Automation*
