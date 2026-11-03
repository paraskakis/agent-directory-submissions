# Submit an MCP server to the ChatGPT plugin directory

A step-by-step guide, from your hosted MCP server to the **Submit for review** button, using the sample in [`api-lessons/`](api-lessons/).

The sample is complete: it's the real package for API Lessons, a live, read-only MCP server with no sign-in. Copy it, change the fields one by one, zip it and upload it.

*Checked against OpenAI's docs on November 2, 2026: [Upload and submit your plugin](https://developers.openai.com/plugins/deploy/submission), [Build plugins](https://developers.openai.com/plugins/build/plugins), [Plugin guidelines](https://developers.openai.com/plugins/plugin-guidelines).*

## What's in the sample

```
api-lessons/
├── plugin.json      the manifest: listing, review test cases, release notes
├── mcp.json         your MCP server's address
└── assets/
    ├── icon.png     shown in the composer
    └── logo.png     the listing icon
```

This is the portable **Agent Plugins** format: `plugin.json` and `mcp.json` at the root, with everything OpenAI-specific under `extensions.com.openai`. OpenAI's docs: "new packages should use this format."

---

## 0. Before you start

You need all of these before you can submit (you can upload earlier and fix things as you go):

- [ ] **An MCP server on a stable, public HTTPS address**, using Streamable HTTP.
- [ ] **Annotations on every tool:** `readOnlyHint`, `destructiveHint` and `openWorldHint`, each set to `true` or `false`. Justifications are no longer required.
- [ ] **Tool descriptions that start with "Use this when…"** and say what the tool is not for.
- [ ] **No auth, or OAuth.** The portal has no API-key option.
- [ ] **An OpenAI Platform organization you can submit from:** you're an owner, or an owner gave you **Apps Management Write**. The organization has completed individual or business verification. Use a project with global data residency: projects with EU data residency can't submit plugins with MCP servers yet.
- [ ] **A plugin that fits the rules:** suitable for users aged 13–17 (no 18+ plugins yet), and if it sells anything, physical goods only. No subscriptions, digital content, tokens or credits.
- [ ] **Four public HTTPS pages:** website, support, privacy policy, terms of service. They must be reachable and name the same publisher as your submission.
- [ ] **A square icon:** PNG, JPEG, WebP or SVG, at least 48×48 px, at most 5 MiB.
- [ ] **A way to put a file on your MCP server's domain** at `/.well-known/openai-apps-challenge` (for domain verification, step 9).
- [ ] **A video walkthrough** of your test cases, at a URL the reviewers can open.
- [ ] **If your server needs sign-in:** a test account with sample data that works without MFA, email or SMS codes, or magic links.

## 1. Copy the sample

Copy `api-lessons/` and rename the folder after your plugin, for example `acme-notes/`.

## 2. Edit `mcp.json`

```json
{
  "$schema": "https://agent-plugins.org/schemas/1.0.0/mcp.schema.json",
  "mcpServers": {
    "api-lessons": {
      "type": "streamable-http",
      "url": "https://lessons.mcp.level250.com/mcp"
    }
  }
}
```

- Change the server's name (`api-lessons`) and its `url`.
- Keep `"type": "streamable-http"`.
- **No secrets:** no `headers`, no `clientSecret`. The upload rejects them.
- Using OAuth? The auth settings go in this file, under the server's `extensions["com.openai"].auth`. See [Configure MCP authentication](https://developers.openai.com/plugins/build/plugins#configure-mcp-authentication). The client secret is entered in the portal, never in the file.

## 3. Edit `plugin.json`, field by field

Go top to bottom. Limits are for public submission and count every character, spaces included.

**Package identity**

| Field | Change it to | Rule |
|---|---|---|
| `$schema` | Keep as is | |
| `name` | Your plugin's ID, e.g. `acme-notes` | Lowercase letters, numbers, single hyphens; max 64. Stays stable |
| `version` | `1.0.0` for your first submission | Raise it for every new ZIP |
| `description` | One sentence on what your plugin does | Max 4,000 |
| `author` | Your company name, email and website | |
| `homepage` | Your product or docs page | HTTPS |
| `keywords` | A few search terms | Optional |

**Listing:** `extensions.com.openai.interface`

| Field | Change it to | Rule |
|---|---|---|
| `displayName` | Your product's name | Max 30. Not a generic word; don't add "MCP", "MCP Server" or "Plugin" |
| `shortDescription` | The subtitle shown under the name | Max 30. No comparisons, pricing, trials, discounts or promotions |
| `longDescription` | What it does, for whom, and its limits | Max 4,000. Same rules as the subtitle. `\n\n` for paragraphs |
| `developerName` | Your company | Max 80. The directory shows your verified identity's name |
| `category` | One of OpenAI's categories, e.g. Education & Research, Productivity, Developer Tools | Must match a name exactly. The list: [Listing and interface errors](https://developers.openai.com/plugins/deploy/submission-errors#listing-and-interface-errors) (`plugin_category_unknown`) |
| `capabilities` | What users can do with it | Optional; up to 20, max 120 each |
| `websiteURL` | Your product website | HTTPS. Required for review |
| `supportURL` | Your support page | HTTPS. Required for review. An email address isn't enough |
| `privacyPolicyURL` | Your privacy policy | HTTPS. Required for review |
| `termsOfServiceURL` | Your terms of service | HTTPS. Required for review |
| `defaultPrompt` | Up to 3 starter prompts | Max 128 each. Don't @mention your plugin |
| `composerIcon`, `logo` | Keep the paths; replace the files in `assets/` | Square, at least 48×48 |

**Review:** `extensions.com.openai.review`

| Field | Change it to | Rule |
|---|---|---|
| `test_cases.positive` | 5 cases (see step 4) | Exactly 5 to submit |
| `test_cases.negative` | 3 cases (see step 4) | Exactly 3 to submit |
| `demo_recording_url` | Your video walkthrough | Required for review |
| `commerce` | `false`, unless your plugin sells physical goods | Physical goods are the only commerce allowed. If `true`, add `commerce_description` |

**Publication:** `extensions.com.openai.publication`

| Field | Change it to | Rule |
|---|---|---|
| `countries` | `[]` to make it available everywhere | Or a list of country codes, such as `["US", "GB"]`. Leave the field out to keep the current targeting |
| `release_notes` | What this version is or changes | |

Optional extras the sample leaves out: `brandColor`, dark icons (`composerIconDark`, `logoDark`), `screenshots` (no longer shown in the directory: starter prompts replace them) and translations. See the [field reference](https://developers.openai.com/plugins/deploy/submission#automatically-provide-submission-and-review-information).

## 4. Write your test cases

Reviewers run them, so **run every case yourself first**, against your live server.

A **positive** case is a prompt where your plugin should act:

```json
{
  "description": "Find a recording on a topic",
  "prompt": "Is there a recording about agentic payments?",
  "tools_triggered": "get_lessons",
  "expected_behavior": "Calls get_lessons with status On Demand and query \"payments\" and returns ... with their recording links."
}
```

- `tools_triggered`: your real tool names (comma-separated if more than one).
- `expected_behavior`: what the user should see. Name results that will stay true, such as past items, not ones that change daily.

A **negative** case is a prompt where your plugin should *not* act: something nearby but out of scope.

```json
{ "description": "Other instructors' lessons are out of scope", "prompt": "All the lessons on Maven" }
```

The sample's 5 positive cases were run against the live API Lessons server on November 2, 2026.

## 5. Replace the icons

Put your own square images in `assets/` with the same file names (`icon.png`, `logo.png`), or change the paths in `plugin.json`.

## 6. Zip it

From **inside** your folder, so `plugin.json` sits at the root of the ZIP:

```
cd acme-notes
zip -r ../acme-notes.zip . -x '*.DS_Store'
```

The ZIP must not contain `.app.json`, an `apps` field, or `hooks/`. Uploads with those are rejected.

## 7. Upload

1. Open [platform.openai.com/plugins](https://platform.openai.com/plugins) and select **Upload new or existing plugin**.
2. Choose your verified **Developer identity**. The directory shows its name.
3. Select **Upload plugin** and pick your ZIP.
4. After validation, the plugin page opens with your draft.

If validation fails, fix the reported errors and upload again. A successful upload doesn't mean the listing is ready to submit.

## 8. Fix the findings

1. Open **Metadata & Skills** and wait for the checks to finish.
2. Read the **Issues**. Select **Copy issues** and paste them into ChatGPT or Codex together with your `plugin.json` if you want help.
3. Fix your files, zip again, and select **Upload plugin to fix issues**.

## 9. Connect your MCP server

1. Open **MCPs**, select your server and select **Connect**.
2. Check the **MCP Server URL** and **Authentication**.
3. **Domain verification:** the portal shows a token. Serve it as plain text (only the token: no JSON, no list) at the URL it shows: `https://<your MCP host>/.well-known/openai-apps-challenge`. A parent domain can work too.
4. Select **Connect** (and sign in, if your server uses OAuth).
5. Wait for the tool scan, then read its **Issues**. Fix your server and use **Rescan**, or **Reconnect** for connection problems.

## 10. Complete the review details

In **Metadata & Skills**, open **Review information → Review details**:

- Your test cases, video URL and release notes are imported from the ZIP and **read-only** here. To change them, edit `plugin.json` and upload again.
- If your server needs sign-in, enter the reviewer's test account and sign-in steps here, never in the ZIP.
- Select **Save details**.

## 11. Submit for review

Select **Submit for review** and complete the policy attestations. Track progress under **Review status**. Feedback arrives by email.

Only one review can be active per plugin. If you're rejected, fix what the email says and submit a corrected ZIP, or reply to the email to appeal.

## 12. Publish

Approval doesn't publish your plugin. Open the approved version and select **Publish plugin**.

---

## After you're live

- **Changes to existing tools** (fixes, descriptions) go live automatically once they pass OpenAI's daily scan. Select **Rescan** under **MCPs** after you deploy to check sooner. No new ZIP needed.
- **New tools** stay unavailable until OpenAI approves them.
- **Metadata, icons, skills or auth settings:** raise `version`, zip again, and select **Upload plugin to make changes**. Each new version is reviewed.
- **Adding skills later is fine.** Adding an MCP server to a plugin that started with skills only is not supported, so put your server in the first ZIP.
- **A new server URL:** contact OpenAI support. The update flow can't change it.

## Using Plugin Creator instead

OpenAI's Plugin Creator skill can build the folder for you: `$plugin-creator` in Codex, or `@plugin-creator` in ChatGPT's Work mode. The Plugins page also has a **Build a plugin** button. It opens ChatGPT's Work mode with the Plugin Creator plugin and this prompt filled in: *"Help me build a plugin. Start by asking what it should do, then help me package it as a ZIP for upload to OpenAI Platform."* What we saw when testing it:

1. Give it your server's URL: *"create a new plugin in the current directory called acme-notes with just this MCP server: https://example.com/mcp"*.
2. It builds the older Codex layout (`.codex-plugin/plugin.json` + `.mcp.json`). Ask for the portable format: *"This uses the older codex plugin format - shouldn't it be the newer Agent Plugin spec that's portable?"*. It converts to `plugin.json` + `mcp.json`.
3. The converted `plugin.json` has only `$schema`, `name`, `version` and `description`. Paste in the whole `extensions` block from this sample's `plugin.json` and edit it from step 3 on.

Avoid OpenAI's other route for this, giving Plugin Creator a `plugin_asdk_app…` connector ID: it wires the server through `.app.json`, which the upload rejects.

## Gotchas

- **Pick one format.** If the root `plugin.json` has `extensions.com.openai`, OpenAI ignores `.codex-plugin/plugin.json`. The two aren't merged.
- **One MCP server per plugin.** `mcp.json` can list several, but only one can be connected, and you choose which under **MCPs**. Test cases in `plugin.json` only work with exactly one server.
- **Your support, privacy and terms pages must be live** before you submit, and name the same publisher.
- **No secrets anywhere in the ZIP:** no headers, client secrets, test credentials or reviewer instructions.
