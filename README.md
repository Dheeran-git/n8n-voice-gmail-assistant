# 🎙️ 💬 Multi-Modal AI Inbox Agent (Gmail + Gemini 2.5)

A robust, multi-modal AI agent that manages Gmail through **Text OR Voice** commands on Telegram. Built entirely on **n8n**, this workflow leverages **Google Gemini 2.5 Flash** for high-speed reasoning and tool use without the high costs associated with OpenAI or Anthropic.

> **Project Status:** Minor/Side Project (Proof of Concept)

## 🌟 Project Overview
This project addresses **Email Fatigue**. Whether I am walking to class or sitting in a quiet library, I needed a way to manage my inbox without endless scrolling.

This Agent is **Multi-Modal**:
* **Voice Mode:** *"Find the email from the Dean sent yesterday."*
* **Text Mode:** *"Draft a reply to the last email saying I'll be late."*

The system handles transcription, intent recognition, tool execution (reading/drafting/replying), and response summarization entirely autonomously.

## 🏗️ Technical Architecture & Node Logic

The workflow is orchestrated in **n8n** and features a sophisticated logic pipeline to handle mixed inputs:

### 1. Multi-Modal Input & Standardization 🔀
* **Telegram Trigger:** Listens for any incoming message.
* **Intelligent Switching:** A `Switch` node detects if the message is **Voice** or **Text**.
    * *Voice Path:* Downloads the OGG file and passes it to **Gemini Transcription** (Native multimodal capability).
    * *Text Path:* Bypasses transcription.
* **The "Standardization Layer" (Crucial):** One of the biggest challenges was the "Undefined Variable" bug when merging these branches. I implemented custom **Edit Fields (Set)** nodes to normalize both inputs into a single variable (`chat_input`). This ensures the AI Brain treats text and transcribed audio exactly the same way without crashing.

### 2. The Brain (AI Agent 1) 🧠
* **Model:** **Google Gemini 2.5 Flash**.
    * *Design Choice:* I specifically chose Gemini over OpenAI (GPT-4) or Anthropic (Claude) because it offers a generous **Free Tier**, native tool calling, and extreme speed. OpenAI's API costs were not viable for a student project.
* **Memory:** Simple Window Buffer (retains the last 5 interactions) mapped to the user's Telegram Chat ID using `.first()` logic to prevent session errors.
* **Tools:** The agent has autonomous access to 8 Gmail tools:
    * `Read Emails`: configured with a **"Search"** parameter to allow natural language queries (e.g., `from:google subject:security`).
    * `Draft Email`: The default "safe" action.
    * `Reply to Email`: Supports HTML formatting for professional responses.
    * `Delete`, `Label`, `Get Label`, etc.

### 3. The Summarizer (AI Agent 2) 📝
* **Role:** The Main Brain returns raw, technical execution data (often messy JSON blobs).
* **Why it's NOT Redundant:** This second agent acts as a "Translator." It takes that raw technical output and rewrites it into a clean, friendly, WhatsApp-style text response for the user.

## 🚀 Key Features
* **Hybrid Control:** No typing required (Voice), but silent control available (Text).
* **Smart Search:** Uses Gmail's native query engine dynamically.
* **Cost-Efficient:** Built entirely on free-tier compatible nodes.
* **Safety First:** Defaults to "Drafting" emails rather than sending them, preventing AI hallucinations from accidentally emailing professors.

## 🚧 Limitations & Challenges
* **Audio Output:** I originally planned to use OpenAI's Text-to-Speech (TTS) for voice replies, but removed it to keep the project 100% free. The bot currently replies via Text.
* **Single User:** Currently hard-coded to the creator's Gmail credentials via n8n's OAuth.
* **Debugging:** Significant effort went into fixing JSON pathing errors (`.item` vs `.first()`) due to the branching logic.

## 🔮 Future Scope
As this is a side project, potential improvements include:
* **Calendar Integration:** Adding Google Calendar nodes to allow scheduling meetings via voice.
* **Vector Database (RAG):** To search through *years* of past emails, not just the recent 50.
* **Multi-User OAuth:** Refactoring for public use.

## 🛠️ How to Use
1.  Import the `voice-gmail-assistant.json` file into n8n.
2.  Set up credentials for **Telegram** and **Google Gemini/PaLM**.
3.  Authenticate your **Gmail** account.
4.  Activate the workflow and start talking or texting!

---
*Built with ❤️ by a ambitious engineering student.*
