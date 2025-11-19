# Reverse Engineering an Endpoint

Once you've discovered an API endpoint, you often need to reverse engineer it to understand its behavior, parameters, and capabilities. Here's how:

## 1. Analyze Request Patterns

- Make multiple requests with different parameters to understand what changes in the response.
- Test boundary conditions: empty values, null, very large numbers, special characters.
- Document which parameters are required vs optional.

## 2. Inspect Response Structure

- Map out the JSON/GraphQL response schema.
- Note nested objects, arrays, and data types.
- Identify pagination metadata (total count, hasNext, cursors).

## 3. Test Parameter Variations

- Try different filters, sort orders, and field selections.
- Test query parameters like `?fields=`, `?include=`, `?expand=` to see if you can control response size.
- Look for undocumented parameters in JavaScript bundles.

## 4. Authentication & Authorization

- Test if the endpoint works without auth (sometimes public data doesn't require it).
- Identify the auth mechanism: cookies, JWT tokens, API keys, or OAuth.
- Check if tokens expire and how to refresh them.

## 5. Rate Limits & Quotas

- Make successive requests to trigger rate limiting.
- Note the response headers (`X-RateLimit-*`, `Retry-After`).
- Document the limits (requests per second/minute).

---

[← Back to Main Guide](README.md)
