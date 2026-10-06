### 10.16 Security & XSS Prevention

**Rule:** Never render unsanitized user content. All dynamic HTML goes through DOMPurify.

**Install:** `pnpm add dompurify @types/dompurify`

**Sanitized HTML rendering:**

```tsx
import DOMPurify from 'dompurify';

function SafeHtml({ html }: { html: string }) {
  const sanitized = DOMPurify.sanitize(html, {
    ALLOWED_TAGS: ['p', 'br', 'strong', 'em', 'u', 'a', 'ul', 'ol', 'li'],
    ALLOWED_ATTR: ['href', 'target', 'rel'],
  });
  return <div dangerouslySetInnerHTML={{ __html: sanitized }} />;
}
```

**URL validation (prevent `javascript:` injection):**

```tsx
export function validateUrl(url: string): string {
  try {
    const parsed = new URL(url);
    if (!['http:', 'https:'].includes(parsed.protocol)) return '';
    return parsed.toString();
  } catch {
    return '';
  }
}

// Usage
<a href={validateUrl(userUrl) || '#'}>Link</a>
```

**CSP for static hosting (Vite build):**

```html
<!-- index.html -->
<meta http-equiv="Content-Security-Policy"
  content="default-src 'self';
           style-src 'self' 'unsafe-inline';
           script-src 'self' 'unsafe-inline';
           img-src 'self' data: blob:;
           font-src 'self' data:;
           connect-src 'self' https://api.yourdomain.com;" />
```

**MUI v6 CSP requirements (static hosting):**

- `style-src 'self' 'unsafe-inline'` — Emotion injects inline styles
- `script-src 'self' 'unsafe-inline'` — Required for inline scripts
- Add `connect-src` for your API domain

**Token storage:** HttpOnly cookies set by server. Client never accesses tokens directly. Use `credentials: 'include'` on fetch.

**Never do:**

- `element.innerHTML = userInput`
- `dangerouslySetInnerHTML={{ __html: userInput }}` without DOMPurify
- `eval()` or `new Function(userInput)`
- Store tokens in `localStorage`/`sessionStorage`
```