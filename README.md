# Branch Naming Enforcer

A GitHub Action that validates branch names match your team's naming conventions before merging.

Consistent branch naming makes it easier to understand what a branch is for at a glance, enables automation (like auto-linking to tickets), and keeps your repository tidy.

## What it does

When a pull request is opened or updated, this action:

1. Reads the source branch name
2. Matches it against your configured naming pattern (regex)
3. Posts a **check run** on the PR (pass/fail) and optionally a **comment**

You configure the naming pattern and feedback mode (blocking or advisory) in your [Flux](https://www.positronlabs.com) dashboard.

## Usage

```yaml
name: Branch Naming
on:
  pull_request:
    types: [opened, synchronize, reopened]

jobs:
  branch-naming:
    runs-on: ubuntu-latest
    steps:
      - uses: positronlabs/flux-branch-naming-enforcer@v1
        with:
          flux-token: ${{ secrets.FLUX_TOKEN }}
          flux-url: ${{ vars.FLUX_URL }}
```

No checkout step needed — this action only reads the branch name from the pull request event.

## Setup

1. Sign up at [positronlabs.com](https://www.positronlabs.com) and create an organisation
2. Go to **Org Admin > Tools** and create an API key
3. Go to **Product > Tools** and configure Branch Naming Enforcer with trigger app "CLI"
4. Set the naming pattern (e.g. `^(feat|fix|chore)/`) and feedback mode
5. Add `FLUX_TOKEN` as a repository secret and `FLUX_URL` as a repository variable
6. Add the workflow above to your repository

## Inputs

| Input | Required | Description |
|-------|----------|-------------|
| `flux-token` | Yes | API key from Flux (Org Admin > Tools > API Keys) |
| `flux-url` | Yes | URL of your Flux instance |

## Behaviour

| Branch name | Blocking mode | Advisory mode |
|-------------|--------------|---------------|
| Matches pattern | Check run: pass | Check run: pass |
| Doesn't match | Check run: fail, CI blocked | CI passes, warning comment posted |

## Examples

| Pattern | Matches | Doesn't match |
|---------|---------|---------------|
| `^(feat\|fix\|chore)/` | `feat/add-login`, `fix/typo` | `my-feature`, `update` |
| `^[A-Z]+-\d+` | `PROJ-123-add-login` | `add-login`, `feat/thing` |
