# Government Scheme Assistant - Troubleshooting Guide

## Issue: 404 Page Not Found

### Root Causes & Solutions

#### 1. **Browser Cache Issue** (Most Common)
**Symptoms:** 404 appears even though pages exist
**Solution:**
```bash
# Hard refresh in browser:
- Windows/Linux: Ctrl + Shift + R
- macOS: Cmd + Shift + R
- Or clear browser cache manually
```

#### 2. **Dev Server Not Running**
**Symptoms:** Connection refused or 404 on all pages
**Solution:**
```bash
cd /vercel/share/v0-project
pnpm dev
```
Should see: `✓ Ready in [time]ms`

#### 3. **Wrong URL Format**
**Available Routes:**
- `http://localhost:3000/` - Landing page (home)
- `http://localhost:3000/chat` - Chat interface
- `http://localhost:3000/api/chat` - API endpoint (POST only)

**What won't work:**
- `http://localhost:3000/chat/` (trailing slash)
- `http://localhost:3000/api/chat/` (trailing slash)
- Any other undefined routes

#### 4. **Port Conflict**
**Symptoms:** "Port 3000 already in use"
**Solution:**
```bash
# Find and kill process on port 3000
lsof -i :3000
kill -9 <PID>

# Or use different port
pnpm dev -- -p 3001
```

#### 5. **Node Modules Issue**
**Symptoms:** Module not found errors, 500 errors
**Solution:**
```bash
rm -rf node_modules pnpm-lock.yaml
pnpm install
pnpm dev
```

### Testing Routes

#### Test Homepage
```bash
curl http://localhost:3000/
# Should return 200 with HTML
```

#### Test Chat Page
```bash
curl http://localhost:3000/chat
# Should return 200 with chat UI
```

#### Test API Endpoint
```bash
curl -X POST http://localhost:3000/api/chat \
  -H "Content-Type: application/json" \
  -d '{"query":"schemes for women","offset":0}'
# Should return 200 with JSON response
```

Expected response:
```json
{
  "message": "Here is detailed information...",
  "schemes": [{...}],
  "isFollowUp": false
}
```

## Issue: API Returns "Scheme Not Found"

### This is EXPECTED Behavior
When you ask a non-scheme-related query like "what's the weather" or "tell me a joke", the API correctly returns:

```json
{
  "message": "I could not find any government schemes related to your query. Please ask about government schemes, such as...",
  "schemes": [],
  "isFollowUp": false
}
```

This is **NOT an error** - it's the intended behavior to ensure the chatbot stays focused on government schemes.

### Valid Query Examples
```
- "schemes for women"
- "student scholarships"
- "PM Mudra Yojana"
- "tell me about housing schemes"
- "any more schemes?"
- "tell me more"
- "farmer support"
```

## Issue: Links Not Opening

### Official Source Button Not Working

**Symptoms:** "Visit Official Site" button doesn't open MyScheme.gov.in

**Solution 1: Check Browser Settings**
- Ensure pop-ups are not blocked for your domain
- Check if extensions are blocking external links
- Try in an incognito/private window

**Solution 2: Verify Link Function**
```bash
# Check that openMySchemeInNewTab is working
grep -n "openMySchemeInNewTab" /vercel/share/v0-project/lib/myscheme-urls.ts
# Should return the function definition
```

**Solution 3: Test Manually**
In browser console:
```javascript
window.open('https://www.myscheme.gov.in/', '_blank', 'noopener,noreferrer')
```
Should open MyScheme.gov.in in new tab

**Solution 4: Verify Button Event**
The button uses `onClick={() => openMySchemeInNewTab(scheme.id)}`
- Check SchemeCard component has the button
- Verify import: `import { openMySchemeInNewTab } from '@/lib/myscheme-urls'`

## Issue: Chat Not Loading Initial Query

**Symptoms:** URL like `/chat?q=...` doesn't auto-submit query

**Solution:**
This is normal - the initial query parameter is pre-filled in the input, but user still needs to press Enter or click the button to submit. This prevents unintended API calls.

To auto-submit, manually press Enter or click Send button.

## Build & Deployment Issues

### Production Build Fails
```bash
# Clean build
pnpm build
# If fails, check for:
# 1. TypeScript errors: pnpm tsc --noEmit
# 2. ESLint errors: pnpm lint (if configured)
# 3. Missing dependencies: pnpm install
```

### Next.js 16 Specific Issues
The app uses Next.js 16.2.0 with Turbopack. If you see issues:
```bash
# Disable Turbopack temporarily (in next.config.mjs)
turbopack: false

# Or rebuild with:
pnpm build -- --experimental-turbopack
```

## Performance Issues

### Slow Initial Load
```bash
# Check build size
pnpm build
# Look for: "Route (app)" sizes in output

# Verify dev server is using Turbopack
# Should see: "▲ Next.js 16.2.0 (Turbopack)"
```

### High CPU Usage During Development
This is normal with Turbopack. To reduce:
```bash
# Disable file system cache (temporary fix)
# In next.config.mjs:
turbopackFileSystemCacheForDev: false
```

## Chat API Issues

### API Returns Empty Array
**Symptoms:** No schemes found even for valid queries

**Causes:**
1. Query too vague - try more specific keywords
2. Similarity threshold too high - check embeddings.ts

**Debug:**
```bash
# Test with verbose output
curl -X POST http://localhost:3000/api/chat \
  -H "Content-Type: application/json" \
  -d '{"query":"PM Mudra Yojana","offset":0}' | jq .
```

### CORS Error
**Symptoms:** "Cross-Origin Request Blocked"
**Note:** This only happens in browser for cross-domain requests. Same-domain requests are fine.

**Solution:** This app is same-origin (API at `/api/chat`), so shouldn't occur.

## Component Import Errors

### "Module not found" in Build
```bash
# Check file exists
ls -la /vercel/share/v0-project/components/[component-name].tsx

# Check import paths use @ alias
# Should be: import { Foo } from '@/components/foo'
# Not: import { Foo } from '../../components/foo'
```

## Database / Storage Issues
This app uses in-memory data. No database setup needed. All scheme data is in `/lib/schemes-data.ts`.

## Still Having Issues?

### Collect Debug Information
```bash
# 1. Check latest logs
tail -100 /tmp/dev.log

# 2. Verify app structure
find /vercel/share/v0-project/app -type f

# 3. Check dependencies installed
pnpm list next react

# 4. Verify Next.js config
cat /vercel/share/v0-project/next.config.mjs
```

### Reset Everything
```bash
cd /vercel/share/v0-project

# 1. Stop dev server (Ctrl+C)
# 2. Clear build cache and node_modules
pnpm install --force

# 3. Rebuild
pnpm build

# 4. Start fresh
pnpm dev
```

---

**Need more help?** Check:
- TECHNICAL_CHANGES.md - Implementation details
- TEST_GUIDE.md - Comprehensive testing guide
- QUICK_REFERENCE.md - API and component reference
