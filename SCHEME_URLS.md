# Official Scheme URLs - MyScheme.gov.in

All 16 government schemes are now linked to their official pages on the MyScheme.gov.in portal. When a user clicks "Visit Official Site" button on any scheme card, it will open the respective official scheme page.

## Scheme to URL Mapping

| Scheme ID | Scheme Name | Official URL |
|-----------|-------------|--------------|
| pm-mudra-yojana | Pradhan Mantri Mudra Yojana (PMMY) | https://www.myscheme.gov.in/schemes/pmmy |
| pm-kisan | Pradhan Mantri Kisan Samman Nidhi (PM-KISAN) | https://www.myscheme.gov.in/schemes/pradhan-mantri-kisan-samman-nidhi |
| sukanya-samriddhi | Sukanya Samriddhi Yojana | https://www.myscheme.gov.in/schemes/sukanya-samriddhi-yojana |
| pm-awas-yojana-urban | Pradhan Mantri Awas Yojana - Urban (PMAY-U) | https://www.myscheme.gov.in/schemes/pradhan-mantri-awas-yojana-urban |
| pm-awas-yojana-gramin | Pradhan Mantri Awas Yojana - Gramin (PMAY-G) | https://www.myscheme.gov.in/schemes/pradhan-mantri-awas-yojana-gramin |
| pm-ujjwala | Pradhan Mantri Ujjwala Yojana (PMUY) | https://www.myscheme.gov.in/schemes/pradhan-mantri-ujjwala-yojana |
| national-scholarship | National Scholarship Portal Schemes | https://www.myscheme.gov.in/schemes/national-scholarship-portal |
| ayushman-bharat | Ayushman Bharat Pradhan Mantri Jan Arogya Yojana (AB-PMJAY) | https://www.myscheme.gov.in/schemes/ayushman-bharat-pradhan-mantri-jan-arogya-yojana |
| stand-up-india | Stand Up India Scheme | https://www.myscheme.gov.in/schemes/stand-up-india |
| pm-vishwakarma | PM Vishwakarma Scheme | https://www.myscheme.gov.in/schemes/pm-vishwakarma-scheme |
| pm-jan-dhan | Pradhan Mantri Jan Dhan Yojana (PMJDY) | https://www.myscheme.gov.in/schemes/pradhan-mantri-jan-dhan-yojana |
| pm-kaushal-vikas | Pradhan Mantri Kaushal Vikas Yojana (PMKVY) | https://www.myscheme.gov.in/schemes/pradhan-mantri-kaushal-vikas-yojana |
| mahila-samman | Mahila Samman Savings Certificate | https://www.myscheme.gov.in/schemes/mahila-samman-savings-certificate |
| atal-pension | Atal Pension Yojana (APY) | https://www.myscheme.gov.in/schemes/atal-pension-yojana |
| pm-svanidhi | PM Street Vendor's AtmaNirbhar Nidhi (PM SVANidhi) | https://www.myscheme.gov.in/schemes/pm-street-vendors-atmannirbhar-nidhi |
| beti-bachao | Beti Bachao Beti Padhao | https://www.myscheme.gov.in/schemes/beti-bachao-beti-padhao |

## How It Works

### User Perspective
1. User searches for a scheme in the chatbot (e.g., "Pradhan Mantri Mudra Yojana")
2. Chatbot displays the scheme details in a professional card
3. User clicks "Visit Official Site" button on the scheme card
4. The official scheme page on MyScheme.gov.in opens in a new tab

### Technical Implementation

**File: `/lib/myscheme-urls.ts`**
- Maintains a mapping of scheme IDs to official URLs
- `getMySchemeUrl(schemeId)` function retrieves the URL for a given scheme
- `openMySchemeInNewTab(schemeId)` opens the URL in a new tab with security flags

**File: `/components/scheme-card.tsx`**
- Displays a "Visit Official Site" button on each scheme card
- Button passes the scheme ID to `openMySchemeInNewTab()`
- Opens in new tab with `noopener,noreferrer` for security

**File: `/lib/schemes-data.ts`**
- Each scheme has a unique `id` field that maps to the URL in myscheme-urls.ts
- Example: Scheme with `id: "pm-mudra-yojana"` maps to `https://www.myscheme.gov.in/schemes/pmmy`

## Testing

### To test a specific scheme URL:
```javascript
// In browser console:
import { openMySchemeInNewTab } from '@/lib/myscheme-urls'
openMySchemeInNewTab('pm-mudra-yojana')
```

### To verify API returns correct scheme ID:
```bash
curl -X POST http://localhost:3000/api/chat \
  -H "Content-Type: application/json" \
  -d '{"query":"Pradhan Mantri Mudra Yojana","offset":0}'
```

The response will include the scheme object with `id: "pm-mudra-yojana"`, which maps to the correct URL.

## Fallback Behavior

If a scheme ID doesn't have a mapping in `schemeUrlMap`, the button will redirect to the MyScheme.gov.in homepage:
```
https://www.myscheme.gov.in/
```

This ensures users always have access to the official portal, even if a specific URL is unavailable.

## Adding New Schemes

To add a new scheme with its official URL:

1. Add the scheme to `/lib/schemes-data.ts` with a unique `id`
2. Add the mapping to the `schemeUrlMap` object in `/lib/myscheme-urls.ts`:
   ```typescript
   'scheme-id': 'https://www.myscheme.gov.in/schemes/scheme-slug'
   ```
3. The button will automatically use the new URL when displaying that scheme
