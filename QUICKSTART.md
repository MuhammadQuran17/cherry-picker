# Cherry Picker - Quick Start Guide

## Installation & Setup

### 1. Install Dependencies

```bash
composer install
```

This will install all required packages:
- guzzlehttp/guzzle (HTTP client)
- symfony/process (Process execution)
- laravel/prompts (CLI prompts)
- vlucas/phpdotenv (Environment variables)
- pimple/pimple (Dependency injection)

### 2. Configure Environment

```bash
cp .env.example .env
```

Edit `.env` with your credentials:

```env
# Git Configuration
GIT_USERNAME=your.username
GIT_EMAIL=your.email@company.com
MR_REVIEWER_NAME=reviewer.username

# GitLab Configuration (Get token from GitLab > Preferences > Access Tokens)
PERSONAL_TOKEN=glpat-xxxxxxxxxxxxxxxxxxxx
GITLAB_API_URL=https://gitlab.da.local/api/v4

# Jira Configuration (Get token from Atlassian Account Settings > Security > API tokens)
JIRA_EMAIL=your.email@company.com
JIRA_TOKEN=ATATTxxxxxxxxxxxxxxxxxxxxxx
JIRA_API_URL=https://your-company.atlassian.net/rest/api/3
```

### 3. Test the Installation

```bash
./bin/cherry-picker --help
```

You should see the help message with all available options.

## Usage Examples

### Example 1: Interactive Mode (Easiest)

```bash
./bin/cherry-picker --interactive
```

Follow the prompts:
1. Enter Jira ticket number (e.g., `PROJ-123`)
2. The tool fetches ticket details automatically
3. Enter commit hashes (space-separated)
4. Select cherry-pick type
5. Review and confirm

### Example 2: Quick Non-Interactive

```bash
./bin/cherry-picker \
  --ticket=PROJ-123 \
  --fix-version=12.0.1 \
  --commits="abc123def def456abc"
```

This will:
- Cherry-pick commits `abc123def` and `def456abc`
- Target branch: `hotfix/12.0.1` or `release/12.0.1`
- Auto-generate branch name: `proj-123-cherry-pick-12-0-1`
- Create merge request with reviewer

### Example 3: Custom Branch and Title

```bash
./bin/cherry-picker \
  --ticket=PROJ-456 \
  --fix-version=11.5.0 \
  --commits="xyz789" \
  --branch=proj-456-urgent-fix \
  --title="[PROJ-456] Urgent security fix for v11.5.0"
```

## What Happens During Execution?

1. **Preparation**
   - Configures git user name/email
   - Stashes current changes
   - Sets up remote with authentication

2. **Branch Operations**
   - Pulls latest from target branch (fix version)
   - Creates new branch for cherry-pick
   - Fetches latest commits

3. **Cherry-Pick**
   - Applies commits with `-x` flag (preserves original commit reference)
   - Detects conflicts automatically

4. **Conflict Resolution** (if needed)
   - Tool pauses and shows conflict message
   - You resolve conflicts in your editor
   - Press Enter to continue
   - Tool stages changes and continues

5. **Finalization**
   - Pushes new branch to remote
   - Creates merge request in GitLab
   - Assigns reviewer
   - Returns to original branch
   - Cleans up local cherry-pick branch
   - Restores stashed changes

## Troubleshooting

### "Composer autoloader not found"
```bash
composer install
```

### "No .env file found"
```bash
cp env.example .env
# Edit .env with your credentials
```

### "Failed to connect to GitLab/Jira"
- Check your API URLs in `.env`
- Verify your access tokens are valid
- Ensure you have network access to the services

### "Permission denied" when running bin/cherry-picker
```bash
chmod +x bin/cherry-picker
```

### Conflicts During Cherry-Pick
1. The tool will pause and show: "Please resolve the conflicts manually..."
2. Open the conflicted files in your editor
3. Resolve the conflict markers (<<<<<<, ======, >>>>>>)
4. Save the files
5. Return to terminal and press Enter
6. The tool continues automatically

## Directory Structure

```
cherry-picker/
├── bin/
│   └── cherry-picker          # Executable CLI script
├── src/
│   ├── CherryPicker.php       # Main orchestrator
│   ├── Container.php          # Dependency injection container
│   ├── Config/
│   │   └── ConfigLoader.php   # Configuration management
│   ├── Clients/
│   │   ├── GitClient.php      # Git operations
│   │   ├── GitLabClient.php   # GitLab API client
│   │   ├── JiraClient.php     # Jira API client
│   │   └── ShellRunner.php    # Shell command execution
│   ├── Contracts/
│   │   └── VCSProviderContract.php  # VCS interface
│   ├── DataTransferObjects/
│   │   └── CherryPickDTO.php  # Data transfer object
│   └── config/
│       └── cherry-picker.php  # Default configuration
├── vendor/                     # Dependencies (after composer install)
├── .env                        # Your configuration (create from env.example)
├── env.example                 # Example configuration
└── composer.json               # Package definition
```

## Next Steps

1. Run `composer install`
2. Configure your `.env` file
3. Try interactive mode: `./bin/cherry-picker --interactive`
4. Read the full [README.md](README.md) for more details

## Support

For issues, please check:
- [README.md](README.md) - Full documentation
- [CONTRIBUTING.md](CONTRIBUTING.md) - Contribution guidelines
- GitHub Issues - Report bugs or request features
