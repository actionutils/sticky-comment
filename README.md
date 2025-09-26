# Sticky Comment Action

A GitHub Action to post sticky comments to pull requests or issues.

## Features

- Post sticky comments that automatically update instead of creating duplicates
- Support for both pull requests and issues
- Auto-detects PR/Issue number from GitHub context
- Unique key-based comment identification for updates

## Usage

```yaml
name: PR Comment

on:
  pull_request:

jobs:
  comment:
    runs-on: ubuntu-latest
    permissions:
      pull-requests: write
    steps:
      - name: Post sticky comment
        uses: actionutils/sticky-comment@main
        with:
          key: 'build-status'
          body: |
            ## Build Status
            ✅ All checks passed!
```

## Inputs

| Input | Description | Required | Default |
|-------|-------------|----------|---------|
| `key` | Unique key for the sticky comment (used for updating) | Yes | - |
| `body` | Comment body content | Yes | - |
| `number` | PR or Issue number. If not provided, auto-detected from context | No | - |
| `token` | GitHub token | No | `${{ github.token }}` |

## How It Works

This action is a handy wrapper around the [github-comment](https://github.com/suzuki-shunsuke/github-comment) CLI. It simplifies the usage by:

- **Handling template escaping automatically**: You can pass any comment body directly without worrying about Go template syntax. The action properly escapes your content using `AvoidHTMLEscape` so special characters like `{{`, `}}`, and Go template syntax in your markdown won't be interpreted as templates.
- **Providing a simple interface**: Just pass the `key` and `body` - no need to write complex template configurations or update conditions.
- **Managing sticky comments**: Uses the key-based update condition to ensure comments are updated rather than duplicated.

When you post a comment with a specific `key`, the action will:

1. Check if a comment with the same key already exists
2. If it exists, update the existing comment
3. If it doesn't exist, create a new comment

This ensures that repeated runs of the action won't spam the PR/Issue with duplicate comments.

**Note:** The action uses `--pr` flag which works for both pull requests and issues.

## Permissions

The action requires appropriate permissions based on the target:

- For **pull requests**: `pull-requests: write`
- For **issues**: `issues: write`

Make sure to set the correct permissions in your workflow file.

## License

MIT
