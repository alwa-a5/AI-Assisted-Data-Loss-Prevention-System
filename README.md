# AI-Assisted Data Loss Prevention System

- For this project, I built a local AI-assisted Data Loss Prevention (DLP) system on an Ubuntu virtual machine to inspect text prompts and supported files before sensitive information is submitted to an AI platform. The application uses Microsoft Presidio and custom pattern detection to identify personally identifiable information (PII) and exposed secrets, while a locally hosted Llama 3.2 model through Ollama classifies the business sensitivity of the content and explains the risk. A deterministic Python policy engine combines those signals with the selected AI destination to return an `ALLOW`, `WARN`, or `BLOCK` decision. I then created a Streamlit dashboard for live scanning, automatic redaction, activity monitoring, and repeatable evaluation. The main goal of this lab was to demonstrate how AI can assist a security control without allowing the model alone to make the final enforcement decision.

# System Topology

<p align="center">
  <img src="assets/ai-dlp-topology.svg" width="100%" alt="AI-Assisted Data Loss Prevention System topology" />
</p>

- The entire workflow runs locally on the Ubuntu VM. A user enters a prompt or uploads a supported `.txt`, `.csv`, or `.docx` file through the Streamlit interface and selects either an approved enterprise AI service or an unapproved public AI service as the destination. Presidio and a custom secret recognizer inspect the content for sensitive entities, and Ollama runs Llama 3.2 locally to classify the information and provide a confidence score, reason, and recommended action. The deterministic risk and policy engine makes the final `ALLOW`, `WARN`, or `BLOCK` decision, produces a sanitized version when sensitive values are found, and records the result in `results.json` for the Monitoring and Evaluation views.

# Setting Up the Ubuntu Environment

- To begin, I installed Python and the supporting package-management tools needed to build and run the local DLP application.

<img width="1718" height="820" alt="Installing Python on the Ubuntu virtual machine" src="https://github.com/user-attachments/assets/ec3ec20c-6e3a-4058-9c93-43467026484a" />

- I created a dedicated project directory and Python virtual environment so the lab dependencies would remain isolated from the rest of the Ubuntu system.

<img width="1718" height="820" alt="Creating the project directory and Python virtual environment" src="https://github.com/user-attachments/assets/e19b014b-2194-476a-8a28-7ee3836a81c1" />

- Inside the virtual environment, I installed Microsoft Presidio and the supporting natural-language processing packages required for sensitive-data detection.

<img width="1718" height="820" alt="Installing Microsoft Presidio and supporting Python packages" src="https://github.com/user-attachments/assets/d6cc2d1a-596c-442e-82c8-a27db67565c5" />

# Building and Testing Sensitive Data Detection

- I created a test file containing synthetic sensitive information so the scanner could be developed safely without using real personal or company data.

<img width="1718" height="820" alt="Creating a synthetic sensitive-data test file" src="https://github.com/user-attachments/assets/c69979d4-fafc-4652-8cbd-4a0e2535198f" />

- I ran the first detection test and confirmed that Presidio could locate sensitive entities in the sample content. This created the rule-based detection layer used later by the complete DLP pipeline.

<img width="1718" height="820" alt="Testing Presidio sensitive-data detection against the sample file" src="https://github.com/user-attachments/assets/36271220-738d-41ed-87bd-4a0e2535198f" />

# Installing the Local AI Model

- Next, I installed Ollama on Ubuntu so the language model could run locally instead of sending the test content to an external cloud model.

<img width="1718" height="820" alt="Installing Ollama on the Ubuntu virtual machine" src="https://github.com/user-attachments/assets/ca2a9c78-0a0e-4955-ba66-d4647f2173e0" />

- I downloaded the Llama 3.2 model that would classify the scanned content as `Public`, `Internal`, `Confidential`, or `Restricted`.

<img width="1718" height="820" alt="Downloading the Llama 3.2 model through Ollama" src="https://github.com/user-attachments/assets/9838fa3e-4e6a-4612-be75-e2ceec34c8d1" />

- After the model was available, I verified that Ollama could process a local request before connecting it to the Python scanner.

<img width="1718" height="820" alt="Verifying local Llama 3.2 inference through Ollama" src="https://github.com/user-attachments/assets/bdd70b71-2924-4477-8449-fff441d3484a" />

# Building the AI-Assisted DLP Pipeline

- I combined Presidio PII detection, custom API-key and secret detection, and local Llama classification into one scanning workflow. The AI returned structured classification details, while the Python policy engine remained responsible for the final enforcement decision.

<img width="1718" height="820" alt="Connecting sensitive-data detection with local AI classification" src="https://github.com/user-attachments/assets/980ba45d-5b79-4dde-944a-18093886f6fb" />

- I tested the pipeline with synthetic prompts representing different sensitivity levels and AI destinations. The scanner calculated a risk score and applied deterministic policies `DLP-001` through `DLP-006` to produce an `ALLOW`, `WARN`, or `BLOCK` result.

<img width="1718" height="820" alt="Testing DLP risk scoring and policy enforcement" src="https://github.com/user-attachments/assets/0ba7aa99-bf26-4f6a-b3e4-a78a648135d8" />

- I confirmed that the completed local workflow returned the detected data types, classification, risk, policy reason, decision, and a redacted version of the scanned content.

<img width="1718" height="820" alt="Reviewing the completed local DLP scan result" src="https://github.com/user-attachments/assets/fe407a8d-2ace-437f-a492-453fc0485936" />

# Creating the Streamlit Dashboard

- After validating the command-line workflow, I installed Streamlit to provide a clear interface for scanning, monitoring, and evaluating DLP activity.

<img width="1718" height="820" alt="Installing Streamlit for the DLP dashboard" src="https://github.com/user-attachments/assets/5c9ec3dd-c159-4fc3-8eee-b809ad57fb09" />

- I launched the **Data Loss Prevention Dashboard**, which organized the application into `Scanner`, `Monitoring`, and `Evaluation` tabs.

<img width="1718" height="820" alt="Launching the Data Loss Prevention Dashboard" src="https://github.com/user-attachments/assets/00fb637f-1bb9-4748-aef4-707a756514df" />

# Testing the Scanner

- In the Scanner tab, I entered test content, selected the intended AI destination, and ran a live scan. The interface displayed the data classification, AI confidence, risk score, enforcement decision, detected data types, matched policy, assessment, and sanitized text.

<img width="1718" height="820" alt="Testing a sensitive-content scan in the Streamlit dashboard" src="https://github.com/user-attachments/assets/98d4b0df-3c37-4d44-ac8e-77c1516debed" />

# Monitoring DLP Activity

- I used the Monitoring tab to review saved scan history from `results.json`. The dashboard summarized high-risk activity, enforcement actions, data classifications, sensitive data types, and individual events for investigation.

<img width="1718" height="820" alt="Reviewing DLP events and enforcement activity in the Monitoring tab" src="https://github.com/user-attachments/assets/3b3a8bb5-2efd-4ae4-8f36-dbaf5450e8ab" />

# Evaluating the Detection Logic

- Finally, I used the Evaluation tab to run repeatable test cases and compare expected policy outcomes with the scanner's actual decisions. This helped verify that the detection and enforcement logic responded consistently across different types of content and destinations.

<img width="1718" height="820" alt="Evaluating the DLP scanner with repeatable test cases" src="https://github.com/user-attachments/assets/6113a2e5-0b98-4f97-8935-bab49ea2574c" />

# Project Outcomes

- Built a local AI-assisted DLP pipeline using Python, Microsoft Presidio, Ollama, and Llama 3.2.
- Detected PII and exposed secrets in text prompts and supported `.txt`, `.csv`, and `.docx` files.
- Applied deterministic DLP policies to return `ALLOW`, `WARN`, or `BLOCK` decisions and redact sensitive values.
- Created Scanner, Monitoring, and Evaluation views in Streamlit with persistent scan history in `results.json`.
- Evaluated the system with synthetic sensitive data without sending the content to an external AI model.

Built and Documented by Aluseni Waritay
