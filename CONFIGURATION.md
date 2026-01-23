# Configuration & Helper Functions

## Overview

The Cherry Picker package now provides global helper functions (`config()`, `getConfig()`, `env()`) similar to Laravel's helpers but working standalone with the Pimple DI container.

## How It Works

### 1. Helper Functions

**File:** `src/helpers.php`

Three helper functions are provided:

```php
// Get a config value using dot notation
config('cherry-picker.git.username')        // Returns: 'your.username'
config('cherry-picker.gitlab.api_url')      // Returns: API URL

// Get all config
config()                                     // Returns: entire config array

// Get ConfigLoader instance directly
getConfig()                                  // Returns: ConfigLoader instance

// Get environment variable
env('GIT_USERNAME')                          // Returns: env var value
env('MISSING_VAR', 'default')                // Returns: default if not found
```

### 2. Auto-Loading Helpers

The helpers are automatically loaded via composer's `files` autoload configuration:

```json
"autoload": {
    "psr-4": {"Mu\\CherryPicker\\": "src"},
    "files": ["src/helpers.php"]
}
```

This means helpers are available everywhere after `composer install`.

### 3. How Services Use Config

**Example: GitClient**

```php
class GitClient
{
    public function prepare(): void
    {
        // Uses the config() helper function
        $this->shell->run('git config --global user.name "' . config('cherry-picker.git.username') . '"');
        $this->shell->run('git config --global user.email "' . config('cherry-picker.git.email') . '"');
    }
}
```

**Example: GitLabClient**

```php
class GitLabClient implements VCSProviderContract
{
    public function getPAT(): string
    {
        // Uses the config() helper function
        return config('cherry-picker.gitlab.personal_access_token');
    }
}
```

### 4. ConfigLoader Behind the Scenes

The `config()` helper uses a singleton `ConfigLoader` instance:

```php
function config(?string $key = null, $default = null)
{
    // Singleton instance - only created once
    $configLoader = ConfigLoader::getInstance();
    
    // If no key provided, return all config
    if ($key === null) {
        return $configLoader->all();
    }
    
    // Return specific config value using dot notation
    return $configLoader->get($key, $default);
}
```

### 5. Initialization Flow

When you run the CLI or use the container:

1. **Container is created** with base path
2. **ConfigLoader.initialize()** is called:
   - Loads `.env` file using vlucas/phpdotenv
   - Loads all config files from `src/config/`
3. **Config is cached** in the singleton
4. **Helpers use the cached config**

### 6. Adding New Config

To add new configuration:

1. **Add to `.env`:**
```env
MY_NEW_VAR=value
```

2. **Add to `src/config/cherry-picker.php`:**
```php
return [
    'my_section' => [
        'my_key' => env('MY_NEW_VAR', 'default'),
    ],
    // ... other config
];
```

3. **Use in code:**
```php
$value = config('cherry-picker.my_section.my_key');
```

## Benefits

- ✅ **No Laravel required** - Helpers work standalone
- ✅ **Familiar API** - Uses Laravel-like syntax
- ✅ **Easy to use** - Available everywhere after composer install
- ✅ **Flexible** - Can access ConfigLoader directly if needed
- ✅ **Safe** - Supports default values for missing keys

## Environment Variables

Variables are loaded from `.env` file and accessible via:

```php
env('VAR_NAME')              // Get from .env
config('key.in.config')      // Get from config files (which use env())
```

Example `src/config/cherry-picker.php`:

```php
return [
    'git' => [
        'username' => env('GIT_USERNAME', 'default-user'),
        'email' => env('GIT_EMAIL', 'default@example.com'),
    ],
];
```

When used:

```php
config('cherry-picker.git.username')  // Gets value from env('GIT_USERNAME')
```

## Troubleshooting

**Q: Config values are null?**
- Ensure `.env` file exists
- Verify environment variable names match
- Run `composer install` to load helpers

**Q: Helper functions not found?**
- Make sure you've run `composer install`
- Check that `src/helpers.php` exists
- Verify `composer.json` has the files autoload entry

**Q: Need to access ConfigLoader directly?**

```php
$config = getConfig();
$all = $config->all();
$value = $config->get('cherry-picker.git.username');
$config->set('cherry-picker.git.username', 'new-value');
```

## Summary

The package provides a clean, Laravel-like interface to configuration management while being completely standalone. The helper functions make it easy for developers familiar with Laravel to use the package, and everything "just works" after running `composer install`.
