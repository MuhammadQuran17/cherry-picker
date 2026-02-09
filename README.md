# Auto Git Cherry Picker with WMT (Jira) and VCS (GitLab) integrations

[![Latest Version on Packagist](https://img.shields.io/packagist/v/mu/cherry-picker.svg?style=flat-square)](https://packagist.org/packages/mu/cherry-picker)
[![Total Downloads](https://img.shields.io/packagist/dt/mu/cherry-picker.svg?style=flat-square)](https://packagist.org/packages/mu/cherry-picker)
![GitHub Actions](https://github.com/MuhammadQuran17/cherry-picker/actions/workflows/main.yml/badge.svg)

Language agnostic package for automated Git cherry-picking with WMT (Work Management Tool - Jira) and VCS (Version Control System - GitLab) integrations for team work. Can be usefull to backport changes to branches, clone fixes or features to separate projects. This package streamlines the process of cherry-picking commits across branches, automatically creating merge requests with proper metadata from WMT tickets (Jira).

## How it works (diagram)

![Cherry Picker Diagram](/assets/diagram.png)

## Features

- 🍒 Automated git cherry-pick workflow
- 🔗 WMT (Work Management Tool) - fetches ticket details automatically (Jira)
- 🦊 VCS (Version Control System) - creates merge requests with assignees and reviewers (GitLab)
- 💻 Interactive CLI mode with beautiful prompts
- 📦 Language-agnostic - works as a standalone package with any programming language
- ⚙️ Configurable via .env file

## Requirements

- PHP, composer

## Installation

Please read [QUICKSTART.md](QUICKSTART.md)

## Architecture

The package uses a clean architecture with dependency injection:

- **Container** - PHP DI container managing all services
- **ConfigLoader** - Loads configuration from .env and config files
- **CherryPicker** - Main orchestrator for the cherry-pick workflow
- **GitClient** - Git operations wrapper
- **VCS Provider (GitLabClient)** - GitLab API integration (implements VCSProviderContract)
- **WMT Provider (JiraClient)** - Jira API integration (implements WMTProviderContract)
- **ShellRunner** - Executes shell commands using Symfony Process

### Main Dependencies

- `guzzlehttp/guzzle` - HTTP client for API requests
- `symfony/process` - Process execution
- `laravel/prompts` - Beautiful CLI prompts
- `vlucas/phpdotenv` - Environment variable loading
- `php-di/php-di` - Dependency injection container

### Testing Pest PHP    

Run tests in CLI (need to install dev dependencies with `composer install --dev`):
```bash
./vendor/bin/pest
```

### Changelog

Please see [CHANGELOG](CHANGELOG.md) for more information what has changed recently.

## Contributing

Please see [CONTRIBUTING](CONTRIBUTING.md) for details.

### Security

If you discover any security related issues, please email muhammadumarsotvoldiev@gmail.com instead of using the issue tracker.

## Credits

-   [Muhammad Umar](https://github.com/MuhammadQuran17)
-   [All Contributors](../../contributors)

## License

The MIT License (MIT). Please see [License File](LICENSE.md) for more information.

## PHP Package Boilerplate

This package was generated using the [PHP Package Boilerplate](https://laravelpackageboilerplate.com) by [Beyond Code](http://beyondco.de/).
