# TODO NUKEM - PR Ticket Link Action

Automatically adds ticket links to Pull Request descriptions based on branch names.

## Quick Setup

Create `.github/workflows/todo-nukem-pr-ticket-link.yml` in your project:

```yaml
name: TODO NUKEM - Add Ticket Link to PR

on:
  pull_request:
    types: [opened]

jobs:
  add-ticket-link:
    runs-on: ubuntu-latest
    permissions:
      pull-requests: write
      contents: read
    steps:
      - name: Checkout
        uses: actions/checkout@v4

      - name: Add ticket link to PR
        uses: jolution/todo-nukem-pr-ticket-link@v1
        with:
          github-token: ${{ secrets.GITHUB_TOKEN }}
```

## Configuration

Add `ticketBaseUrl` to your `package.json`:

```json
{
  "todonukem": {
    "ticketBaseUrl": "https://jira.example.com/browse/"
  }
}
```

Or create a `.todonukem.json` file (overrides `package.json`):

```json
{
  "ticketBaseUrl": "https://your-company.atlassian.net/browse/"
}
```

### Optional: Ticket Prefix

Add a `ticketPrefix` to display a system-specific prefix in the ticket reference (prefix is only used for display, not in the URL):

```json
{
  "ticketBaseUrl": "https://dev.azure.com/myorg/myproject/_workitems/edit/",
  "ticketPrefix": "AB#"
}
```

### Example Configs

**Jira** (`.todonukem.json`):

```json
{
  "ticketBaseUrl": "https://url-to-jira/browse/",
  "ticketPrefix": "PROJ-"
}
```

**Azure DevOps** (`.todonukem.json`):

```json
{
  "ticketBaseUrl": "https://dev.azure.com/myorg/myproject/_workitems/edit/",
  "ticketPrefix": "AB#"
}
```

### GitHub Autolink References (alternative)

Instead of `linkStyle: markdown`, use `linkStyle: plaintext` and let GitHub link the reference. Configure under _Settings → Code and automation → Autolink references → Add autolink reference_.

```yaml
- name: Add ticket link to PR
  uses: jolution/todo-nukem-pr-ticket-link@v1
  with:
    github-token: ${{ secrets.GITHUB_TOKEN }}
    linkStyle: plaintext
```

**Jira:**

- Reference prefix: `PROJ-`
- Target URL: `https://url-to-jira/browse/PROJ-<num>`
- Format: Numeric

**Azure DevOps:**

- Reference prefix: `AB#`
- Target URL: `https://dev.azure.com/myorg/myproject/_workitems/edit/<num>`
- Format: Numeric

## Branch Naming

Your branch names must contain a ticket ID:

- ✅ `feature/PROJ-123`
- ✅ `fix/TICKET-456`
- ✅ `PROJ-789-refactor`
- ❌ `feature/my-feature` (no ticket ID)

## Result

Appends a footer to the PR description. The exact format depends on `linkStyle`:

**`markdown` (default)**

```markdown
---

<!-- TODO NUKEM PR Ticket Link -->

[ 🎫 [PROJ-123](https://jira.example.com/browse/PROJ-123) ]

_via [TODO NUKEM](https://github.com/jolution/todo-nukem)_
```

**`plaintext`** (e.g. for Azure DevOps auto-linking)

```markdown
---

<!-- TODO NUKEM PR Ticket Link -->

[ 🎫 AB#123 ]

_via [TODO NUKEM](https://github.com/jolution/todo-nukem)_
```

The HTML comment acts as a marker to prevent duplicate entries on subsequent runs.

## Inputs

| Name            | Description                                                                                                                     | Required | Default               |
| --------------- | ------------------------------------------------------------------------------------------------------------------------------- | -------- | --------------------- |
| `github-token`  | GitHub token for API access                                                                                                     | Yes      | `${{ github.token }}` |
| `hidePromotion` | Hide the 'via TODO NUKEM' promotion link                                                                                        | No       | `false`               |
| `linkStyle`     | Format of the ticket reference: `markdown` (Markdown hyperlink) or `plaintext` (plain text, e.g. for Azure DevOps auto-linking) | No       | `markdown`            |

## Related

- [TODO NUKEM](https://github.com/jolution/todo-nukem) - Main project
