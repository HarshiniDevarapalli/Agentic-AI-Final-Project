
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




## 🧩 Step-by-Step Implementation of the Project. 

Follow these steps to set up and deploy the **HR Expert — Automated Candidate Evaluation System**.

---

### **1. Preparation**

* **Create Accounts & Collect Credentials:**
  Set up accounts for **n8n**, **OpenAI**, **Google Cloud (Drive, Sheets, Gmail)**, and other integrations.
  Obtain API keys, OAuth client IDs, and service credentials, and store them securely.

* **Project Repository Setup:**
  Create a local folder or GitHub repository to store your workflow exports, prompts, and documentation.
  Maintain prompt templates like `personal_data_prompt.txt`, `evaluation_prompt.txt`, etc.

---

### **2. Environment Setup**

* **Install & Run n8n:**
  Choose between **local** (for testing) and **cloud hosting** (for production).
  For local setup, you can use Docker:

  ```bash
  docker run -it --rm \
    --name n8n -p 5678:5678 \
    -e N8N_BASIC_AUTH_ACTIVE=true \
    -e N8N_BASIC_AUTH_USER=<user> \
    -e N8N_BASIC_AUTH_PASSWORD=<pass> \
    n8nio/n8n
  ```

* **Expose Webhook (for testing):**
  Use **ngrok** to get a temporary public URL for your local n8n instance:

  ```bash
  ngrok http 5678
  ```

---

### **3. Google & Email Configuration**

* **Enable Google APIs:**
  In Google Cloud Console, enable **Drive**, **Sheets**, and **Gmail** APIs.
  Create an **OAuth Client ID** and set the redirect URI as:

  ```
  http://localhost:5678/rest/oauth2-credential/callback
  ```

  or your deployed domain equivalent.

* **Gmail Integration:**
  Connect Gmail via OAuth or SMTP in n8n.
  Test a sample “Send Email” node to ensure credentials are valid.

---

### **4. Google Sheets Setup**

* **Job Profiles Sheet:**
  Create a sheet with columns:

  ```
  job_id | title | required_skills | min_experience | description
  ```
* **Candidate Evaluations Sheet:**
  Create another sheet:

  ```
  date | name | email | city | skills | summary | vote | consideration | decision | resume_link
  ```

  Share both sheets with the same Google account linked in n8n.

---

### **5. Form & Resume Collection**

* **Candidate Submission Form:**
  Use an n8n form trigger or a simple HTML form to collect:

  * Name
  * Email
  * CV (PDF Upload)

* **Webhook Connection:**
  Connect your form submission to the **Webhook Trigger** in n8n to start the workflow automatically.

---

### **6. Resume Handling**

* **File Storage:**
  Save uploaded CVs to Google Drive and capture their file links.
* **PDF Extraction:**
  Use the *Extract from File* node to read text, metadata, and author info.
  This converts unstructured PDFs into machine-readable text.

---

### **7. AI Data Extraction**

* **Personal Data Model:**
  Extract structured details such as Name, Email, Phone Number, and City.
* **Qualifications Model:**
  Extract education, experience, projects, and key skills.
* **Output:** Two structured JSON datasets merged later.

---

### **8. Data Combination & Summarization**

* **Merge Step:**
  Combine personal and qualification data into a unified JSON structure.
* **Summarization Chain:**
  Generate a short AI-written summary of the candidate’s qualifications and achievements.

---

### **9. Job Profile Comparison**

* **Manual Input Node:**
  The “Profile Wanted” node contains:

  * Desired role and responsibilities
  * Required skills
  * Minimum qualifications
    This acts as the benchmark for evaluating candidates.

---

### **10. AI Evaluation**

* **Evaluation Model (HR Expert Prompt):**
  The model compares the **candidate summary** with the **job profile** using the following criteria:

  * Technical skills alignment
  * Experience & education relevance
  * Soft skills and overall suitability
    The output is a **JSON object** containing:

  ```json
  {
    "vote": 8,
    "consideration": "Strong React and web development background; suitable for next round."
  }
  ```

---

### **11. Decision & Email Automation**

* **Decision Rules:**

  * `vote >= 7` → Selected for Interview
  * `vote <=6` → Rejected

* **Automated Emails:**

  * **Selected:** Sends a congratulatory message.
  * **Rejected:** Sends a polite rejection email.
  * Emails are sent via the Gmail node with dynamic placeholders for name and details.

---

### **12. Data Logging**

* **Append to Google Sheets:**
  Log candidate name, summary, decision, and AI considerations automatically in a centralized sheet.

---

### **13. Multi-Role Evaluation (Optional Enhancement)**

* Allow the workflow to evaluate one candidate across **multiple job profiles**.
* For each role in your job sheet:

  * Compare extracted candidate skills with each profile.
  * Run evaluation prompt for every role and log best matches.

---

### **14. Testing & Error Handling**

* Test the workflow with multiple CVs (structured, unstructured, and scanned).
* Handle edge cases like:

  * Missing fields (e.g., no email).
  * Parsing errors or API timeouts.
* Add retry nodes for temporary failures.

---

### **15. Deployment & Maintenance**

* Deploy n8n to a stable host.
* Update redirect URIs and credentials for the production domain.
* Schedule periodic reviews of the AI evaluation output to improve prompt accuracy.
* Rotate API keys regularly for security.

---



## **PPT LINK**
* **https://docs.google.com/presentation/d/1dlaea0F99eIgqV1TKaumliKc-KKfneMwoK8_VpIjr0E/edit?usp=sharing**
