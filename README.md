# `action-triage-stale-issue`

This action identifies stale issues and updates them:
- After 14 days without new user comments, a comment is posted summarising the current status, and a <kbd>stale</kbd> label is added.
- After a further 7 days, a comment is posted stating that the issue is being closed, and the issue is closed.

> [!TIP]
> Each invocation of this action performs each of these steps a maximum of once; schedule it sufficiently frequently for the expected number of stale issues.

> [!CAUTION]
> This action is provided for my own use and published in case it is useful to others. If you rely on it, fork and maintain your own copy. No support or stability guarantees are offered.

## Prerequisites

Before using this workflow, ensure:
- The workflow has `issues: write` and `contents: read` permissions (either via the default `GITHUB_TOKEN` or a fine-grained token).
- You have created a [Gemini API key](https://ai.google.dev/gemini-api/docs/api-key) and placed it in a repository secret (e.g. `GEMINI_API_KEY`).
- You understand the [rate limits](https://ai.google.dev/gemini-api/docs/rate-limits) for your chosen model and usage tier.

> [!TIP]
> Google AI Studio Gemini rate limits are per-project. Create multiple projects, each with its own API key, to increase quotas.

## Inputs

Various inputs are defined in the action to configure its operation:

| Name | Description | Default
| --- | --- | ---
| `gemini_api_key`: The Google AI Studio Gemini API key | *required*
| `issue_number` | The GitHub issue to summarise | &nbsp;
| `dry_run` | Disables actions that modify the issue (adding the comments/labels and closing the issue) for testing | `false`

## Usage

Example workflow to check for stale issues every hour:

```yaml
name: AI Stale Issue Triage
permissions:
  issues: write
  contents: read
concurrency:
  group: triage-stale
  cancel-in-progress: false

on:
  schedule:
  - cron: '10 * * * *'
  workflow_dispatch:
    inputs:
      issue_number:
        description: 'Issue number'
        required: false
        type: number
      dry_run:
        description: 'Dry run (do not modify issue)'
        type: boolean
        default: true

jobs:
  stale:
    runs-on: ubuntu-latest

    steps:
    - name: AI stale issue triage
      uses: thoukydides/action-triage-stale-issue@v1
      with:
        gemini_api_key: ${{ secrets.GEMINI_API_KEY }}
        issue_number:   ${{ fromJson(inputs.issue_number) }}
        dry_run:        ${{ inputs.dry_run == true }}
```

> [!TIP]
> Use `workflow_dispatch` to manually trigger the workflow for specific issues, bypassing the normal inactivity periods (e.g. for testing or to make an issue as stale more quickly).

## ISC License (ISC)

<details>
<summary>Copyright © 2026 Alexander Thoukydides</summary>

> Permission to use, copy, modify, and/or distribute this software for any purpose with or without fee is hereby granted, provided that the above copyright notice and this permission notice appear in all copies.
>
> THE SOFTWARE IS PROVIDED "AS IS" AND THE AUTHOR DISCLAIMS ALL WARRANTIES WITH REGARD TO THIS SOFTWARE INCLUDING ALL IMPLIED WARRANTIES OF MERCHANTABILITY AND FITNESS. IN NO EVENT SHALL THE AUTHOR BE LIABLE FOR ANY SPECIAL, DIRECT, INDIRECT, OR CONSEQUENTIAL DAMAGES OR ANY DAMAGES WHATSOEVER RESULTING FROM LOSS OF USE, DATA OR PROFITS, WHETHER IN AN ACTION OF CONTRACT, NEGLIGENCE OR OTHER TORTIOUS ACTION, ARISING OUT OF OR IN CONNECTION WITH THE USE OR PERFORMANCE OF THIS SOFTWARE.
</details>