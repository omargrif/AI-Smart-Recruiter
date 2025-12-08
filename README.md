# 🤖 n8n AI Smart Recruiter Agent

An autonomous Applicant Tracking System (ATS) workflow built with **n8n**, **Google Gemini**, and **Pinecone**. This agent automates the entire recruitment funnel: sourcing, screening (RAG), scoring, and scheduling.

<img width="1717" height="532" alt="image" src="https://github.com/user-attachments/assets/ff81b116-059e-42a4-98d4-73e96cda37eb" />


## 🚀 Features

- **Email Listening (Gmail):** Automatically detects emails with "Application" or specific keywords and extracts PDF attachments.
- **CV Parsing:** Converts PDF resumes into machine-readable text.
- **Vector Search (RAG):** Uses **Pinecone** to store candidate embeddings and retrieve relevant experience based on the Job Description (JD).
- **AI Scoring (Gemini):** Evaluates candidates on a 0-100 scale based on Hard/Soft skills match.
- **Automated Logic:**
  - **Pass (>75/100):** Sends an interview invitation with a generic calendar link.
  - **Fail (<75/100):** Sends a polite rejection email and archives the profile.
  - 

## 📦 How to Use
[AI Smart Recruiter.json](https://github.com/user-attachments/files/24028425/AI.Smart.Recruiter.json)


1. **Import the Workflow:**
   - Download the `` file from this repository.
   - In your n8n instance, go to **Workflows** > **Import from File**.

2. **Configure Credentials:**
   You will need to set up the following credentials in n8n:
   - **Gmail OAuth2** (for reading emails and sending replies).
   - **Pinecone API** (for vector storage).
   - **Google Gemini API** (for the AI Agent).

3. **Customize the Prompt:**
   - Open the **AI Agent Node**.
   - Edit the System Prompt to fit your specific Job Description or connect it to a Google Doc for dynamic job offers.

## 📄 License

This project is open-source. Feel free to use it for your own automation projects!
