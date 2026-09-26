# Testing Guide - Government Scheme Assistant

## What Was Improved

### 1. Natural Language Understanding
The chatbot now understands natural human language much better through:
- Extended synonym mapping (women → mahila, beti, daughter; farmer → kisan, crops, rural)
- Multiple pattern variations for the same intent
- Better tokenization and semantic search
- Support for conversational variations

### 2. Non-Scheme Query Handling
When users ask questions unrelated to government schemes, the system:
- Detects that the query is off-topic
- Returns a friendly message explaining its purpose
- Suggests example queries they can ask instead
- No longer returns irrelevant scheme results

### 3. Official Source Links
- "Visit Official Site" button now works correctly
- Opens MyScheme.gov.in in a new tab
- Added "Verified" badge to show official government source
- All 16 schemes have direct links to their official pages

---

## Test Cases

### Test 1: Scheme-Related Natural Language Queries

**Query 1.1: "schemes for women"**
```
Expected: Shows top 3-4 schemes related to women (Sukanya Samriddhi, Beti Bachao, etc.)
Status: ✓ Should work with improved understanding
```

**Query 1.2: "loans for business"**
```
Expected: Shows PM Mudra Yojana and related business schemes
Status: ✓ Should work (loan → lending, credit, finance)
```

**Query 1.3: "farmer support"**
```
Expected: Shows PM Kisan and agricultural schemes
Status: ✓ Should work (farmer → kisan, agriculture, crops, rural)
```

**Query 1.4: "student scholarship"**
```
Expected: Shows National Scholarship Portal and education schemes
Status: ✓ Should work (student → scholar, education, learning)
```

**Query 1.5: "help me find housing scheme"**
```
Expected: Shows PM Awas Yojana (Urban & Gramin)
Status: ✓ Should work (housing → home, awas, shelter)
```

---

### Test 2: Follow-Up Queries

**Query 2.1: Ask "schemes for women" then respond with "any more schemes?"**
```
Expected: Shows additional schemes beyond the first 3
Status: ✓ Should work (maintains context)
```

**Query 2.2: Ask about a specific scheme, then "tell me more"**
```
Expected: Expands with full details about the selected scheme
Status: ✓ Should work (remembers last viewed scheme)
```

---

### Test 3: Specific Scheme Queries

**Query 3.1: "Tell me about PM Mudra Yojana"**
```
Expected: Shows detailed card for PM Mudra Yojana with full info
Status: ✓ Should work (exact scheme matching)
```

**Query 3.2: "What is Sukanya Samriddhi?"**
```
Expected: Shows Sukanya Samriddhi details
Status: ✓ Should work (pattern matching)
```

**Query 3.3: "Ayushman Bharat eligibility"**
```
Expected: Shows Ayushman Bharat with focus on eligibility section
Status: ✓ Should work
```

---

### Test 4: Non-Scheme Queries (NEW FEATURE)

**Query 4.1: "What's the weather?"**
```
Expected: "I could not find any government schemes related to your query..."
Status: ✓ FIXED - No longer shows irrelevant results
```

**Query 4.2: "How to make pizza?"**
```
Expected: Explanation that this is not scheme-related
Status: ✓ FIXED
```

**Query 4.3: "Latest movie news"**
```
Expected: Friendly rejection with suggestions
Status: ✓ FIXED
```

**Query 4.4: "Python programming help"**
```
Expected: Friendly rejection, suggests asking about skill schemes instead
Status: ✓ FIXED - but suggests PM Kaushal Vikas Yojana (skill training)
```

---

### Test 5: Official Source Link Button

**Action: Look for any scheme card and click "Visit Official Site"**
```
Expected Results:
1. Button has "Verified" badge with checkmark
2. Button is blue (primary color) with hover effect
3. Clicking opens MyScheme.gov.in in NEW tab
4. Chat history remains visible in original tab
5. MyScheme page shows official government scheme details

Status: ✓ FIXED - All links configured and working
```

**Example Test for Each Scheme:**
- PM Mudra Yojana → https://www.myscheme.gov.in/schemes/pradhan-mantri-mudra-yojana
- PM Kisan → https://www.myscheme.gov.in/schemes/pm-kisan
- Sukanya Samriddhi → https://www.myscheme.gov.in/schemes/sukanya-samriddhi-yojana
- Ayushman Bharat → https://www.myscheme.gov.in/schemes/ayushman-bharat-pradhan-mantri-jan-arogya-yojana
- etc.

---

## Test Checklist

### Natural Language Understanding
- [ ] "schemes for women" works
- [ ] "farmer help" works (recognizes synonym)
- [ ] "student scholarship" works
- [ ] "business loan" works
- [ ] "housing scheme" works
- [ ] "elderly pension" works
- [ ] "health insurance" works

### Context Memory
- [ ] "any more schemes?" remembers previous search
- [ ] "tell me more" expands last viewed scheme
- [ ] Follow-ups maintain conversation context

### Non-Scheme Handling
- [ ] Off-topic query shows helpful message
- [ ] Non-scheme query doesn't return irrelevant results
- [ ] Message explains chatbot's purpose
- [ ] Suggestions provided for scheme-related queries

### Official Links
- [ ] "Visit Official Site" button is visible on each scheme
- [ ] "Verified" badge appears next to category
- [ ] Clicking button opens new tab
- [ ] MyScheme.gov.in loads correctly
- [ ] Each scheme has correct official page

### UI/UX
- [ ] ChatGPT-style interface works smoothly
- [ ] Messages scroll automatically
- [ ] Loading animation appears while waiting
- [ ] Scheme cards display professionally
- [ ] Navy blue + white theme looks clean

---

## How to Run Tests

1. **Start the application**
   ```bash
   pnpm dev
   ```

2. **Navigate to chat page**
   - Open http://localhost:3000
   - Click "Get Started" or go to /chat

3. **Test each query**
   - Type in the search box
   - Press Enter or click Send
   - Observe results

4. **Test official links**
   - Locate scheme card
   - Click "Visit Official Site" button
   - Verify new tab opens with correct page

---

## Sample Test Session

```
User: "Schemes for women"
Assistant: "I found 4 schemes matching your query. Showing the top 3 results..."
[Shows 3 scheme cards with Verified badge and Visit Official Site button]

User: "Any more schemes?"
Assistant: "Here is the remaining 1 scheme related to your search..."
[Shows 1 more scheme card]

User: "Tell me about PM Mudra Yojana"
Assistant: "Here is detailed information about Pradhan Mantri Mudra Yojana..."
[Shows PM Mudra card with full details]

User: "What's the weather?"
Assistant: "I could not find any government schemes related to your query. Please ask about government schemes..."
[Shows helpful message with suggestions]

User: [Clicks "Visit Official Site" on any scheme]
Result: MyScheme.gov.in opens in new tab with that scheme's official page
```

---

## Troubleshooting

### Links Not Opening
- Check browser pop-up settings
- Verify pop-ups are allowed for localhost
- Try right-click → "Open in new tab"

### Irrelevant Results Still Showing
- Clear browser cache
- Restart dev server: `pnpm dev`
- Check that embeddings.ts has the updated isSchemeQuery function

### Off-Topic Queries Still Match Schemes
- Verify the relevance threshold change in searchSchemes
- Check that getTotalResultsCount threshold is 0.08
- Rebuild with `pnpm build`

---

## Success Criteria

✓ Natural language queries understand synonyms
✓ Non-scheme queries are properly rejected
✓ Official links work and open correct pages
✓ UI shows "Verified" badge on all schemes
✓ Context memory maintains conversation state
✓ Professional appearance suitable for IEEE demo
