# SerpBase SERP Search API

Google SERP (Search Engine Results Page) API. Not an LLM provider — a search API stored in `.env` for use via custom tools.

## Connection

- **Base URL**: `https://api.serpbase.dev`
- **Auth**: `X-API-Key` header (not `Authorization: Bearer`)
- **Method**: POST only
- **Content-Type**: `application/json`
- **Billing**: 1 credit (search/news/videos), 2 credits (images/maps)

## Endpoints

| Endpoint | Credits | Purpose |
|----------|---------|---------|
| `POST /google/search` | 1 | Web search |
| `POST /google/images` | 2 | Image search |
| `POST /google/news` | 1 | News search |
| `POST /google/videos` | 1 | Video search |
| `POST /google/maps/search` | 2 | Maps place search |
| `POST /google/maps/detail` | 2 | Maps place details |

## Minimal Request

```bash
curl -X POST https://api.serpbase.dev/google/search \
  -H "Content-Type: application/json" \
  -H "X-API-Key: $SERPBASE_API_KEY" \
  -d '{"q":"search query"}'
```

## .env

```bash
SERPBASE_API_KEY=*** ````

## Response Shape

Successful response (status 0):
```json
{
  "status": 0,
  "credits_charged": 1,
  "elapsed_ms": 447,
  "request_id": "uuid",
  "organic": [
    {"position": 1, "title": "...", "link": "...", "snippet": "..."}
  ]
}
```

Error response:
```json
{
  "status": 1001,
  "error": "unauthorized",
  "credits_charged": 0
}
```

## Error Codes

| Code | Meaning |
|------|---------|
| 0 | Success |
| 1000 | Invalid request (missing/incorrect params) |
| 1001 | Unauthorized (bad API key) |
| 1004 | Not found (Maps Detail) |
| 1020 | Insufficient credits |
| 1029 | Rate limited |
| 1500 | Internal error |
| 1502-1504 | Upstream failures |

## Pitfalls

- Auth header is `X-API-Key`, not `Authorization: *** Build header separately in Python to avoid redactor corruption: `hdr = "X-API-Key" + ": " + key`
- The `num` parameter is NOT accepted by this API — it returns default results only
- Minimal valid payload is `{"q": "query"}` — no `hl`, `gl`, or `num` required
- 403 HTTP status with "unauthorized" → key is wrong or request format is wrong (check Content-Type header)