# Fixing Browser Caching Issues

If your HTML/CSS changes aren't appearing when viewing through localhost, follow these steps:

## ✅ Solution 1: Use the Custom Server (Recommended)

The `run.sh` script now uses a custom Python server (`server.py`) that automatically sends no-cache headers:

```bash
./run.sh
```

This prevents browsers from caching files during development.

## ✅ Solution 2: Hard Refresh Your Browser

After making changes, do a hard refresh:

- **Mac:** `Cmd + Shift + R` or `Cmd + Option + R`
- **Windows/Linux:** `Ctrl + Shift + R` or `Ctrl + F5`

## ✅ Solution 3: Disable Cache in Developer Tools

1. Open Developer Tools (F12 or Right-click → Inspect)
2. Go to the **Network** tab
3. Check the **"Disable cache"** checkbox
4. Keep Developer Tools open while developing

## ✅ Solution 4: Clear Browser Cache

1. Open Developer Tools (F12)
2. Right-click the refresh button
3. Select **"Empty Cache and Hard Reload"**

## ✅ Solution 5: Add Cache Headers to HTML Files

Run the script to add cache-busting meta tags to all HTML files:

```bash
./add-cache-headers.sh
```

Or manually add these tags to each HTML file's `<head>` section:

```html
<meta http-equiv="Cache-Control" content="no-cache, no-store, must-revalidate" />
<meta http-equiv="Pragma" content="no-cache" />
<meta http-equiv="Expires" content="0" />
```

## 🔍 Why This Happens

Browsers cache files to improve performance. When you open files directly (file://), browsers often bypass cache, but when using a local server (http://localhost), browsers may cache files. The custom server fixes this by sending headers that tell browsers not to cache files.

## 💡 Best Practice

Always use the `./run.sh` script during development - it's configured to prevent caching automatically!
