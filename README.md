# Form Submission API

A lightweight self-hosted API that receives form submissions from any of your websites and delivers them to any email address via Gmail SMTP.

## Features
- Works with multiple websites (CORS whitelist)
- API key authentication
- Rate limiting (5 submissions / IP / 10 min)
- Clean HTML email output with reply-to set to the sender
- Extra form fields (phone, service, etc.) are included automatically

---

## Setup

### 1. Install dependencies
```bash
npm install
```

### 2. Configure environment variables
Copy `.env.example` to `.env` and fill in your values:
```bash
cp .env.example .env
```

| Variable | Description |
|---|---|
| `GMAIL_USER` | Your Gmail address |
| `GMAIL_APP_PASS` | Your 16-char Google App Password |
| `API_KEY` | A strong random secret key you create |
| `ALLOWED_ORIGINS` | Comma-separated list of your website origins |

**Generate an API key:**
```bash
node -e "console.log(require('crypto').randomBytes(32).toString('hex'))"
```

### 3. Run locally
```bash
npm run dev
```

---

## Deploy to Render (free)

1. Push this `form-api` folder to its own GitHub repo
2. Go to [render.com](https://render.com) → New → Web Service
3. Connect the GitHub repo
4. Set **Build Command**: `npm install`
5. Set **Start Command**: `npm start`
6. Add all environment variables from `.env` in the Render dashboard
7. Deploy — you'll get a URL like `https://your-app.onrender.com`

---

## Using it in your websites

Replace the fetch URL and add the required hidden fields:

```html
<form id="contactForm">
  <input type="hidden" name="api_key" value="YOUR_API_KEY">
  <input type="hidden" name="to_email" value="info@assurancebyjummy.com.ng">
  <input type="hidden" name="subject" value="New Inquiry from assurancebyjummy.com.ng">

  <input type="text" name="name" required>
  <input type="email" name="email" required>
  <textarea name="message" required></textarea>
  <button type="submit">Send</button>
</form>
```

The JS fetch call:
```javascript
const formData = Object.fromEntries(new FormData(form));

fetch("https://your-app.onrender.com/submit", {
  method: "POST",
  headers: { "Content-Type": "application/json" },
  body: JSON.stringify(formData)
})
.then(res => res.json())
.then(data => console.log(data));
```

Any extra fields (e.g. `phone`, `service`) are automatically included in the email.

---

## Endpoints

| Method | Path | Description |
|---|---|---|
| GET | `/` | Health check |
| POST | `/submit` | Submit a form |

### POST /submit — Required fields

| Field | Description |
|---|---|
| `api_key` | Your secret API key |
| `to_email` | Destination email address |
| `name` | Sender's name |
| `email` | Sender's email (used as reply-to) |
| `message` | Message body |
| `subject` *(optional)* | Custom email subject |
| *any extra field* | Automatically included in the email |
