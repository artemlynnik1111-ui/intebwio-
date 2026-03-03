# API Keys Management - Centralized Configuration

## Overview
All API keys are now managed in a **single centralized location** instead of being scattered across multiple files. This improves security, maintainability, and configuration management.

## Architecture

### 1. **Central Storage: `includes/config.php`**
- Single source of truth for all API keys
- Loads keys from `APIKeyManager` class
- All other files use constants defined here
- Located: `/public_html/includes/config.php`

### 2. **Key Loader: `includes/apikeys.php`**
- `APIKeyManager` class provides secure loading
- Reads from environment variables (recommended for production)
- Falls back to placeholder values
- Located: `/public_html/includes/apikeys.php`

### 3. **Frontend API: `api/get-api-keys.php`**
- Endpoint for JavaScript frontend to get API keys
- Returns JSON with safe-to-expose keys
- Located: `/public_html/api/get-api-keys.php`

## Files Updated

### PHP Backend Files
Files that now use centralized config instead of hardcoded keys:

```
✓ /public_html/includes/config.php       [MAIN CONFIG]
✓ /public_html/includes/apikeys.php      [KEY MANAGER]
✓ /public_html/list-gemini-models.php    [Updated]
✓ /public_html/test-timeout.php          [Uses config]
✓ /public_html/test-simple-timeout.php   [Updated]
✓ /public_html/test-gemini-api.php       [Updated]
✓ /public_html/diagnose-gemini.php       [Updated]
✓ /public_html/api/get-api-keys.php      [New API]
```

### JavaScript Frontend Files
```
✓ /public_html/index.html                [Updated to fetch keys]
```

## How It Works

### Backend (PHP)
```php
// In any PHP file, just require the config:
require_once __DIR__ . '/includes/config.php';

// Use the constants defined there:
$geminiKey = GEMINI_API_KEY;
$searchKey = GOOGLE_SEARCH_API_KEY;
```

### Frontend (JavaScript)
```javascript
// Load keys from the API endpoint:
async function loadAPIKeys() {
    const response = await fetch('/api/get-api-keys.php');
    const keys = await response.json();
    // Use: keys.gemini, keys.googleSearch, etc.
}
```

## Setup Instructions

### 1. Set Environment Variables (Production - Recommended)
```bash
export GEMINI_API_KEY='AIzaSy...'
export GOOGLE_SEARCH_API_KEY='...'
export SERPAPI_KEY='...'
```

Or use `.env` file with a PHP library like `vlucas/phpdotenv`

### 2. Or Modify `apikeys.php` Directly
Edit `/public_html/includes/apikeys.php` and replace placeholder values:
```php
self::$keys['gemini'] = 'AIzaSy...your_actual_key...';
self::$keys['google_search'] = '...';
self::$keys['serpapi'] = '...';
```

### 3. Set File Permissions (Important!)
```bash
chmod 600 /public_html/includes/apikeys.php
chmod 600 /public_html/includes/config.php
```

### 4. Add to .gitignore (CRITICAL!)
```bash
# Never commit actual API keys to version control
public_html/includes/config.php
public_html/includes/apikeys.php
.env
```

## Security Best Practices

✅ **DO:**
- Use environment variables for production
- Store actual keys in a separate `.env` file (not in repo)
- Set proper file permissions (600)
- Add config files to `.gitignore`
- Use different keys for different environments

❌ **DON'T:**
- Commit actual API keys to version control
- Hardcode keys in JavaScript or HTML
- Expose sensitive keys to frontend
- Share API credentials through email
- Use placeholder keys in production

## Key Locations Map

| Key Name | Constant | Definition |
|----------|----------|-----------|
| Gemini API | `GEMINI_API_KEY` | `config.php` line 42 |
| Google Search | `GOOGLE_SEARCH_API_KEY` | `config.php` line 43 |
| SerpAPI | `SERPAPI_KEY` | `config.php` line 45 |
| Google (legacy) | `GOOGLE_API_KEY` | `config.php` line 41 |

## Accessing Keys in Different Contexts

### In PHP Files
```php
require_once 'includes/config.php';
$key = GEMINI_API_KEY;
```

### In JavaScript
```javascript
fetch('/api/get-api-keys.php')
    .then(r => r.json())
    .then(keys => {
        console.log(keys.gemini);
    });
```

### Via APIKeyManager
```php
require_once 'includes/apikeys.php';
$key = APIKeyManager::getKey('gemini');
$allKeys = APIKeyManager::getAllKeys(); // (masked)
$issues = APIKeyManager::validateConfiguration();
```

## Migration Complete

All hardcoded API keys have been removed from:
- ✓ `index.html` (JavaScript)
- ✓ `list-gemini-models.php`
- ✓ `test-timeout.php`
- ✓ `test-simple-timeout.php`
- ✓ `test-gemini-api.php`
- ✓ `diagnose-gemini.php`

All of these now load keys from the centralized configuration.
