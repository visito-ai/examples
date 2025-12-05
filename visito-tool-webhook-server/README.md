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

Server starts on:
	•	http://localhost:3000
	•	Health check: GET /health

Quick test (local)

curl -X POST http://localhost:3000/tools/get-order-status \
  -H "Content-Type: application/json" \
  -d '{
    "arguments": { "order_number": "A-1002" },
    "meta": {
      "conversation": "65da7ced90ef936e6d0b0798",
      "contact": { "id": "c_123", "number": "5215512345678" }
    }
  }'
```

Expected response:

{ "data": "Status: shipped" }

What to change for your use case
• Replace the in-memory ORDERS map with your real database / API call.
• Add authentication (e.g. HMAC signature, bearer token, IP allowlist) before going to production.

Next steps

# Webhook README (Tool Calls + ngrok)

This document explains how to run the demo webhook locally and expose it publicly using **ngrok** so an AI tool-calling system (e.g., Visito AI) can reach your machine.

## What is a tool call webhook?

A tool call webhook is an HTTP endpoint your assistant can call to:

- fetch data (order status, reservation details, availability)
- perform actions (create appointment, update CRM, send messages)

Your backend receives a JSON payload like:

```json
{
  "arguments": { "order_number": "A-1002" },
  "meta": {
    "conversation": "65da7ced90ef936e6d0b0798",
    "contact": { "id": "c_123", "number": "5215512345678" }
  }
}
```

And it returns:

`{ "data": "Status: shipped" }`

Keep responses short and directly useful to the assistant.

Local usage

1. Start the server

npm i express
node demo-tool-webhook-server.js

Your endpoint will be available at:
• POST http://localhost:3000/tools/get-order-status

2. Test locally with curl

curl -X POST http://localhost:3000/tools/get-order-status \
 -H "Content-Type: application/json" \
 -d '{
"arguments": { "order_number": "A-1002" },
"meta": { "conversation": "conv_1", "contact": { "number": "5215512345678" } }
}'

⸻

Expose the webhook with ngrok

1. Install ngrok

Option A: via ngrok website (recommended)
• Download and install ngrok from the official site
• Authenticate with your token (ngrok config add-authtoken ...)

Option B: if you already have it installed
• Ensure ngrok is available in your PATH

2. Start ngrok for port 3000

ngrok http 3000

ngrok will print a public URL like:
• https://abcd-12-34-56-78.ngrok-free.app

3. Your public webhook URL

If ngrok gives you:
• https://abcd-12-34-56-78.ngrok-free.app

Then your tool endpoint becomes:
• https://abcd-12-34-56-78.ngrok-free.app/tools/get-order-status

4. Test via the ngrok URL

curl -X POST https://abcd-12-34-56-78.ngrok-free.app/tools/get-order-status \
 -H "Content-Type: application/json" \
 -d '{
"arguments": { "order_number": "A-1002" },
"meta": { "conversation": "conv_public", "contact": { "number": "5215512345678" } }
}'

How to wire this into your tool definition

Wherever you configure tools for your assistant, set the tool endpoint URL to the ngrok URL, for example:
• Tool name: get_order_status
• Method: POST
• URL: https://abcd-12-34-56-78.ngrok-free.app/tools/get-order-status

Your assistant will POST { arguments, meta } to that URL.

Adding more tool endpoints

Add another route to demo-tool-webhook-server.js, for example:

app.post("/tools/create-appointment", async (req, res) => {
const { arguments, meta } = req.body;
// ... do work ...
return res.json({ data: "Appointment created for Tue 10:30 AM" });
});

Then expose it through ngrok:
• https://<your-ngrok-domain>/tools/create-appointment

Production notes (important)

ngrok is perfect for demos, but for production you should add:
• Authentication (bearer token, HMAC signature, etc.)
• Rate limiting
• Input validation (Zod/Joi)
• Timeouts + retries (idempotency for actions)
• Logging + tracing (correlation ID per tool call)

Troubleshooting
• I get 404 from ngrok: confirm your server is running and the path matches exactly.
• I get 400 invalid_arguments: ensure arguments.order_number is present and is a string.
• ngrok URL keeps changing: consider a paid ngrok plan or configure a stable domain if needed.
