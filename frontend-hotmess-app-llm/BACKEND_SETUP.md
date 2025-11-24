# Backend Connection Setup

This guide explains how to connect the frontend to your backend API.

## Quick Setup

1. **Create a `.env.local` file** in the `frontend-hotmess-app-llm` directory:
   ```bash
   cd frontend-hotmess-app-llm
   touch .env.local
   ```

2. **Add your backend URL** to `.env.local`:
   ```env
   NEXT_PUBLIC_BACKEND_URL=http://localhost:8000
   ```
   
   Replace `http://localhost:8000` with your actual backend URL:
   - **Local development**: `http://localhost:8000` (or your backend port)
   - **Production**: `https://your-production-backend-url.com`
   - **Staging**: `https://your-staging-backend-url.com`

3. **Restart your Next.js dev server** if it's already running:
   ```bash
   npm run dev
   ```

## Backend API Requirements

The frontend expects your backend to:

- **Endpoint**: Accept POST requests at the root URL (or `/api/chat` if you prefer)
- **Request Format**:
  ```json
  {
    "message": "User's message here"
  }
  ```
- **Response Format**:
  ```json
  {
    "reply": "Assistant's response here"
  }
  ```

## Example Backend Implementation

### Python (Flask/FastAPI)
```python
from flask import Flask, request, jsonify

app = Flask(__name__)

@app.route('/', methods=['POST'])
@app.route('/api/chat', methods=['POST'])
def chat():
    data = request.json
    user_message = data.get('message', '')
    
    # Your LLM/chat logic here
    reply = process_message(user_message)
    
    return jsonify({'reply': reply})
```

### Node.js (Express)
```javascript
const express = require('express');
const app = express();

app.use(express.json());

app.post('/', (req, res) => {
  const { message } = req.body;
  
  // Your LLM/chat logic here
  const reply = processMessage(message);
  
  res.json({ reply });
});
```

## Environment Variables

- **`NEXT_PUBLIC_BACKEND_URL`**: The base URL of your backend API
  - Must start with `NEXT_PUBLIC_` to be accessible in the browser
  - Should not have a trailing slash
  - Examples:
    - `http://localhost:8000`
    - `https://api.example.com`
    - `https://your-backend.vercel.app`

## Troubleshooting

### CORS Issues
If you encounter CORS errors, make sure your backend allows requests from your frontend origin:

**Flask:**
```python
from flask_cors import CORS
CORS(app, origins=["http://localhost:3000"])
```

**Express:**
```javascript
const cors = require('cors');
app.use(cors({ origin: 'http://localhost:3000' }));
```

### Connection Refused
- Verify your backend is running
- Check the port number matches your `.env.local` configuration
- Ensure there are no firewall issues blocking the connection

### Environment Variable Not Working
- Make sure the variable name starts with `NEXT_PUBLIC_`
- Restart your Next.js dev server after changing `.env.local`
- Check that `.env.local` is in the `frontend-hotmess-app-llm` directory

## Current Configuration

The frontend is configured to use the backend URL from `lib/config.ts`, which reads from the `NEXT_PUBLIC_BACKEND_URL` environment variable. If the variable is not set, it falls back to the default hardcoded URL.

