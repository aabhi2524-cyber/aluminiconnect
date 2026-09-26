# Scam Message Detector (Node.js + Google Gemini)

A lightweight Express.js application with a calm, responsive single-page web UI that detects and analyzes SMS, WhatsApp, chat messages, and uploaded screenshots for Indian scam patterns (fake KYC, customs/courier fees, "digital arrest" police threats, electricity disconnection alerts, and fake job offers).

---

## ✨ Features

- **Text & Screenshot Scam Detection**: Paste SMS/WhatsApp messages or upload screenshots directly for instant multimodal AI analysis.
- **Multilingual Support**: Choose between **English**, **Hindi (हिन्दी)**, and **Kannada (ಕನ್ನಡ)**. Verdicts (risk label, reason, and recommended action) are automatically translated into the chosen language regardless of the input language.
- **Dark Mode Toggle**: Built-in sun/moon toggle with smooth `0.3s` CSS transitions and persistent choice via `localStorage`.
- **User Identification & Scan History**: Simple name-based history tracking persisted to AWS DynamoDB with a "My History" viewer.
- **Privacy-Preserving**: Uploaded screenshots are analyzed directly in-memory as base64 data without third-party retention.
- **Port Conflict Auto-Recovery**: `start.bat` automatically frees port 3000 if occupied by a zombie process.

---

## 🔑 Getting an API Key

1. Go to **[Google AI Studio](https://aistudio.google.com/app/apikey)**.
2. Sign in with your standard Google account.
3. Click **"Create API key"** (or "Get API key").
4. Copy your generated key (starts with `AIzaSy...`).

---

## ⚙️ Configuration (`.env`)

Open the [`.env`](.env) file in the project folder and paste your key:

```env
PORT=3000

# GOOGLE GEMINI API
GEMINI_API_KEY=AIzaSyYourGeminiApiKeyHere
GEMINI_MODEL=gemini-3.6-flash

# AWS CREDENTIALS (Optional - for scan history persistence)
AWS_REGION=us-east-1
DYNAMODB_TABLE_NAME=scam_detector_history
```

---

## 🚀 How to Run

1. Open your terminal in the `hack1` folder:
   ```powershell
   .\start.bat
   ```
   *(Or run `npm start`)*

2. Open your web browser and navigate to:
   ```text
   http://localhost:3000
   ```

---

## 📡 API Reference

### 1. Analyze Text Message
- **Method**: `POST`
- **URL**: `http://localhost:3000/analyze`
- **Headers**: `Content-Type: application/json`
- **Body**:
  ```json
  {
    "message": "Dear customer, your SBI account KYC is expired. Update immediately at http://bit.ly/sbi-kyc",
    "userName": "Guest",
    "language": "English"
  }
  ```
- **Response (200 OK)**:
  ```json
  {
    "risk": "Dangerous",
    "reason": "This is a fake bank KYC phishing alert designed to compromise net banking credentials.",
    "action": "Do not click the link; banks never request KYC updates via SMS links."
  }
  ```

### 2. Analyze Screenshot
- **Method**: `POST`
- **URL**: `http://localhost:3000/analyze-image`
- **Content-Type**: `multipart/form-data`
- **Form Fields**:
  - `image`: Screenshot image file (`image/png`, `image/jpeg`, `image/webp`)
  - `userName` (optional): User name string
  - `language` (optional): `"English"`, `"Hindi"`, or `"Kannada"`
- **Response (200 OK)**: Same JSON format with `risk`, `reason`, and `action`.

### 3. Get User History
- **Method**: `GET`
- **URL**: `http://localhost:3000/history?userName=Guest`
- **Response (200 OK)**:
  ```json
  {
    "userName": "Guest",
    "items": [
      {
        "id": "scan_1726744000000",
        "userName": "Guest",
        "timestamp": 1726744000000,
        "message": "Your electricity bill...",
        "risk": "Dangerous",
        "reason": "...",
        "action": "...",
        "source": "text"
      }
    ]
  }
  ```

