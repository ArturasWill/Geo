# Handling Bot Protection & Anti-Bot Measures

Many websites protect their APIs from automated access using various anti-bot techniques. Here's how to identify and handle them:

## Common Bot Protection Systems

| Protection Type | Indicators | Difficulty |
|----------------|-----------|-----------|
| **Cloudflare** | 403 errors, challenge pages, `cf-ray` headers | Medium-High |
| **reCAPTCHA** | Visual/invisible captcha on page/API | High |
| **DataDome** | 403 with `datadome` in response | High |
| **PerimeterX** | `_px`, `_pxhd` cookies, challenge pages | High |
| **Rate limiting** | 429 errors, `X-RateLimit-*` headers | Low-Medium |
| **TLS fingerprinting** | Consistent 403s with correct headers | Medium |

## Strategies to Handle Bot Protection

### 1. Start with the Basics

- Use a realistic `User-Agent` header (copy from your actual browser)
- Include proper `Accept`, `Accept-Language`, `Accept-Encoding` headers
- Send the `Referer` header matching the website
- Maintain proper `Origin` for cross-origin requests
- Keep cookies across requests (session management)

### 2. Slow Down Requests

- Add random delays between requests (500ms-2s)
- Add jitter to avoid predictable patterns
- Respect rate limits strictly
- Mimic human behavior (don't access data sequentially)

### 3. When Facing CAPTCHAs

- **Manual solving**: For small-scale operations, solve captchas yourself
- **CAPTCHA APIs**: Services like 2captcha, Anti-Captcha (paid, ethically questionable)
- **Avoid captchas**: Some endpoints may not require them - test the API directly
- **Browser automation**: Use headless browsers that can render captchas for manual solving

### 4. Using Headless Browsers

When simple HTTP requests fail, use browser automation:

```javascript
// Playwright example with stealth
const { chromium } = require('playwright-extra');
const stealth = require('puppeteer-extra-plugin-stealth')();

const browser = await chromium.launch({ headless: true });
const context = await browser.newContext({
  userAgent: 'Mozilla/5.0...',
  viewport: { width: 1920, height: 1080 }
});

const page = await context.newPage();
await page.goto('https://example.com');

// Extract cookies/tokens
const cookies = await context.cookies();
const token = await page.evaluate(() => localStorage.getItem('token'));
```

### 5. Cookie & Token Management

- Extract required cookies from a valid browser session
- Some protection systems use tokens that change per request
- Look for hidden tokens in HTML or JavaScript
- Some APIs require both cookies AND bearer tokens

## Best Practices

- **Start simple**: Try basic requests before complex solutions
- **Understand the protection**: Use DevTools to see what's being checked
- **Be respectful**: Don't overwhelm servers; slow down if you hit limits
- **Legal first**: Check if there's a legitimate way to get the data (official API, data dumps)
- **Document behavior**: Note what triggers blocks so you can avoid it
- **Have a fallback**: If APIs are too protected, consider alternatives

## Red Flags (When to Reconsider)

⚠️ **You probably shouldn't proceed if:**
- The site explicitly prohibits scraping in robots.txt or ToS
- You need to break CAPTCHA protections repeatedly
- You're facing increasingly aggressive blocks

**Remember:** The goal is to access **public data** efficiently, not to bypass security protecting private information.

---

[← Back to Main Guide](README.md)
