# GOM AI - Multi-Agent Debate System

Hệ thống AI phân tích gốm sứ sử dụng 3 mô hình AI (GPT-4, Grok, Gemini) tranh luận với nhau để đưa ra kết luận chính xác nhất.

## Giới thiệu

Service này nhận ảnh gốm sứ, chạy qua 4 phase:
- Phase 0: GPT-4 Vision phân tích visual features
- Phase 1: GPT, Grok, Gemini đưa ra dự đoán độc lập
- Phase 2: Các agents tranh luận, tấn công/phòng thủ, điều chỉnh confidence
- Phase 3: Llama 3.3-70b làm judge tổng hợp kết luận cuối

## Công nghệ

- FastAPI + Uvicorn
- OpenAI GPT-4 & GPT-4 Vision
- xAI Grok
- Google Gemini 2.0 Flash
- Meta Llama 3.3-70b (Judge)
- httpx, Pillow, python-dotenv, tenacity

## Cấu trúc code

```
app/
├── agents/
│   ├── base_agent.py
│   ├── specialists.py      # GPT, Grok, Gemini
│   ├── vision_agent.py
│   └── judge_agent.py
├── debate/
│   └── debate_engine.py
└── main.py
uploads/                    # Ảnh debug
requirements.txt
.env
```

## Yêu cầu

- Python 3.10+
- Internet connection (gọi API)

## Cài đặt

### Tạo virtual environment

```bash
# Windows
python -m venv venv
venv\Scripts\activate

# Linux/macOS
python3 -m venv venv
source venv/bin/activate
```

### Install dependencies

```bash
pip install -r requirements.txt
```

## Cấu hình

Tạo file `.env`:

```env
OPENAI_API_KEY=sk-proj-xxxxx
XAI_API_KEY=xai-xxxxx
GOOGLE_API_KEY=AIzaSyxxxxx
CORS_ALLOW_ORIGINS=http://localhost:3000,http://127.0.0.1:3000
FRONTEND_URL=http://localhost:3000
GOOGLE_CLIENT_ID=xxxxx.apps.googleusercontent.com
```

Lấy API keys:
- OpenAI: https://platform.openai.com/api-keys
- xAI: https://console.x.ai/
- Gemini: https://aistudio.google.com/app/apikey

## Chạy server

```bash
uvicorn app.main:app --reload --host 0.0.0.0 --port 8001
```

Server chạy tại http://localhost:8001

Docs tự động: http://localhost:8001/docs

## API Endpoints

### Health Check

```http
GET /
```

**Response:**
```json
{
  "status": "online",
  "system": "Multi-Agent AI Debate"
}
```

### Predict

```http
POST /predict
Content-Type: multipart/form-data
```

Request: multipart/form-data với file ảnh

Response:
```json
{
  "visual_features": {
    "is_pottery": true,
    "description": "Bình gốm màu xanh ngọc...",
    "key_features": ["men ngọc", "hoa văn rồng"]
  },
  "agent_predictions": [
    {
      "agent_name": "GPT",
      "prediction": {
        "ceramic_line": "Gốm Celadon",
        "country": "Việt Nam",
        "era": "Thế kỷ 15-16"
      },
      "confidence": 0.85
    }
  ],
  "final_report": {
    "final_prediction": "Gốm Celadon Việt Nam",
    "country": "Việt Nam",
    "era": "Thế kỷ 15-16",
    "confidence": 0.88,
    "reasoning": "Dựa trên phân tích..."
  }
}
```

### Chat

```http
POST /chat
Content-Type: application/json
```

**Request:**
```json
{
  "question": "Gốm Celadon có đặc điểm gì?"
}
```

**Response:**
```json
{
  "answer": "Gốm Celadon là loại gốm có men màu xanh ngọc...",
  "sources": ["Kiến thức chuyên gia AI", "Wikipedia: Gốm Celadon"]
}
```

### Social Login

```http
POST /api/login/social
Content-Type: application/json
```

**Request:**
```json
{
  "provider": "google",
  "credential": "eyJhbGciOiJSUzI1NiIs...",
  "clientId": "your-client-id.apps.googleusercontent.com"
}
```

**Response:**
```json
{
  "success": true,
  "provider": "google",
  "user": {
    "id": "123456789",
    "email": "user@example.com",
    "name": "Nguyễn Văn A",
    "picture": "https://...",
    "email_verified": true
  }
}
```

## Troubleshooting

ModuleNotFoundError:
```bash
pip install -r requirements.txt
```

401 Unauthorized: Check API keys trong .env

Rate limit: Đợi hoặc upgrade plan

## Logs

Console logs:
```
2026-04-27 10:30:45 [INFO] gom-ai: POST /predict - Received image.jpg
2026-04-27 10:30:50 [INFO] gom-ai.debate.engine: Starting Phase 1
```

Errors: `error_log.txt`

## Testing

```bash
curl http://localhost:8001/

curl -X POST http://localhost:8001/predict -F "file=@test.jpg"

curl -X POST http://localhost:8001/chat \
  -H "Content-Type: application/json" \
  -d '{"question":"Gốm Celadon là gì?"}'
```

## Tài liệu khác

- docs/database.md - Data flow và models
- docs/api.md - API chi tiết
