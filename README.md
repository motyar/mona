# Mona — GitHub Actions Telegram AI Bot

A zero-dependency Telegram bot that runs entirely on GitHub Actions. It polls Telegram for new messages on a cron schedule, processes them with GPT-4o-mini, interacts with the GitHub API to manage issues and pull requests, and replies back — all from a single workflow file with no server, no Docker, and no `npm install`.

## Setup

1. **Create a Telegram bot** — message [@BotFather](https://t.me/BotFather) on Telegram, use `/newbot`, and save the token.
2. **Get an OpenAI API key** — sign up at [platform.openai.com](https://platform.openai.com) and create an API key (ensure your account has access to GPT-4o-mini).
3. **Add repository secrets** — go to your repo's **Settings → Secrets and variables → Actions** and add:
   | Secret | Value |
   |---|---|
   | `TELEGRAM_BOT_TOKEN` | Your Telegram bot token |
   | `OPENAI_API_KEY` | Your OpenAI API key |
4. **Enable GitHub Actions** — make sure Actions are enabled on the repo (they are by default on public repos).
5. **Enable Copilot** *(optional)* — if you want `@copilot` assignment to work, ensure GitHub Copilot is enabled on the repo or organization.

## How It Works

```
Telegram ──→ GitHub Actions (cron every minute) ──→ GPT-4o-mini ──→ GitHub API
                                                                   ──→ Telegram reply
```

1. A cron job runs every minute and checks Telegram for new messages.
2. If there are messages, each one is sent to GPT-4o-mini along with conversation history.
3. GPT-4o-mini classifies the user's intent and generates a response.
4. If a GitHub action is needed (create issue, create PR, etc.), the bot calls the GitHub API.
5. The bot replies to the user on Telegram with the result.
6. Conversation history and the Telegram update offset are saved to `memory/state.json` and committed back to the repo.

## Supported Commands

You can message the bot in natural language. Here are some examples:

| Message | What happens |
|---|---|
| *"Hello!"* | Normal chat — the bot says hi back |
| *"Create an issue called 'Add dark mode'"* | Creates a GitHub issue |
| *"Create an issue 'Fix login bug' and assign to copilot"* | Creates an issue and assigns @copilot |
| *"List open issues"* | Shows the 5 most recent open issues |
| *"List open PRs"* | Shows the 5 most recent open pull requests |
| *"Assign copilot to issue #7"* | Assigns @copilot to an existing issue |
| *"Request copilot review on PR #3"* | Requests @copilot as a reviewer on a PR |
| *"Create a PR from feature-branch to main"* | Creates a pull request |

## Cost

- **Public repos**: GitHub Actions minutes are **free**, so the bot costs nothing to run (aside from OpenAI API usage which is minimal with GPT-4o-mini).
- **Private repos**: GitHub Actions minutes are metered. The cron runs every minute, but most runs exit immediately (~2 seconds) when there are no new messages. Expect roughly **~40 minutes/day** of billable time in the worst case.

## Limitations

- **Cron delay**: GitHub Actions cron jobs can have up to ~1 minute of delay, so responses are not instant.
- **No streaming**: The bot sends a complete response after processing, not a streaming/typing experience.
- **Memory limit**: Conversation history is trimmed to the last **20 messages per chat** to keep context manageable.
- **Single workflow**: All logic lives in one YAML file — great for simplicity, but not ideal for very complex extensions.
- **Rate limits**: Subject to GitHub API and OpenAI API rate limits.
