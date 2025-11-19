# How to use Internal Web APIs?

### Overview

This guide provides practical, step-by-step instructions on how to identify, analyze, and leverage internal web APIs used by public websites and applications. It covers discovery techniques, request setup, authentication patterns, pagination strategies, error handling, and best practices for reliably accessing structured data from undocumented endpoints.

### Goal

Access public data more efficiently than scraping by calling the same
JSON/GraphQL APIs used by the site.

### When to Use

Use internal APIs when:

- The site exposes structured JSON/GraphQL endpoints
- Endpoints are more stable than HTML structure
- You need real-time or paginated data

Prefer official APIs when available.

## Workflow

1. Identify endpoints via Network tab or GraphQL operations.
2. Capture required headers/auth and replicate in a request client.
3. Implement pagination with respectful delays/backoff.
4. Normalize responses to internal schema and preserve source fields as needed.
5. Validate and de-duplicate. Log errors with enough context.
6. Store/export results. Resume from checkpoints on failure.

---

### How to Discover APIs

1.  **Browser DevTools → Network tab**
    - Trigger actions on the site and inspect `XHR` / `fetch` /
      `graphql` calls.
    - Note the **URL**, **method**, **params**, **headers**, and
      **response**.
    - Right-click → `Copy as fetch (Node.js)` (or `Copy as cURL"`) for a ready-to-use request template.
2.  **Inspect JavaScript Bundles**
    - Examine the website’s JavaScript files (often named `main.js`, `bundle.js`, etc.).
    - Look for hardcoded API endpoints, route patterns, or GraphQL query names within these files.
3.  **GraphQL Endpoints**
    - Look for `/graphql` calls and capture **query** + **variables**.

---

### Reverse Engineering an Endpoint

Learn how to analyze and understand API endpoints by testing parameters, inspecting responses, and identifying authentication patterns.

**[📖 Read the full guide →](reverse-engineering.md)**

---

### Handling Bot Protection & Anti-Bot Measures

Strategies for identifying and handling bot protection systems like Cloudflare, reCAPTCHA, and other anti-bot measures.

**[📖 Read the full guide →](bot-protection.md)**

---

### Required Request Elements

| Item              | Details                                                                       |
| ----------------- | ----------------------------------------------------------------------------- |
| Base URL & method | Copy exactly from Network (e.g., `GET https://api.example.com/v1/items`).     |
| Params/body       | Copy query params and JSON body of the request you observed.                  |
| Headers           | Include `Accept`, `User-Agent`, `Origin`, `Referer` etc.                      |
| Auth              | Use cookies, bearer/JWT, or API keys as required (store in env vars).         |
| Pagination        | Choose the strategy the API uses: offset/limit, cursor/next, or page numbers. |

### Things to Note

#### 1. Pagination

Handle one of these patterns:
| Pagination Type | Example | How to Advance |
|-----------------|-----------------------------|-----------------------------------------|
| **Offset** | `?offset=0&limit=50` | Increase `offset` until no results |
| **Cursor** | `nextCursor` or `next` | Follow cursor field until it is null |
| **Page numbers**| `?page=1` | Increase page number until no results |

#### 2. Error Handling

| Error Type           | Handling Strategy              |
| -------------------- | ------------------------------ |
| **429 (Rate limit)** | Retry with exponential backoff |
| **5xx**              | Retry 2-3 times                |
| **401/403**          | Re-authenticate                |
| **4xx (others)**     | Log and skip                   |

#### 3. Rate Limiting

- Add 200-500ms delay between requests
- Implement exponential backoff: 1s, 2s, 4s, 8s
- Respect `Retry-After` headers

#### 4. Using Proxies

Proxies help bypass rate limits, geo-restrictions, and IP blocks.

**When to use:**
- You're rate-limited by IP
- API restricts certain countries/regions
- Rotating IPs to appear as different users

**Types:**
| Type | Use Case | Cost |
|------|----------|------|
| **Datacenter** | Fast, cheap, easily detected | $ |
| **Residential** | Real IPs, harder to detect | $$$ |
| **Rotating** | Auto IP rotation per request | $$-$$$ |

**Quick example (Node.js with axios):**
```javascript
const axios = require('axios');

const response = await axios.get('https://api.example.com/data', {
  proxy: {
    host: 'proxy.example.com',
    port: 8080,
    auth: { username: 'user', password: 'pass' }
  }
});
```

**Free options:** Be cautious with free proxies (slow, unreliable, security risks). Better: use your own VPS as a proxy or paid services (Bright Data, Oxylabs, Smartproxy).

---

### Tools

| Category         | Options                                           | When to use                                   |
| ---------------- | ------------------------------------------------- | --------------------------------------------- |
| HTTP             | fetch, axios                                      | REST/JSON endpoints.                          |
| GraphQL          | Lightweight clients                               | Query/mutate via `/graphql`.                  |
| Headless browser | Playwright, Puppeteer                             | Only if tokens/anti-bot require real runtime. |
| API Testing      | Postman, Insomnia                                 | Manual testing and exploring endpoints.       |
| Helpers          | Retry/backoff, file streams, User-Agent utilities | Reliability and throughput control.                 |

---

#### Using Postman for API Exploration

[Postman](https://www.postman.com/) is an excellent tool for manually testing and exploring internal APIs before writing code:

1.  **Quick Setup**
    - Import requests directly from browser DevTools (right-click → `Copy as cURL` → Import in Postman).
    - Organize requests into collections by site or project.

2.  **Testing Multiple Query Parameters**
    - Use the **Params** tab to easily add/remove query parameters without manually editing URLs.
    - Test different combinations of filters, sort orders, and pagination options.
    - Example: `?category=tech&status=active&sort=date&order=desc&limit=50`

3.  **Managing Complex Filters**
    - For array parameters: `?tags=javascript&tags=api&tags=tutorial`
    - For nested filters: `?filter[status]=active&filter[date][gte]=2025-01-01`
    - Use Postman's bulk edit mode for query parameters to quickly adjust multiple values.

4.  **Environment Variables**
    - Store base URLs, auth tokens, and common parameters as environment variables.
    - Switch between development/staging/production environments easily.
    - Example: `{{base_url}}/api/v1/items?token={{api_token}}`

5.  **Code Generation**
    - Once you've perfected a request in Postman, use the **Code Snippet** feature to generate code in various languages (JavaScript, Python, cURL, etc.).
    - This saves time when moving from exploration to implementation.

**Best Practices:**
- Document your findings in request descriptions.
- Use folders to organize related endpoints.
- Share Samples with your team for consistent testing.

---

### Output & Storage

- Save data as JSON or CSV (small chunks if large data).
- Keep raw responses optional (for debugging).
- Use deterministic filenames for resumable jobs.

---

### Data modeling and normalization

- Define minimal internal types (e.g., id, name, url, dates, cover_image) and map provider fields to them.
- Keep source-specific attributes under a `source_` prefix or a nested `source` object.
- Record `source_id`, `source_url`, and fetch metadata (timestamps, status) for traceability.

**Example of normalized item in JSON:**

```json
{
  "id": "item_abc123",
  "name": "Sample Track Title",
  "url": "https://example.com/item/abc123",
  "cover_image": "https://example.com/images/abc123.jpg",
  "published_date": "2025-02-04T15:22:00Z",
  "source_id": "abc123",
  "source_url": "https://example.com/item/abc123",
  "fetched_at": "2025-02-04T15:30:00Z"
}
```

---

### Common Issues and Fixes

| Issue                | Fix                                                             |
| -------------------- | --------------------------------------------------------------- |
| Token/cookie expired | Refresh or re-export credentials from a valid session.          |
| Schema changed       | Validate schema before mapping                                  |
| Rate-limited         | Reduce concurrency, add jitter, respect `Retry-After`.          |
| Anti-bot checks      | Use realistic headers, stable `User-Agent`, slow down requests. |


## Examples

### Fetching Data from an Internal API
```javascript
const BASE_URL = 'https://api.example.com/v1/items';
const headers = {
  'Accept': 'application/json',
  'User-Agent': 'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36',
  'Authorization': 'Bearer YOUR_TOKEN' 
};

async function fetchItems() {
  let offset = 0;
  const limit = 50;
  let allItems = [];

  while (true) {
    const response = await fetch(`${BASE_URL}?offset=${offset}&limit=${limit}`, { headers });
    
    if (response.status === 429) {
      await sleep(2000); 
      continue;
    }
    
    const data = await response.json();
    if (!data.items.length) break;
    
    allItems.push(...data.items);
    offset += limit;
    await sleep(300); 
  }
  
  return allItems;
}

function sleep(ms) {
  return new Promise(resolve => setTimeout(resolve, ms));
}
```
## Conclusion

By utilizing internal APIs discovered via browser tools, and following robust patterns for requests, authentication, data modeling, and error handling, you can collect public site data efficiently and reliably.
