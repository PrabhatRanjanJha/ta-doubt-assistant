# TA Doubt Assistant

An intelligent doubt management system built for Teaching Assistants to handle student queries automatically using RAG (Retrieval Augmented Generation) and n8n workflow automation.

## What It Does

Students submit doubts through a form. The system:
- Searches a knowledge base semantically to find relevant answers
- Auto-answers known questions instantly
- Flags unknown questions to the TA for manual review
- Adds TA answers to the knowledge base automatically so future similar questions get auto-answered
- Handles follow-up questions with conversation memory
- Tracks struggling students who ask repeated follow-ups on the same topic
- Logs everything to Google Sheets for tracking

## Tech Stack

- **n8n** — workflow orchestration
- **Groq (LLaMA 3.3 70B)** — LLM for answering and formatting
- **HuggingFace (all-MiniLM-L6-v2)** — semantic embeddings
- **n8n Simple Vector Store** — vector storage and similarity search
- **Google Sheets** — knowledge base and student tracking database
- **Gmail SMTP** — email delivery

## Architecture

Three workflows:

1. **Indexing Workflow** — reads Q&A pairs from Google Sheets, embeds them using HuggingFace, stores in vector store
2. **Main Workflow** — handles form submissions, routes new doubts vs follow-ups
3. **Doubt Handler (Sub-workflow)** — processes doubts through RAG, handles flagging, logging, and email delivery

## How It Works
Student submits doubt via form
↓
Is it a follow-up?
Yes → Retrieve previous session ID → Load conversation memory
No → Generate new session ID
↓
Search knowledge base (semantic similarity)
↓
Answer found? → Auto-answer → Email student
↓
No answer? → Flag to TA → TA reviews and replies
↓
TA answer → Added to knowledge base → Re-index


## Features

- **Semantic search** — finds relevant answers even when question phrasing differs
- **Conversation memory** — follow-up questions maintain context from previous session
- **Auto-learning** — knowledge base grows as TA answers new questions
- **Urgency detection** — flags students who ask 5+ follow-ups on the same topic
- **Full logging** — every doubt tracked with response type, status, and follow-up count

## Setup

### Prerequisites
- n8n (self-hosted)
- Groq API key (free at console.groq.com)
- Google account with Sheets and Gmail access

### Steps

1. Clone this repository
2. Import all three workflow JSON files into n8n
3. Set up credentials in n8n:
   - Groq API key
   - Google Sheets OAuth
   - Gmail SMTP
4. Create two Google Sheets:
   - **Doubt Database** — columns: Question, Answer, Category, Date Added
   - **Student Tracking** — columns: Roll_Number, Session_ID, Doubt, Doubt_Category, Response_Type, Status, Created_At, Follow_Up_Count
5. Update the Google Sheets node in each workflow to point to your sheets
6. Run the Indexing workflow to populate the vector store
7. Activate the Main workflow
8. Share the form URL with students

## Knowledge Base

The system comes with 30 pre-loaded Q&A pairs covering:
- HTML — elements, forms, tables, lists, semantic tags
- CSS — flexbox, grid, box model, animations, media queries
- JavaScript — closures, promises, async/await, DOM, events

## Project Background

Built as a real-world project to automate TA workflows at Scaler School of Technology. The system handles doubt resolution for web development courses covering HTML, CSS, JavaScript, and React.
