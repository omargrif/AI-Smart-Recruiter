# 🤖 n8n AI Smart Recruiter Agent

An autonomous Applicant Tracking System (ATS) workflow built with **n8n**, **Google Gemini**, and **Pinecone**. This agent automates the entire recruitment funnel: sourcing, screening (RAG), scoring, and scheduling.

<img width="1526" height="440" alt="image" src="https://github.com/user-attachments/assets/f4c342b2-2a72-4f09-b08f-4b0ee6c748c9" />


## 🚀 Features

- **Email Listening (Gmail):** Automatically detects emails with "Application" or specific keywords and extracts PDF attachments.
- **CV Parsing:** Converts PDF resumes into machine-readable text.
- **Vector Search (RAG):** Uses **Pinecone** to store candidate embeddings and retrieve relevant experience based on the Job Description (JD).
- **AI Scoring (Gemini):** Evaluates candidates on a 0-100 scale based on Hard/Soft skills match.
- **Automated Logic:**
  - **Pass (>75/100):** Sends an interview invitation with a generic calendar link.
  - **Fail (<75/100):** Sends a polite rejection email and archives the profile.

## 📦 How to Use

### 1. Import the Workflow
- Download the **[AI Smart Recruiter.json](https://github.com/user-attachments/files/24028425/AI.Smart.Recruiter.json)** file.
- In your n8n instance, go to **Workflows** > **Import from File** and select the file you just downloaded.

### 2. Configure Credentials
You will need to set up the following credentials in n8n (the nodes will appear red until configured):
- **Gmail OAuth2** (for reading emails and sending replies).
- **Pinecone API** (for vector storage).
- **Google Gemini API** (for the AI Agent).

### 3. Customize the Prompt
- Open the **AI Agent Node**.
- Edit the System Prompt to fit your specific Job Description (JD) or connect it to a Google Doc node for dynamic job offers.

## 📄 License

This project is open-source. Feel free to use it for your own automation projects!
