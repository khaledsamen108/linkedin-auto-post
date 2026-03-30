# LinkedIn Auto Post

Automate daily LinkedIn posts using GitHub Actions cron jobs + LinkedIn API (OAuth 2.0).
Write your posts as Markdown files, set a date, and the workflow publishes them every day at 9 AM UTC.

Compatible with VS Code and Claude Code.

## How It Works

1. Write post content in posts/YYYY-MM-DD.md
2. GitHub Actions cron runs daily at 9:00 AM UTC
3. Python script finds the next unposted scheduled post
4. Publishes it to LinkedIn via OAuth 2.0 (UGC Posts API)
5. Marks the post as published: true and commits back

## Project Structure

    linkedin-auto-post/
    .github/workflows/
        scheduled-post.yml    # Cron job: runs daily at 9 AM UTC
    posts/
        2026-04-01.md         # Your post files (date-named markdown)
    src/
        linkedin_client.py    # LinkedIn API wrapper (OAuth 2.0)
        post_scheduler.py     # Reads posts/ and publishes next one
    requirements.txt
    README.md

## Setup

### 1. Clone the repo

    git clone https://github.com/khaledsamen108/linkedin-auto-post.git
    cd linkedin-auto-post

Open in VS Code:  code .
Open in Claude Code:  claude .

### 2. Create a LinkedIn Developer App

1. Go to https://developer.linkedin.com
2. Create App and request Share on LinkedIn product (w_member_social scope)
3. Copy your Client ID and Client Secret from the Auth tab

### 3. Get OAuth Tokens

Use LinkedIn OAuth 2.0 Authorization Code Flow:

    # Open in browser (replace YOUR_CLIENT_ID):
    https://www.linkedin.com/oauth/v2/authorization?response_type=code&client_id=YOUR_CLIENT_ID&redirect_uri=https://localhost&scope=w_member_social

    # Exchange code for tokens:
    curl -X POST https://www.linkedin.com/oauth/v2/accessToken
      -d "grant_type=authorization_code"
      -d "code=YOUR_CODE"
      -d "client_id=YOUR_CLIENT_ID"
      -d "client_secret=YOUR_CLIENT_SECRET"
      -d "redirect_uri=https://localhost"

### 4. Find Your Person URN

    curl -H "Authorization: Bearer YOUR_ACCESS_TOKEN" https://api.linkedin.com/v2/me
    # URN format: urn:li:person:XXXXXXX

### 5. Add GitHub Secrets

Settings -> Secrets and variables -> Actions -> New repository secret

| Secret Name              | Value                          |
|--------------------------|--------------------------------|
| LINKEDIN_CLIENT_ID       | Your app Client ID             |
| LINKEDIN_CLIENT_SECRET   | Your app Client Secret         |
| LINKEDIN_ACCESS_TOKEN    | OAuth Access Token (60d expiry)|
| LINKEDIN_REFRESH_TOKEN   | OAuth Refresh Token (365d)     |
| LINKEDIN_PERSON_URN      | urn:li:person:XXXXXXX          |

### 6. Write Your First Post

Create posts/2026-04-01.md:

    ---
    scheduled: 2026-04-01
    published: false
    ---
    
    Your LinkedIn post content here.
    
    #AI #Automation #GitHub

### 7. Push and Done!

The workflow runs automatically on schedule.
To test manually: Actions tab -> Post to LinkedIn Daily -> Run workflow

## Customizing the Schedule

Edit .github/workflows/scheduled-post.yml:

    cron: '0 9 * * *'    # Every day at 9am UTC (current)
    cron: '0 9 * * 1'    # Every Monday at 9am UTC
    cron: '0 17 * * *'   # Every day at 5pm UTC

Use https://crontab.guru to build custom schedules.

## VS Code & Claude Code Integration

This repo works directly with:
- VS Code: open with  code .  — full Python IntelliSense support
- Claude Code: open with  claude .  — AI-assisted post writing and workflow editing

## Token Refresh

Access tokens expire every 60 days. The script auto-refreshes using your refresh token.
If the refresh token expires (365 days), re-authorize and update GitHub Secrets.

## License

MIT (c) khaledsamen108# LinkedIn Auto Post 🤖

> Automate daily LinkedIn posts using **GitHub Actions cron jobs** + **LinkedIn API (OAuth 2.0)**. Write your posts as Markdown files, set a date, and let the workflow handle publishing every day at 9 AM UTC.

[![GitHub Actions](https://img.shields.io/badge/GitHub%20Actions-Cron%20Workflow-2088FF?logo=github-actions)](https://github.com/features/actions)
[![Python](https://img.shields.io/badge/Python-3.12-3776AB?logo=python)](https://python.org)
[![LinkedIn API](https://img.shields.io/badge/LinkedIn-UGC%20Posts%20API-0A66C2?logo=linkedin)](https://developer.linkedin.com)
[![VS Code](https://img.shields.io/badge/VS%20Code-Compatible-007ACC?logo=visual-studio-code)](https://code.visualstudio.com)
[![Claude Code](https://img.shields.io/badge/Claude%20Code-Compatible-6B46C1)](https://claude.ai)

---

## How It Works

```
posts/
  2026-04-01.md   ──►  .github/workflows/scheduled-post.yml
  2026-04-02.md        (cron: every day at 9AM UTC)
  2026-04-03.md              │
                             ▼
                    src/post_scheduler.py
                             │
                             ▼
                    src/linkedin_client.py
                             │
                             ▼
                    LinkedIn API (POST /ugcPosts)
```

1. Write your post content in `posts/YYYY-MM-DD.md`
2. GitHub Actions runs daily at 9:00 AM UTC via cron
3. The script finds the next unposted scheduled post
4. Publishes it to your LinkedIn profile via OAuth 2.0
5. Marks the post as `published: true` and commits back

---

## Project Structure

```
linkedin-auto-post/
├── .github/workflows/
│   └── scheduled-post.yml    # Cron job — runs daily at 9 AM UTC
├── posts/
│   └── 2026-04-01.md         # Your post files (date-named markdown)
├── src/
│   ├── linkedin_client.py    # LinkedIn API wrapper (OAuth 2.0)
│   └── post_scheduler.py     # Reads posts/ and publishes next one
├── requirements.txt
└── README.md
```

---

## Quick Setup

### Step 1 — Clone the repo

```bash
git clone https://github.com/khaledsamen108/linkedin-auto-post.git
cd linkedin-auto-post
```

**Open in VS Code:**
```bash
code .
```

**Open in Claude Code:**
```bash
claude .
```

### Step 2 — Create a LinkedIn Developer App

1. Go to [developer.linkedin.com](https://developer.linkedin.com)
2. Click **Create App**
3. Under **Products**, request access to **Share on LinkedIn** (`w_member_social` scope)
4. From the **Auth** tab, copy your **Client ID** and **Client Secret**

### Step 3 — Get OAuth Tokens

Use LinkedIn's OAuth 2.0 Authorization Code Flow:

```bash
# 1. Open this URL in your browser (replace YOUR_CLIENT_ID):
https://www.linkedin.com/oauth/v2/authorization?response_type=code&client_id=YOUR_CLIENT_ID&redirect_uri=https://localhost&scope=w_member_social%20r_liteprofile

# 2. After authorizing, copy the 'code' from the redirect URL

# 3. Exchange it for tokens:
curl -X POST https://www.linkedin.com/oauth/v2/accessToken \
  -d "grant_type=authorization_code" \
  -d "code=YOUR_CODE" \
  -d "client_id=YOUR_CLIENT_ID" \
  -d "client_secret=YOUR_CLIENT_SECRET" \
  -d "redirect_uri=https://localhost"
```

### Step 4 — Find Your LinkedIn Person URN

```bash
curl -H "Authorization: Bearer YOUR_ACCESS_TOKEN" https://api.linkedin.com/v2/me
# Your URN is: urn:li:person:{id}
```

### Step 5 — Add GitHub Secrets

Go to **Settings → Secrets and variables → Actions → New repository secret**:

| Secret Name | Value |
|---|---|
| `LINKEDIN_CLIENT_ID` | Your app's Client ID |
| `LINKEDIN_CLIENT_SECRET` | Your app's Client Secret |
| `LINKEDIN_ACCESS_TOKEN` | OAuth Access Token (expires 60 days) |
| `LINKEDIN_REFRESH_TOKEN` | OAuth Refresh Token (expires 365 days) |
| `LINKEDIN_PERSON_URN` | `urn:li:person:XXXXXXX` |

### Step 6 — Write Your First Post

Create a file in `posts/` named with the date:

```markdown
---
scheduled: 2026-04-01
published: false
---

Your LinkedIn post content here.

Add hashtags at the bottom.
#AI #Automation #GitHub
```

### Step 7 — Push and You're Done!

The workflow runs automatically on schedule. To test manually:
- Go to **Actions** tab → Select **Post to LinkedIn Daily** → Click **Run workflow**

---

## Customizing the Schedule

Edit `.github/workflows/scheduled-post.yml`:

```yaml
on:
  schedule:
    - cron: '0 9 * * *'    # Every day at 9am UTC  (current)
    # - cron: '0 9 * * 1'  # Every Monday at 9am UTC
    # - cron: '0 17 * * *' # Every day at 5pm UTC
```

Use [crontab.guru](https://crontab.guru) to build your custom schedule.

---
