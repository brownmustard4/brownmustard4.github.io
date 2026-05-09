# API Key Security Setup

## Overview
Your Google Maps API key has been removed from the public repository to prevent unauthorized use and billing abuse.

## Files Added/Modified

### 1. `.gitignore` (new)
- Excludes `config.js`, `.env`, and other sensitive files from version control
- Prevents accidental commits of API keys

### 2. `config.js` (new)
- **Location:** `.gitignore` entry (not committed)
- **Purpose:** Local API key configuration
- **Content:** 
  ```javascript
  window.GOOGLE_MAPS_API_KEY = 'YOUR_ACTUAL_API_KEY_HERE';
  ```

### 3. `index.html` (updated)
- Removed hardcoded API key from script tag
- Changed script loading to dynamic injection via `config.js`
- Added fallback error handling

### 4. `config-template.html` (reference only)
- Shows how to structure the script tag
- For documentation purposes

---

## Setup Instructions

### Step 1: Regenerate Your API Key
1. Go to [Google Cloud Console](https://console.cloud.google.com)
2. **Delete** the exposed key: `AIzaSyC-SSluiPz6o0960WILZFjQvKi00T-3Du0`
3. Create a **new API key** with:
   - **Restriction Type:** HTTP referrers
   - **Allowed referrers:** `brownmustard4.github.io`
   - **API Restrictions:** Enable only:
     - Google Maps JavaScript API
     - Places API

### Step 2: Local Development
1. Create `config.js` in your repository root (don't commit it):
   ```javascript
   window.GOOGLE_MAPS_API_KEY = 'YOUR_NEW_API_KEY_HERE';
   ```

2. Add to `.gitignore`:
   ```
   config.js
   .env
   ```

### Step 3: Production Deployment (GitHub Pages)
For GitHub Pages static sites, you have two options:

#### Option A: Build Process with Environment Variables
1. Use a build tool (e.g., Vite, Webpack) to inject the key at build time
2. Set GitHub Actions secret: `GOOGLE_MAPS_API_KEY`
3. Build step injects it into `config.js`

#### Option B: Backend API Endpoint (Recommended)
1. Create a serverless function (Vercel, Netlify, Firebase Functions)
2. Function returns API key only from authorized origins
3. HTML calls endpoint to load config:
   ```javascript
   fetch('/.netlify/functions/get-config')
     .then(r => r.json())
     .then(config => {
       window.GOOGLE_MAPS_API_KEY = config.googleMapsKey;
       // Load Google Maps script
     });
   ```

#### Option C: Server-Side Rendering
If you migrate from static to a Node server, inject at server startup:
```javascript
// server.js
const apiKey = process.env.GOOGLE_MAPS_API_KEY;
app.get('/config.js', (req, res) => {
  res.setHeader('Content-Type', 'application/javascript');
  res.send(`window.GOOGLE_MAPS_API_KEY = '${apiKey}';`);
});
```

---

## Quick Testing

### Local Testing
```bash
# 1. Create config.js with your test key
echo "window.GOOGLE_MAPS_API_KEY = 'YOUR_TEST_KEY';" > config.js

# 2. Open index.html in a browser
# 3. Open DevTools Console
# 4. Type: console.log(window.GOOGLE_MAPS_API_KEY)
# 5. Should output your key (not 'YOUR_API_KEY_HERE')
```

### Verify Security
```bash
# Verify config.js is in .gitignore
grep "config.js" .gitignore

# Verify no API keys in repo
git log -p --all -S 'AIzaSy' | head -20  # Should show history only
```

---

## Troubleshooting

### Address Autocomplete Not Working
1. Check browser console for warnings
2. Verify `config.js` exists and loads (DevTools Network tab)
3. Confirm API key is valid in Google Cloud Console
4. Check API key restrictions match your domain

### API Key Rejected
- Verify referrer restrictions include your domain
- Check that Places API is enabled in Google Cloud Console
- Ensure key is for a valid GCP project with billing enabled

---

## Best Practices
✅ Never commit API keys to version control
✅ Use `.gitignore` to exclude sensitive files
✅ Restrict API keys by domain and API
✅ Enable billing alerts in Google Cloud Console
✅ Rotate keys periodically
✅ Monitor usage in Google Cloud Console

---

## References
- [Google Cloud Documentation](https://cloud.google.com/docs/authentication/api-keys)
- [Places API Setup](https://developers.google.com/maps/documentation/places/web-service/get-api-key)
- [Security Best Practices](https://developers.google.com/maps/best-practices)
