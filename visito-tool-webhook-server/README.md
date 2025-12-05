# Tool Call Webhook Demo (Express)

This repo is a **one-file demo** showing where a **tool call webhook** request arrives and how your backend should respond so the AI assistant can use the result.

- **Server:** Node.js + Express
- **Tool endpoint:** `POST /tools/get-order-status`
- **Payload shape:** `{ arguments, meta }`
- **Response shape:** `{ data: "..." }`

## Requirements

- Node.js 18+ recommended

## Install & run

```bash
npm i express
node demo-tool-webhook-server.js
```

Server starts on:

- http://localhost:3000

### Quick test (local)

```bash
curl -X POST http://localhost:3000/tools/get-order-status \
  -H "Content-Type: application/json" \
  -d '{
    "arguments": { "order_number": "A-1002" },
    "meta": {
      "conversation": "65da7ced90ef936e6d0b0798",
      "contact": "65da7ced90ef936e6d033398"
    }
  }'
```

Expected response:

```json
{ "data": "Status: shipped" }
```

### Expose the webhook with ngrok

1. Install ngrok

2. Start ngrok for port 3000

```bash
ngrok http 3000
```

ngrok will print a public URL like:

- https://abcd-12-34-56-78.ngrok-free.app

3. Your public webhook URL

If ngrok gives you:

- https://abcd-12-34-56-78.ngrok-free.app

Then your tool endpoint becomes:

- https://abcd-12-34-56-78.ngrok-free.app/tools/get-order-status

4. Test via the ngrok URL

```bash
curl -X POST https://abcd-12-34-56-78.ngrok-free.app/tools/get-order-status \
 -H "Content-Type: application/json" \
 -d '{
    "arguments": { "order_number": "A-1002" },
    "meta": {
      "conversation": "65da7ced90ef936e6d0b0798",
      "contact": "65da7ced90ef936e6d033398"
    }
}'
```

5. Create your tool call with Visito with this enpoint url.
