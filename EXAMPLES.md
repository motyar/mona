# Examples

A complete collection of examples showing everything Mona can do — from simple chat to advanced multi-step workflows.

---

## Table of Contents

- [Chat](#chat)
- [Create Issues](#create-issues)
- [Create Pull Requests](#create-pull-requests)
- [List Issues](#list-issues)
- [List Pull Requests](#list-pull-requests)
- [Assign Copilot to Issues](#assign-copilot-to-issues)
- [Request Copilot Review on PRs](#request-copilot-review-on-prs)
- [Copilot-Powered Workflows](#copilot-powered-workflows)
- [Multi-Turn Conversations](#multi-turn-conversations)
- [Adding Custom Skills](#adding-custom-skills)

---

## Chat

Mona understands natural language and can hold general conversations.

| You say | Mona replies |
|---|---|
| *"Hello!"* | 👋 Hi there! How can I help you with your repository today? |
| *"What can you do?"* | I can create issues, open pull requests, list open issues and PRs, assign Copilot to work on issues, and more! |
| *"Thanks for the help"* | You're welcome! Let me know if you need anything else. |
| *"Tell me about this repo"* | This is the repository `owner/repo`. I can help you manage issues and pull requests here. |
| *"Who are you?"* | I'm Mona, a GitHub bot that lives in GitHub Actions. I can help you manage this repository from Telegram! |

---

## Create Issues

Create GitHub issues using natural language. Mona extracts the title, body, and assignees automatically.

### Basic — title only

> **You:** Create an issue called "Add dark mode"
>
> **Mona:** ✅ Created issue #12: Add dark mode — https://github.com/owner/repo/issues/12

### With a description

> **You:** Create an issue titled "Improve onboarding flow" with the description "The current sign-up process has too many steps. Simplify it to 3 steps max."
>
> **Mona:** ✅ Created issue #13: Improve onboarding flow — https://github.com/owner/repo/issues/13

### Bug report style

> **You:** Open an issue: "Login button unresponsive on mobile". Users on iOS Safari report that the login button does not respond to taps.
>
> **Mona:** ✅ Created issue #14: Login button unresponsive on mobile — https://github.com/owner/repo/issues/14

### Feature request style

> **You:** File an issue for adding webhook support so external services can subscribe to repository events
>
> **Mona:** ✅ Created issue #15: Add webhook support — https://github.com/owner/repo/issues/15

### With Copilot assignment

> **You:** Create an issue "Fix typo in README" and assign it to copilot
>
> **Mona:** ✅ Created issue #16: Fix typo in README — https://github.com/owner/repo/issues/16 and assigned to @copilot!

### Let Copilot handle it

> **You:** I need an issue for adding input validation to the signup form. Let copilot handle it.
>
> **Mona:** ✅ Created issue #17: Add input validation to signup form — https://github.com/owner/repo/issues/17 and assigned to @copilot!

---

## Create Pull Requests

Open pull requests by specifying the source and target branches.

### Basic PR

> **You:** Create a PR from feature-login to main
>
> **Mona:** ✅ Created PR #5: feature-login — https://github.com/owner/repo/pull/5

### PR with a title

> **You:** Open a pull request from fix/header-bug to main titled "Fix header alignment on mobile"
>
> **Mona:** ✅ Created PR #6: Fix header alignment on mobile — https://github.com/owner/repo/pull/6

### PR with title and description

> **You:** Create a PR from feature/dark-mode to develop with title "Add dark mode support" and body "Implements dark mode toggle in the settings page. Includes CSS variables for theme switching."
>
> **Mona:** ✅ Created PR #7: Add dark mode support — https://github.com/owner/repo/pull/7

### PR with Copilot review

> **You:** Open a PR from refactor/api-layer to main and have copilot review it
>
> **Mona:** ✅ Created PR #8: refactor/api-layer — https://github.com/owner/repo/pull/8 and requested review from @copilot!

### PR to a non-main branch

> **You:** Create a pull request from feature/notifications to develop
>
> **Mona:** ✅ Created PR #9: feature/notifications — https://github.com/owner/repo/pull/9

---

## List Issues

View the most recent open issues in the repository.

### Basic listing

> **You:** List open issues
>
> **Mona:**
> • #14 Login button unresponsive on mobile
>   https://github.com/owner/repo/issues/14
> • #13 Improve onboarding flow
>   https://github.com/owner/repo/issues/13
> • #12 Add dark mode
>   https://github.com/owner/repo/issues/12
> • #10 Update dependencies
>   https://github.com/owner/repo/issues/10
> • #9 Add unit tests for auth module
>   https://github.com/owner/repo/issues/9

### Natural variations

All of these work the same way:

- *"Show me open issues"*
- *"What issues are open?"*
- *"Any open issues?"*
- *"List issues"*
- *"What needs to be done?"*

### When there are no issues

> **You:** List open issues
>
> **Mona:** No open issues found.

---

## List Pull Requests

View the most recent open pull requests.

### Basic listing

> **You:** List open PRs
>
> **Mona:**
> • #8 refactor/api-layer
>   https://github.com/owner/repo/pull/8
> • #7 Add dark mode support
>   https://github.com/owner/repo/pull/7
> • #6 Fix header alignment on mobile
>   https://github.com/owner/repo/pull/6

### Natural variations

All of these work the same way:

- *"Show me open pull requests"*
- *"Any PRs open?"*
- *"What pull requests are pending?"*
- *"List PRs"*

### When there are no PRs

> **You:** List open PRs
>
> **Mona:** No open pull requests found.

---

## Assign Copilot to Issues

Assign @copilot to an existing issue so it can start working on it autonomously.

### By issue number

> **You:** Assign copilot to issue #7
>
> **Mona:** ✅ Assigned @copilot to issue #7

### Natural variations

- *"Let copilot work on issue #7"*
- *"Have copilot handle issue #12"*
- *"Put copilot on issue #3"*
- *"Assign issue #5 to copilot"*

---

## Request Copilot Review on PRs

Request @copilot as a reviewer on an existing pull request.

### By PR number

> **You:** Request copilot review on PR #3
>
> **Mona:** ✅ Requested review from @copilot on PR #3

### Natural variations

- *"Have copilot review PR #3"*
- *"Ask copilot to review pull request #5"*
- *"Add copilot as a reviewer on PR #8"*
- *"Get copilot's review on PR #10"*

---

## Copilot-Powered Workflows

Combine multiple capabilities to create end-to-end workflows powered by Copilot.

### Create and delegate to Copilot

> **You:** Create an issue "Add rate limiting to the API endpoints" and assign to copilot
>
> **Mona:** ✅ Created issue #20: Add rate limiting to the API endpoints — https://github.com/owner/repo/issues/20 and assigned to @copilot!

Copilot will now autonomously start working on the issue, create a branch, write code, and open a pull request.

### Create a PR and get Copilot's review

> **You:** Create a PR from feature/caching to main titled "Add Redis caching layer" and let copilot review it
>
> **Mona:** ✅ Created PR #11: Add Redis caching layer — https://github.com/owner/repo/pull/11 and requested review from @copilot!

Copilot will review the PR and leave feedback on the code.

### Full issue-to-review cycle

This shows how a complete workflow might look across multiple messages:

> **You:** Create an issue "Refactor database queries for better performance" and let copilot handle it
>
> **Mona:** ✅ Created issue #21: Refactor database queries for better performance — https://github.com/owner/repo/issues/21 and assigned to @copilot!

*…Copilot works on the issue and opens a PR…*

> **You:** List open PRs
>
> **Mona:**
> • #12 Refactor database queries for better performance
>   https://github.com/owner/repo/pull/12

> **You:** Request copilot review on PR #12
>
> **Mona:** ✅ Requested review from @copilot on PR #12

---

## Multi-Turn Conversations

Mona remembers the last 20 messages per chat, so you can have natural follow-up conversations.

### Follow-up questions

> **You:** What issues are open?
>
> **Mona:** Here are the open issues: …
>
> **You:** Assign copilot to the first one
>
> **Mona:** ✅ Assigned @copilot to issue #14

### Iterative issue creation

> **You:** Create an issue called "Redesign landing page"
>
> **Mona:** ✅ Created issue #22: Redesign landing page — https://github.com/owner/repo/issues/22
>
> **You:** Now create another one for "Update footer links"
>
> **Mona:** ✅ Created issue #23: Update footer links — https://github.com/owner/repo/issues/23
>
> **You:** Assign copilot to both of those
>
> **Mona:** ✅ Assigned @copilot to issue #22
> ✅ Assigned @copilot to issue #23

### Context-aware responses

> **You:** Hi! I'm working on the authentication module today.
>
> **Mona:** Hello! Sounds great — let me know if you need any issues created or PRs opened for the authentication module.
>
> **You:** Actually yes, create an issue for adding two-factor authentication
>
> **Mona:** ✅ Created issue #24: Add two-factor authentication — https://github.com/owner/repo/issues/24

---

## Adding Custom Skills

Mona's skill registry makes it easy to add new capabilities. Here are examples of custom skills you can add to the `SKILLS` array in `.github/workflows/bot.yml`.

### Close an issue

```js
{
  intent: 'close_issue',
  description: 'Close an existing GitHub issue.',
  dataFields: {
    issue_number: 'number - the issue number to close'
  },
  execute: async (data, context) => {
    await context.github('PATCH', `/repos/${context.env.REPO}/issues/${data.issue_number}`, {
      state: 'closed'
    });
    return `✅ Closed issue #${data.issue_number}`;
  }
}
```

> **You:** Close issue #7
>
> **Mona:** ✅ Closed issue #7

### Add a label to an issue

```js
{
  intent: 'label_issue',
  description: 'Add a label to an existing GitHub issue.',
  dataFields: {
    issue_number: 'number - the issue number',
    label: 'string - the label name to add'
  },
  execute: async (data, context) => {
    await context.github('POST', `/repos/${context.env.REPO}/issues/${data.issue_number}/labels`, {
      labels: [data.label]
    });
    return `✅ Added label "${data.label}" to issue #${data.issue_number}`;
  }
}
```

> **You:** Add the label "bug" to issue #14
>
> **Mona:** ✅ Added label "bug" to issue #14

### Comment on an issue

```js
{
  intent: 'comment_on_issue',
  description: 'Add a comment to an existing GitHub issue.',
  dataFields: {
    issue_number: 'number - the issue number',
    comment: 'string - the comment text'
  },
  execute: async (data, context) => {
    await context.github('POST', `/repos/${context.env.REPO}/issues/${data.issue_number}/comments`, {
      body: data.comment
    });
    return `✅ Commented on issue #${data.issue_number}`;
  }
}
```

> **You:** Comment on issue #5 saying "This is now a priority for next sprint"
>
> **Mona:** ✅ Commented on issue #5

### Lock an issue

```js
{
  intent: 'lock_issue',
  description: 'Lock an issue to prevent further comments.',
  dataFields: {
    issue_number: 'number - the issue number to lock',
    reason: 'string - reason for locking (off-topic, too heated, resolved, spam)'
  },
  execute: async (data, context) => {
    await context.github('PUT', `/repos/${context.env.REPO}/issues/${data.issue_number}/lock`, {
      lock_reason: data.reason || 'resolved'
    });
    return `🔒 Locked issue #${data.issue_number}`;
  }
}
```

> **You:** Lock issue #3, it's been resolved
>
> **Mona:** 🔒 Locked issue #3

### Merge a pull request

```js
{
  intent: 'merge_pr',
  description: 'Merge an open pull request.',
  dataFields: {
    pr_number: 'number - the pull request number to merge',
    merge_method: 'string - merge method: merge, squash, or rebase (default: merge)'
  },
  execute: async (data, context) => {
    await context.github('PUT', `/repos/${context.env.REPO}/pulls/${data.pr_number}/merge`, {
      merge_method: data.merge_method || 'merge'
    });
    return `✅ Merged PR #${data.pr_number}`;
  }
}
```

> **You:** Squash and merge PR #6
>
> **Mona:** ✅ Merged PR #6

### Create a release

```js
{
  intent: 'create_release',
  description: 'Create a new GitHub release from a tag.',
  dataFields: {
    tag: 'string - the tag name for the release (e.g. v1.0.0)',
    name: 'string - the release title',
    body: 'string - release notes describing what changed'
  },
  execute: async (data, context) => {
    const release = await context.github('POST', `/repos/${context.env.REPO}/releases`, {
      tag_name: data.tag,
      name: data.name || data.tag,
      body: data.body || ''
    });
    return `🚀 Created release ${release.name} — ${release.html_url}`;
  }
}
```

> **You:** Create a release v2.0.0 called "Version 2.0" with notes "Major redesign with dark mode and new API"
>
> **Mona:** 🚀 Created release Version 2.0 — https://github.com/owner/repo/releases/tag/v2.0.0

### List repository contributors

```js
{
  intent: 'list_contributors',
  description: 'List the top contributors to the repository.',
  dataFields: null,
  execute: async (data, context) => {
    const contributors = await context.github('GET', `/repos/${context.env.REPO}/contributors?per_page=10`);
    if (contributors.length === 0) return 'No contributors found.';
    return contributors.map((c, i) => `${i + 1}. @${c.login} — ${c.contributions} contributions`).join('\n');
  }
}
```

> **You:** Who are the top contributors?
>
> **Mona:**
> 1. @alice — 142 contributions
> 2. @bob — 87 contributions
> 3. @charlie — 53 contributions

### Create a branch

```js
{
  intent: 'create_branch',
  description: 'Create a new branch from the default branch.',
  dataFields: {
    branch_name: 'string - the name for the new branch'
  },
  execute: async (data, context) => {
    const ref = await context.github('GET', `/repos/${context.env.REPO}/git/ref/heads/main`);
    await context.github('POST', `/repos/${context.env.REPO}/git/refs`, {
      ref: `refs/heads/${data.branch_name}`,
      sha: ref.object.sha
    });
    return `✅ Created branch \`${data.branch_name}\` from main`;
  }
}
```

> **You:** Create a branch called feature/notifications
>
> **Mona:** ✅ Created branch `feature/notifications` from main
