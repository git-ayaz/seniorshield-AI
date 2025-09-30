# 🚀 SeniorShield - Complete Deployment Guide

## **Project Structure**
```
seniorshield/
├── agents/                 # FastAPI Backend (Railway)
│   ├── app/
│   │   ├── main.py        # FastAPI application
│   │   ├── coordinator.py  # Agent coordinator
│   │   ├── agents/        # All agent implementations
│   │   └── database/      # SQLite + ChromaDB helpers
│   ├── requirements.txt
│   └── Railway.md
└── web/                   # React Frontend (Vercel)
    ├── src/
    │   ├── components/ElderChat.jsx
    │   └── main.jsx
    ├── api/route.js       # Vercel serverless proxy
    ├── package.json
    └── vercel.json
```

## **🎯 Quick Deploy Guide**

### **Step 1: Deploy Python Backend to Railway**

1. **Create Railway Account**: https://railway.app
2. **Create New Project** → Deploy from GitHub
3. **Set Environment Variables**:
   ```bash
   GEMINI_API_KEY="PASTE YOUR API KEY"
   PORT=8000
   DATA_DIR=/data
   ENABLE_RETRIEVAL=true
   ```
4. **Configure Build**:
   - Root Directory: `agents`
   - Start Command: `uvicorn app.main:app --host 0.0.0.0 --port $PORT`
5. **Add Persistent Volume**: Mount at `/data`
6. **Deploy** and note the public URL (e.g., `https://seniorshield-agents.railway.app`)

### **Step 2: Deploy React Frontend to Vercel**

1. **Create Vercel Account**: https://vercel.com
2. **Import Project** from GitHub
3. **Set Root Directory**: `web`
4. **Set Environment Variables**:
   ```bash
   FASTAPI_URL=https://seniorshield-agents.railway.app
   ```
5. **Deploy** and note the frontend URL

## **🧪 Testing Scenarios**

Test these 6 golden scenarios:

### **1. SSN Phone Scam (HIGH RISK)**
```
Input: "Someone called saying they're from the IRS and need my Social Security number to avoid arrest"
Expected: HIGH risk, fraud+family agents activated, actions include HANG_UP, ALERT_FAMILY
```

### **2. Gift Card Scam (HIGH RISK)**  
```
Input: "A caller said I owe money and must pay with gift cards immediately"
Expected: HIGH risk, fraud+family agents, actions include DO_NOT_PAY, BLOCK_NUMBER
```

### **3. Medical Bill Question (MEDIUM RISK)**
```
Input: "I received a $2000 hospital bill that seems too high"
Expected: MEDIUM risk, healthcare+fraud agents, actions include REVIEW_BILL, CONTACT_PROVIDER
```

### **4. Family Emergency Scam (HIGH RISK)**
```
Input: "Someone called saying my grandson is in jail and needs $5000 bail money right now"
Expected: HIGH risk, fraud+family agents, actions include VERIFY_FAMILY, DO_NOT_SEND_MONEY
```

### **5. Estate Planning Query (LOW RISK)**
```
Input: "I need help updating my will and power of attorney documents"
Expected: LOW risk, estate agent, actions include UPDATE_DOCUMENTS, CONSULT_ATTORNEY
```

### **6. Tech Support Scam (HIGH RISK)**
```
Input: "Microsoft called about a virus on my computer and wants remote access"
Expected: HIGH risk, fraud+family agents, actions include HANG_UP, DO_NOT_ALLOW_ACCESS
```

## **🔧 Local Development**

### **Backend (Python)**
```bash
cd seniorshield/agents
pip install -r requirements.txt
export GEMINI_API_KEY="PASTE YOUR API KEY"
export DATA_DIR="./data"
uvicorn app.main:app --reload --port 8000
```

### **Frontend (React)**
```bash
cd seniorshield/web
npm install
npm run dev
# Runs on http://localhost:3000
```

## **📊 Health Checks**

### **Backend Health Check**
```bash
curl https://your-railway-app.railway.app/health
```
Expected response:
```json
{
  "status": "healthy",
  "agents": {
    "fraud": true,
    "healthcare": true,
    "estate": true,
    "family": true
  },
  "database": {
    "sqlite": true,
    "chromadb": true
  }
}
```

### **Frontend API Check**
```bash
curl -X POST https://your-vercel-app.vercel.app/api/route \
  -H "Content-Type: application/json" \
  -d '{"user_id": "test_user", "text": "Someone called asking for my SSN"}'
```

## **🔒 Security Notes**

1. **API Key Security**: Keep GEMINI_API_KEY secure in environment variables
2. **Rate Limiting**: Basic IP rate limiting implemented in Vercel function
3. **Input Validation**: Text length limits and sanitization
4. **CORS**: Properly configured for cross-origin requests
5. **Error Handling**: No sensitive information exposed in error messages

## **📈 Monitoring**

### **Key Metrics to Track**
- Response times (target: <2s p50, <4s p95)
- Error rates (target: <1% 5xx errors)
- Risk detection accuracy (target: >90% on golden scenarios)
- User engagement (conversations per session)

### **Log Monitoring**
- Check Railway logs for agent initialization
- Monitor Vercel function logs for API errors
- Track database operations in SQLite
