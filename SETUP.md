# Setup Guide — AI Agent Workflows

This guide explains everything you still need to configure manually to make the AI agent workflows fully operational for yourself and other enterprise members.

---

## Section 1 — Repo Access Configuration (Required)

For member repos to call these workflows via `uses:`, this repo must be marked as accessible across the enterprise.

1. Go to **`ItsJT5216/.github-private`** on GitHub
2. Click **Settings** → **Actions** → **General**
3. Scroll to the **Access** section
4. Select: **"Accessible from repositories owned by any user in the [enterprise] enterprise"**
5. Click **Save**

> Without this step, calls to `uses: ItsJT5216/.github-private/.github/workflows/...` from personal repos will fail with a permission error.

---

## Section 2 — Secret: `LITELLM_MASTER_KEY` (Required)

Every repo that calls these workflows needs a secret named `LITELLM_MASTER_KEY`. This is the master key for your LiteLLM proxy at `https://cohort-xero.duckdns.org/litellm/v1`.

You have three options (choose the broadest scope that fits your needs):

### Option A — Enterprise-Level Secret (Recommended)

Makes the secret available to all orgs and members in the enterprise automatically.

1. Go to your **Enterprise Settings**
2. Click **Secrets and variables** → **Actions**
3. Click **New enterprise secret**
4. Name: `LITELLM_MASTER_KEY`
5. Value: your LiteLLM master key
6. Select which organizations can access it (or allow all)
7. Click **Add secret**

### Option B — Org-Level Secret

Makes the secret available to all repos within a specific organization.

1. Go to your **Organization Settings** (`jhayw` or relevant org)
2. Click **Secrets and variables** → **Actions**
3. Click **New organization secret**
4. Name: `LITELLM_MASTER_KEY`
5. Value: your LiteLLM master key
6. Set repository access policy (all repos or selected repos)
7. Click **Add secret**

### Option C — Repo-Level Secret (Per-User Opt-In)

Each member adds the secret to their own personal repo.

1. Go to the member's repo → **Settings** → **Secrets and variables** → **Actions**
2. Click **New repository secret**
3. Name: `LITELLM_MASTER_KEY`
4. Value: the LiteLLM master key (you'll need to share this with each member)
5. Click **Add secret**

> **Note:** `GITHUB_TOKEN` is provided automatically by GitHub Actions — you do not need to configure it.

---

## Section 3 — How Members Opt In (Personal Repos)

Share the file at `.github/workflow-templates/enable-ai-agents.yml` in this repo with your members. Each member should:

1. Create the file `.github/workflows/ai-agents.yml` in their personal repo
2. Copy the contents of `enable-ai-agents.yml` into it
3. Commit and push

That's it. The next time they open a PR, push a commit, or have a failing CI run, the AI agents will activate automatically.

> The agents need `LITELLM_MASTER_KEY` available in the calling repo's secrets (see Section 2). Make sure members have that configured before they opt in.

---

## Section 4 — For Org/Enterprise Repos: Optional Mandatory Enforcement

If you want these workflows to run on **all org repos** automatically (without opt-in), you can enforce them via Rulesets.

1. Go to your **Organization Settings** → **Rulesets** → **New branch ruleset**
2. Set **Enforcement status** to `Active`
3. Under **Target repositories**, select **All repositories** (or specific ones)
4. Under **Target branches**, add `main` (or `~DEFAULT_BRANCH`)
5. Under **Rules**, enable **"Require workflows to pass before merging"**
6. Click **Add workflow** → select this repo (`ItsJT5216/.github-private`) → select `pr-reviewing-agent.yml` → branch `main`
7. Click **Create**

> This is optional for now. The opt-in approach (Section 3) is the recommended starting point. Rulesets are best applied after you've confirmed the agents are working correctly in a few test repos.

---

## Section 5 — Verification Checklist

- [ ] Repo access set to enterprise-accessible (Settings → Actions → General → Access)
- [ ] `LITELLM_MASTER_KEY` secret configured at enterprise or org level
- [ ] Opt-in template (`.github/workflow-templates/enable-ai-agents.yml`) shared with members
- [ ] Test by adding `.github/workflows/ai-agents.yml` to a test repo and opening a PR
