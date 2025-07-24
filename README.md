# TPM Agent: AI-Powered GitHub Issue Enhancer

## Overview

TPM Agent is a helpful tool (called a GitHub Action) that uses artificial intelligence to review and improve your GitHub issues. Think of it as a smart project manager that rewrites unclear tasks into clean, complete, and engineer-ready user stories.

It makes your GitHub issues:

1.Clearer to understand

2.Easier to prioritize

3.Ready for developers to work on immediately

## Features
-**AI-Driven Issue Review**
TPM Agent reads each GitHub issue and:

Summarizes it clearly

Checks if enough information is provided

Tells you if it’s ready for developers to start working

-**Improved User Stories**
If the issue is unclear, the agent:

Suggests a better title

Rewrites the description in a user-focused way

Adds "Acceptance Criteria" so the team knows when the task is done

-**Smart Label Suggestions**
Automatically recommends up to 3 relevant labels (like bug, enhancement, or UI) to keep your board organized.

-**Clean Formatting with Markdown**
Uses GitHub's markdown formatting to:

Make the AI’s suggestions easy to read

Seamlessly update the issue with structured content

-**Safe Input Handling**
Double-checks that all required configuration values (like your AI API key) are valid before running.

-**Modular, Maintainable Codebase**
Built in a clean, modular way—so developers can easily:

Understand how it works

Extend or customize it for specific workflows

## Usage

### As a GitHub Action

This action is designed to run in a Docker container as part of your GitHub workflow. It requires configuration of environment variables for GitHub and Azure OpenAI access.

#### Example Workflow

```yaml
name: AI Issue Enhancer
on:
  issues:
    types: [opened, edited]
  issue_comment:
    types: [created]

jobs:
  enhance:
    runs-on: ubuntu-latest
    steps:
      - name: Run TPM Agent
        uses: mattdot/tpmagent@v1
        with:
          github_token: ${{ secrets.GITHUB_TOKEN }}
          github_repository: ${{ github.repository }}
          github_event_name: ${{ github.event_name }}
          github_issue_id: ${{ github.event.issue.number }}
          check_all: false
          azure_openai_api_key: ${{ secrets.AZURE_OPENAI_KEY }}
          azure_openai_target_uri: ${{ secrets.AZURE_OPENAI_TARGET_URI }}
          github_issue_comment_id: ${{ github.event.comment.id }}
```

### Local Development

1. Clone the repo.

2. Install dependencies:

   ```bash
   pip install -r requirements.txt
   ```

3. Run/test scripts in `src/` as needed.

### Local Testing with Docker

You can test the action locally by running the Docker container directly. Example:

```bash
docker build -t tpmagent .
docker run --rm \
  -e INPUT_GITHUB_TOKEN=your_github_token \
  -e INPUT_GITHUB_REPOSITORY=owner/repo \
  -e INPUT_GITHUB_EVENT_NAME=issues \
  -e INPUT_GITHUB_ISSUE_ID=123 \
  -e INPUT_AZURE_OPENAI_API_KEY=your_openai_key \
  -e INPUT_AZURE_OPENAI_TARGET_URI=your_openai_target_uri \
  tpmagent
```

Adjust the environment variables as needed for your test scenario. For `issue_comment` events, also set `INPUT_GITHUB_ISSUE_COMMENT_ID`.

## File Structure

- `src/main.py` - Entry point, event handling, AI integration
- `src/github_utils.py` - GitHub API helpers
- `src/openai_utils.py` - OpenAI/Semantic Kernel helpers
- `src/response_models.py` - Markdown parsing/generation
- `src/prompts.py` - Prompt construction
- `action.yml` - GitHub Action metadata
- `requirements.txt` - Python dependencies
- `.github/workflows/` - Example workflows

## Inputs

### Required for All Events

| Name                | Description                                            | Required | Example                       |
| ------------------- | ------------------------------------------------------ | -------- | ----------------------------- |
| `github_token`      | GitHub token for API access                            | Yes      | `${{ secrets.GITHUB_TOKEN }}` |
| `github_repository` | Repository in `owner/repo` format                      | Yes      | `octocat/Hello-World`         |
| `github_event_name` | Name of the GitHub event                               | Yes      | `issues` or `issue_comment`   |
| `github_issue_id`   | ID of the GitHub issue to process                      | Yes      | `123`                         |
| `check_all`         | If true, checks all issues (overrides label filtering) | No       | `true` or `false`             |

### Additional Required for `issues` Event

| Name                      | Description                             | Required | Example                                  |
| ------------------------- | --------------------------------------- | -------- | ---------------------------------------- |
| `azure_openai_api_key`    | Azure OpenAI API key                    | Yes      | `${{ secrets.AZURE_OPENAI_KEY }}`        |
| `azure_openai_target_uri` | Azure OpenAI target URI (full endpoint) | Yes      | `${{ secrets.AZURE_OPENAI_TARGET_URI }}` |

### Additional Required for `issue_comment` Event

| Name                      | Description                               | Required | Example |
| ------------------------- | ----------------------------------------- | -------- | ------- |
| `github_issue_comment_id` | ID of the GitHub issue comment to process | Yes      | `456`   |

See `action.yml` for a full list and details.

## Configuration

- Requires Azure OpenAI credentials and GitHub token as inputs or environment variables.
- See `action.yml` for all supported inputs.

## Supported Workflow Events and Triggers

This action supports the following GitHub workflow events:

- **issues**
  - Triggered on issue events such as `opened` and `edited`.
  - Used to analyze and enhance new or updated issues.
- **issue_comment**
  - Triggered on issue comment events such as `created`.
  - Used to apply enhancements when a user comments with a specific command (e.g., `/apply`).

Example configuration in your workflow:

```yaml
on:
  issues:
    types: [opened, edited]
  issue_comment:
    types: [created]
```

See the [GitHub Actions documentation](https://docs.github.com/en/actions/using-workflows/events-that-trigger-workflows) for more details on workflow events and triggers.

## Contributing

See [CONTRIBUTING.md](CONTRIBUTING.md) for guidelines.

## License

MIT License. See [LICENSE](LICENSE).
