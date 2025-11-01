
# **HR Expert — Automated Candidate Evaluation System**

## **Overview**

**HR Expert** is an AI-driven workflow built using **n8n** that automates the entire candidate screening process — from CV submission to evaluation and feedback. It extracts information from uploaded resumes, compares candidate data with job requirements, generates AI-based evaluations, and sends automatic email responses based on selection criteria.

This project integrates **OpenAI**, **Google Drive**, **Google Sheets**, and **Gmail** to build an end-to-end, no-code recruitment assistant that streamlines manual HR efforts.

---

## **Main Sources**

* **Candidate Resume Uploads** — via n8n Form Trigger (PDF format).
* **Google Sheets Job Profile Store** — logs all evaluations for record keeping.
* **Manual Job Profile Input** — HR defines desired qualifications and skills directly in n8n.

---

## **Models**

* **Primary Model:** OpenAI GPT-4 Turbo — used for text extraction, summarization, and evaluation.
* **Optional Fallback:** Gemini 1.5 Flash (via Lovable AI) — for fast, lightweight processing tasks.

---

## **Objectives**

* Automate repetitive HR screening workflows.
* Extract structured information (skills, education, contact details) from CVs.
* Evaluate candidates against a job profile using AI reasoning.
* Log evaluation data into Google Sheets automatically.
* Send customized email notifications to candidates based on performance.

---

## **Workflow Architecture**

### **1. Trigger — Candidate Form Submission**

* **Node:** Form Trigger
* The workflow begins when a candidate fills out an online form and uploads their CV.
* The form collects:

  * Name
  * Email ID
  * Uploaded CV (PDF format)

---

### **2. File Upload and Text Extraction**

* **Nodes:**

  * *Google Drive — Upload File:* Uploads CV to Drive.
  * *PDF Extractor:* Extracts text from the uploaded CV.
* Converts the unstructured resume into machine-readable text for AI analysis.

---

### **3. Data Extraction — Personal and Professional Info**

* **Nodes:**

  * *Personal Data (OpenAI):* Extracts Name, Email, Telephone, City, Birthdate.
  * *Qualifications (OpenAI):* Extracts Education, Experience, and Skills.
* Both outputs are in structured JSON format.

---

### **4. Merge Node**

* **Node:** Merge
* Combines outputs from the Personal and Qualifications nodes into a single structured dataset for each candidate.
* This combined JSON serves as the unified input for AI summarization.

---

### **5. Candidate Summarization**

* **Node:** Summarization Chain (Model)
* Creates a concise summary of the candidate’s background — focusing on qualifications, strengths, and relevant experiences.

---

### **6. Job Profile Input**

* **Node:** Profile Wanted (Manual Input)
* HR defines:

  * Desired role and key skills
  * Minimum qualifications and experience required
* This input acts as the benchmark for AI evaluation.

---

### **7. AI Evaluation — Candidate Scoring**

* **Node:** HR Expert (Model)

* **Prompt:**
  The AI evaluates the candidate against the provided job profile using the following criteria:

  1. Relevance of technical skills and tools used.
  2. Alignment of experience and education with the role.
  3. Soft skills and cultural fit.
  4. Project quality and problem-solving abilities.

* **Output Format (Strict JSON):**

  ```json
  {
    "vote": <number between 1 and 10>,
    "consideration": "<brief explanation>"
  }
  ```

---

### **8. Structured Output Parsing**

* **Node:** Structured Output Parser
* Ensures AI-generated responses follow valid JSON syntax for further workflow automation.

---

### **9. Conditional Email Notifications**

* **Node:** IF Condition (Vote ≥ 8 or < 5)

  * If **vote ≥ 8** → Sends *“Selected for Interview”* email.
  * If **vote < 5** → Sends *“Not Selected”* email.

#### **Email Templates**

* **Selected Email:**
  “Congratulations — You’ve been shortlisted! Our HR team will reach out shortly.”
* **Rejected Email:**
  “After careful review, your profile does not match our current requirements. We encourage you to apply again in the future.”

---

### **10. Data Logging**

* **Node:** Google Sheets (Append Row)
* Each candidate’s details, summary, score, and AI consideration are recorded automatically for HR tracking.

---

## **Error Handling**

If you encounter this Gmail error:

> “The provided authorization grant or refresh token is invalid or expired…”

Follow these steps:

1. Go to **n8n Credentials → Gmail API**.
2. Delete the existing Gmail connection.
3. Reconnect using your **Google account** (make sure to allow all permissions).
4. If using multiple Gmail accounts, ensure you’re logged into the same one during authorization.

---

## **Key Features**

* End-to-end automation — from CV upload to decision email.
* AI-powered candidate analysis using GPT-4.
* Auto-structured data stored in Google Sheets.
* Zero manual intervention post-setup.
* Reusable for any role or company with minor modifications.

---

## **Future Enhancements**

* Integration with LinkedIn Job Listings API for real-time applicant imports.
* Dashboard visualization for candidate analytics.
* Multi-language resume support.
* Role-based evaluation weighting (skills vs. education).

---

## **Tech Stack**

* **Platform:** n8n
* **AI Models:** OpenAI GPT-4 Turbo, Gemini 1.5 Flash
* **Integrations:** Google Drive, Google Sheets, Gmail
* **File Format:** PDF CVs
* **Data Flow:** JSON structured automation

