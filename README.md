# Examples

A collection of small, runnable demos used to showcase Visito-style integrations and patterns.  
Each demo is intentionally minimal so you can copy/paste it into your own project.

## Demos

### 1) Tool Call Webhook (Express)

A one-file Node/Express server that shows:

- where a **tool call** request arrives (`POST /tools/get-order-status`)
- how to read `{ arguments, meta }`
- how to respond with `{ data: "..." }` so the assistant can use the result

**Path**

- [`tool-call-webhook/`](https://github.com/visito-ai/examples/tree/main/visito-tool-webhook-server)

**Docs**

- `tool-call-webhook/README.md`

## Links

Visit our site -> [visitoai.com](https://www.visitoai.com/)
Visit our docs -> [visitoai.com](https://docs.visitoai.com/api-docs/)
