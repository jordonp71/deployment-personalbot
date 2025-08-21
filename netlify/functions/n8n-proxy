// netlify/functions/n8n-proxy.js
// ESM syntax (Netlify supports this)

const TARGET_URL = process.env.N8N_URL;
const USER = process.env.N8N_USERNAME;
const PASS = process.env.N8N_PASSWORD;

export async function handler(event) {
  // Ensure env vars are set
  if (!TARGET_URL || !USER || !PASS) {
    return {
      statusCode: 500,
      body: JSON.stringify({
        error: "Missing required environment variables: N8N_URL, N8N_USERNAME, N8N_PASSWORD"
      })
    };
  }

  // Handle CORS preflight
  if (event.httpMethod === "OPTIONS") {
    return {
      statusCode: 204,
      headers: {
        "Access-Control-Allow-Origin": "*",
        "Access-Control-Allow-Methods": "GET,POST,OPTIONS",
        "Access-Control-Allow-Headers": "Content-Type, Authorization"
      },
      body: ""
    };
  }

  // Preserve query string if present
  const qs = event.rawQuery ? `?${event.rawQuery}` : "";
  const url = `${TARGET_URL}${qs}`;

  // Forward request with Basic Auth header
  const resp = await fetch(url, {
    method: event.httpMethod,
    headers: {
      "Content-Type": event.headers["content-type"] || "application/json",
      "Authorization": "Basic " + Buffer.from(`${USER}:${PASS}`).toString("base64")
    },
    body: ["GET", "HEAD"].includes(event.httpMethod) ? undefined : event.body,
  });

  // Relay response
  const text = await resp.text();
  return {
    statusCode: resp.status,
    headers: {
      "Access-Control-Allow-Origin": "*",
      "Content-Type": resp.headers.get("content-type") || "application/json"
    },
    body: text
  };
}
