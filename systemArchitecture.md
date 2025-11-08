# System Architecture – Context Capture

---

## Overview

**Context Capture** is a web-based companion app that provides *real-time, intelligent meeting transcripts* for online calls.  
It connects to existing video conferencing platforms (like Zoom, Teams, etc), processes live audio through AI transcription, and displays a readable transcript beside the call — complete with speaker labels and on-demand explanations.

The architecture is designed for **speed, scalability, and accessibility**, ensuring that even during heavy live conversations, transcripts remain accurate and immediate.

---

## Core Architecture Layers

| Layer | Purpose | Key Technologies |
|--------|----------|------------------|
| **Frontend** | Displays live transcript, “Explain This” interface, and user actions | React.js, Tailwind CSS, WebSocket |
| **Backend** | Handles audio input, AI transcription, and explanation requests | Node.js, Express.js |
| **Database** | Stores meeting metadata, transcript logs, and user preferences | MongoDB |
| **AI Layer** | Processes speech and provides natural language explanations | Whisper API (Speech-to-Text), OpenAI GPT API |
| **Infrastructure** | Ensures scalability and real-time delivery | WebSocket Server, Cloud Hosting (e.g., AWS or Render) |

---

## System Workflow

Below is the end-to-end data flow that powers Context Capture:

1. **User joins a meeting** on Zoom/Teams/Meet.
2. **User opens Context Capture web app** and pastes the meeting link.
3. The app connects securely to the **meeting audio stream**.
4. The backend sends audio chunks to the **Speech-to-Text Engine** (Whisper or AssemblyAI).
5. The engine returns **live text output** with timestamps and speaker labels.
6. The **backend processes and formats** the text.
7. Transcript data is pushed to the **frontend via WebSocket**, updating in real-time.
8. When a user highlights text and clicks **“Explain This”**, the frontend sends the snippet to the backend.
9. Backend sends it to the **AI explanation engine (OpenAI GPT)**.
10. AI returns a short, simple explanation → displayed in a **side chat panel**.
11. After meeting ends, transcript + explanations are **stored in MongoDB** and optionally exported as `.txt` or `.pdf`.

---

## Component Communication Diagram

```plaintext
 ┌────────────────────────────────────────────┐
 │                 FRONTEND                   │
 │ ┌──────────────────────────────────────┐   │
 │ │  React + Tailwind Interface          │   │
 │ │  - Transcript Window                 │   │
 │ │  - Explain This Panel                │   │
 │ └──────────────────────────────────────┘   │
 │           ↑   ↓  (WebSocket + REST)        │
 └───────────┼────────────────────────────────┘
             │
             │
 ┌───────────┼────────────────────────────────┐
 │                 BACKEND                    │
 │  Node.js + Express + WebSocket Server      │
 │  - API Gateway                             │
 │  - Audio Stream Handler                    │
 │  - AI Explanation Handler                  │
 │  - Data Formatter                          │
 └───────────┼────────────────────────────────┘
             │
             │
 ┌───────────┼─────────────┐   ┌───────────────────────────────┐
 │         DATABASE         │   │           AI LAYER            │
 │        MongoDB           │   │ Whisper API / GPT API         │
 │ - Store transcripts      │   │ - Speech-to-Text              │
 │ - Save explanations      │   │ - Natural language output     │
 └──────────────────────────┘   └───────────────────────────────┘
```
 ## Key Design Principles
1. Accessibility by Default

    - Designed for hearing-impaired users; clear contrast, readable fonts, and live updates.

    - Works on browsers with no installation required.

    - Responsive design for desktop and tablet use.

2. Real-Time Performance

    - WebSocket ensures low latency data transmission between backend and frontend.

    - Audio processed in micro-chunks (<2s delay per segment).

3. Scalability

    - Each meeting runs as an independent WebSocket session.

    - Easily deployable on cloud hosting (AWS, Render, or Vercel).

    - Horizontal scaling supported through containerization (Docker).

4. Data Privacy

    - No permanent storage of audio files — only transcripts are stored.

    - End-to-end encrypted API connections for AI services.

    - Optional data deletion after meeting ends.

## Feasibility Justification
| **Technical Choice** | **Why It Works** |
|-----------------------|------------------|
| **React + WebSocket** | Enables instant UI updates and smooth real-time transcript scrolling. |
| **Whisper API** | Proven speech recognition with high accuracy for multiple accents and languages. |
| **OpenAI GPT API** | Powers the “Explain This” feature with contextual understanding. |
| **MongoDB** | Flexible schema allows transcripts and explanations to be stored together. |
| **Node.js** | Handles asynchronous audio and text events efficiently. |


## Data Model (Simplified)
```json
{
  "meetingId": "1234-5678",
  "participants": [
    {
      "name": "Jane",
      "role": "Speaker"
    },
    {
      "name": "Alex",
      "role": "Listener"
    }
  ],
  "transcript": [
    {
      "timestamp": "00:02:14",
      "speaker": "Jane",
      "text": "Let's review the agenda."
    }
  ],
  "explanations": [
    {
      "highlightedText": "agenda",
      "aiResponse": "A list of items to discuss in a meeting."
    }
  ]
}
```

 ## Future Technical Improvements

1. AI Speaker Recognition: Detect and label speakers automatically using voiceprints.

2. Multi-language Support: Live translation and localized explanations.

3. Offline Mode: Local caching for poor connectivity environments.

4. Analytics Dashboard: For insights into meeting participation and comprehension trends.

## Architecture Diagram 

[User Interface]
   ↓
[Frontend (React)]
   ↔  [Backend (Node + Express + WebSocket)]
        ↙          ↓             ↘
[Database]     [Whisper API]     [GPT API]
(MongoDB)     (Speech-to-Text)   (Explanation AI)

“From voice to understanding — a seamless pipeline of accessibility.”