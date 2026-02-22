# Build a GitHub Actions-powered Telegram AI Bot

## Project Overview

Build a zero-dependency Telegram bot that runs entirely on GitHub Actions. It uses a cron-triggered workflow that polls Telegram for new messages, processes them with GPT-4o-mini, optionally interacts with the GitHub API, persists memory in a committed JSON file, and replies back to the user on Telegram.

There is NO server, NO docker container, NO npm install, NO external dependencies. Everything runs as a single GitHub Actions workflow with an inline Node.js script using only built-in globals (`fetch`, `fs`, `process`).

---

## Repository Structure

```
.github/
  workflows/
    bot.yml              # The single workflow file — this is the entire bot
memory/
  state.json             # Persisted bot state (Telegram offset + conversation memory)
  .gitkeep
README.md
```

That's it. No `package.json`. No `node_modules`. No source files outside of the workflow.

---

## Secrets Required

The following secrets must be set in the repository settings before the bot works:

| Secret Name | Description |
|---|---|
| `TELEGRAM_BOT_TOKEN` | Bot token from @BotFather on Telegram |
| `OPENAI_API_KEY` | OpenAI API key with access to GPT-4o-mini |

The workflow also uses the built-in `GITHUB_TOKEN` secret (automatically available in all Actions runs) for GitHub API operations.

---

## Workflow File: `.github/workflows/bot.yml`

### Trigger
- `schedule`: every minute using cron `* * * * *`
- `workflow_dispatch`: manual trigger for testing

### Permissions
```yaml
permissions:
  contents: write       # needed to commit memory/state.json
  issues: write         # needed to create GitHub issues
  pull-requests: write  # needed to create and assign PRs
```

### Concurrency
```yaml
concurrency:
  group: telegram-bot
  cancel-in-progress: true
```
This ensures only one run executes at a time. If a new cron fires while one is running, the old one is cancelled.

### Timeout
```yaml
timeout-minutes: 1
```
Hard cap at 1 minute to prevent runaway jobs and save Actions minutes.

### Environment Variables (passed to the inline script)
```yaml
env:
  TELEGRAM_BOT_TOKEN: ${{ secrets.TELEGRAM_BOT_TOKEN }}
  OPENAI_API_KEY: ${{ secrets.OPENAI_API_KEY }}
  GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
  REPO: ${{ github.repository }}
```

---

## Workflow Steps

### Step 1: Checkout
Use `actions/checkout@v4` with no extra options. This checks out the repo so the inline script can read and write `memory/state.json`.

### Step 2: Run bot
Use a single `run` step with `node --input-type=module` reading from a heredoc. The entire bot logic is inline JavaScript inside the workflow YAML. No external files.

```yaml
- name: Run bot
  run: node --input-type=module << 'EOF'
  # ... entire inline JS script here
  EOF
```

### Step 3: Commit state
After the script runs, commit any changes to `memory/state.json` back to the repo:

```yaml
- name: Commit state
  run: |
    git config user.name "github-actions[bot]"
    git config user.email "github-actions[bot]@users.noreply.github.com"
    git pull --rebase origin main
    git add memory/state.json
    git diff --cached --quiet || git commit -m "chore: update bot state [skip ci]"
    git push
```

The `[skip ci]` in the commit message prevents this commit from triggering a new workflow run.

---

## The Inline Node.js Script

This is the heart of the bot. It must be written as ESM (ES Modules) since we use `--input-type=module`. It uses only Node.js built-in globals: `fetch` (Node 18+), `fs/promises`, `process.env`.

### Constants
```js
const TELEGRAM_TOKEN = process.env.TELEGRAM_BOT_TOKEN;
const OPENAI_KEY = process.env.OPENAI_API_KEY;
const GITHUB_TOKEN = process.env.GITHUB_TOKEN;
const REPO = process.env.REPO; // e.g. "owner/repo"
const STATE_FILE = 'memory/state.json';
```

### State Shape
```js
// memory/state.json
{
  "offset": 0,           // last processed Telegram update_id + 1
  "conversations": {
    "<chat_id>": [       // array of last N messages for this chat
      { "role": "user", "content": "..." },
      { "role": "assistant", "content": "..." }
    ]
  }
}
```
Keep only the last **20 messages** per chat to avoid the context growing unbounded.

### Script Flow

#### 1. Load state
Read `memory/state.json`. If it doesn't exist or is malformed, start with `{ offset: 0, conversations: {} }`.

#### 2. Fetch Telegram updates
```
GET https://api.telegram.org/bot{TOKEN}/getUpdates?offset={state.offset}&timeout=0&limit=10
```
- `timeout=0` means do not long-poll — return immediately
- If the response has no updates → exit the script immediately (do not proceed, do not write state)

#### 3. Early exit check
```js
if (!updates || updates.length === 0) process.exit(0);
```
This is the fast path. Most runs will exit here.

#### 4. Process each update
For each update that contains a `message` with `text`:

a. Extract `chat_id`, `message_id`, `text`

b. Update `state.offset` to `update.update_id + 1`

c. Load the conversation history for this `chat_id` from state (or start empty)

d. Append the user's message to the conversation history

e. Call the **intent classifier + response generator** (single GPT-4o-mini call — see below)

f. Based on the returned intent, execute the appropriate action

g. Send the reply back to Telegram

h. Append the assistant reply to conversation history

i. Trim conversation history to last 20 messages

#### 5. Save state
Write the updated state back to `memory/state.json`.

---

## GPT-4o-mini Call Design

This is the most important design decision: **one single OpenAI call per message** that both classifies the intent AND generates the response. Do not make two separate calls.

### API Call
```
POST https://api.openai.com/v1/chat/completions
```

### Model
`gpt-4o-mini`

### System Prompt
```
You are a helpful assistant embedded in a Telegram bot. You have access to a GitHub repository and can help manage it.

When responding, you MUST always reply with a valid JSON object (no markdown, no code blocks, raw JSON only) in this exact shape:

{
  "intent": "<one of: chat | create_issue | create_pr | assign_copilot_issue | assign_copilot_pr | list_issues | list_prs>",
  "reply": "<the message to send back to the user on Telegram>",
  "data": {
    // only include fields relevant to the intent:
    "title": "...",        // for create_issue, create_pr
    "body": "...",         // for create_issue, create_pr
    "issue_number": 123,   // for assign_copilot_issue
    "pr_number": 123,      // for assign_copilot_pr
    "head_branch": "...",  // for create_pr
    "base_branch": "main"  // for create_pr
  }
}

Intent definitions:
- chat: normal conversation, no GitHub action needed
- create_issue: user wants to create a new GitHub issue (optionally assign to Copilot)
- create_pr: user wants to create a new pull request (optionally assign to Copilot)
- assign_copilot_issue: user wants to assign an existing issue to @copilot
- assign_copilot_pr: user wants to assign an existing PR to @copilot for review
- list_issues: user wants to see open issues
- list_prs: user wants to see open pull requests

For create_issue and create_pr, check if the user mentions "copilot", "assign to copilot", or "let copilot handle it" — if so, set data.assign_copilot = true.

The reply field must always contain a friendly human-readable response confirming what you did or answering the question.

The repository context is: REPO_PLACEHOLDER
```

Replace `REPO_PLACEHOLDER` with the actual `REPO` env variable value at runtime.

### Messages sent to OpenAI
Include the full conversation history for this chat (last 20 messages) plus the current user message. This gives GPT-4o-mini full context.

### Parse the response
Parse the JSON from `choices[0].message.content`. If parsing fails for any reason, fall back to treating the whole content as a plain `chat` reply.

---

## GitHub API Actions

All calls use `fetch` with:
```js
headers: {
  'Authorization': `Bearer ${GITHUB_TOKEN}`,
  'Accept': 'application/vnd.github+json',
  'X-GitHub-Api-Version': '2022-11-28',
  'Content-Type': 'application/json'
}
```
Base URL: `https://api.github.com`

### create_issue
```
POST /repos/{REPO}/issues
Body: { title, body, assignees: data.assign_copilot ? ['copilot'] : [] }
```
Include the created issue URL in the Telegram reply.

### create_pr
```
POST /repos/{REPO}/pulls
Body: { title, body, head: data.head_branch, base: data.base_branch || 'main' }
```
Then if `data.assign_copilot`:
```
POST /repos/{REPO}/pulls/{pr_number}/requested_reviewers
Body: { reviewers: ['copilot'] }
```
Include the created PR URL in the Telegram reply.

### assign_copilot_issue
```
PATCH /repos/{REPO}/issues/{data.issue_number}
Body: { assignees: ['copilot'] }
```

### assign_copilot_pr
```
POST /repos/{REPO}/pulls/{data.pr_number}/requested_reviewers
Body: { reviewers: ['copilot'] }
```

### list_issues
```
GET /repos/{REPO}/issues?state=open&per_page=5
```
Format the results as a readable list in the Telegram reply (title + number + URL).

### list_prs
```
GET /repos/{REPO}/pulls?state=open&per_page=5
```
Format the results as a readable list in the Telegram reply (title + number + URL).

---

## Telegram sendMessage

```
POST https://api.telegram.org/bot{TOKEN}/sendMessage
Body: {
  chat_id: <chat_id>,
  text: <reply text>,
  parse_mode: 'Markdown'
}
```

---

## Error Handling

- Wrap the entire script in a `try/catch`
- On any error: log it to console (visible in Actions logs), attempt to send an error message to Telegram if `chat_id` is known, then `process.exit(1)`
- GitHub API errors should be caught per-call and included in the Telegram reply gracefully (e.g. "I couldn't create the issue: 422 Unprocessable Entity")
- Never let an error in one update's processing block the others — process each update in a `try/catch`

---

## memory/state.json Initial Content

```json
{
  "offset": 0,
  "conversations": {}
}
```

---

## README.md

Write a clear README that covers:
1. What this is (one paragraph)
2. Setup instructions:
   - Create a Telegram bot via @BotFather and get the token
   - Get an OpenAI API key
   - Add both as repository secrets (`TELEGRAM_BOT_TOKEN`, `OPENAI_API_KEY`)
   - Enable GitHub Actions on the repo
   - Make sure Copilot is enabled on the repo/org if you want @copilot assignment to work
3. How it works (brief architecture description)
4. Supported commands / example messages the bot understands
5. Cost breakdown (free on public repos, Actions minutes cost on private repos)
6. Limitations (cron delay up to ~1 min, no streaming, memory trimmed to 20 messages per chat)

---

## Constraints & Rules

- **No `npm install`** anywhere in the workflow. Zero.
- **No external Actions** beyond `actions/checkout@v4`.
- **No separate JS/TS source files**. All logic is inline in the workflow YAML.
- **Node.js built-in `fetch` only** (available in Node 18+, which is the default on `ubuntu-latest` runners).
- **ESM syntax** (`import` is not needed since we use no modules — just `fetch`, `fs/promises`, `JSON`, `process`).
- **`fs/promises`** for reading/writing state: `import { readFile, writeFile } from 'fs/promises'` at the top of the inline script.
- The workflow runner must be `ubuntu-latest`.
- The default branch is assumed to be `main`.
- All git operations must handle the case where `memory/state.json` has not changed (use `git diff --cached --quiet ||` guard before commit).
- The `[skip ci]` tag on the commit message is mandatory to prevent infinite loops.

---

## What Success Looks Like

When complete, a user can:
1. Message the bot on Telegram: *"Create an issue called 'Add dark mode' and assign it to copilot"*
2. The next cron run (within 1 minute) picks it up
3. The bot creates the GitHub issue, assigns it to @copilot
4. The bot replies on Telegram: *"✅ Created issue #7: Add dark mode — https://github.com/owner/repo/issues/7 and assigned to @copilot!"*
5. Copilot begins working on the issue autonomously

All of this with zero running infrastructure, zero monthly server cost (on a public repo), and a single ~150-line YAML file.
