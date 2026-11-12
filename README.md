# RemixViral for Gemini CLI

Read the ranked, captioned Shorts [RemixViral](https://remixviral.ai) cut from your long videos, and make faceless Shorts from a topic, without leaving Gemini CLI. The extension connects Gemini to the RemixViral MCP server and adds four slash commands and a context file. Nothing that costs credits runs until you have seen the cost and said yes.

## Before you start

- **You need a RemixViral account** (https://remixviral.ai). The extension is a connector, not a free video generator.
- **Clip jobs are started in the app.** Upload a long video you own at https://remixviral.ai/dashboard/clips; the extension then reads the result. There is no link import.
- **Faceless videos cost credits** from your own balance. The cost comes from RemixViral's tool description at the moment you ask; this extension never states a price of its own.
- You need Gemini CLI. A browser must be available once, for sign-in.

## Install

```bash
gemini extensions install https://github.com/airdropfi/remixviral-gemini-extension
```

Restart Gemini CLI, then sign in:

```text
/mcp auth remixviral
```

A browser window opens: sign in to RemixViral and click Allow. Sign-in is OAuth 2.1 with PKCE and dynamic client registration, discovered from the server, so there is no key to paste. Check it with `/mcp` and try `/remixviral:credits`.

Update later with `gemini extensions update remixviral`; remove with `gemini extensions uninstall remixviral`.

### Use an API key instead of OAuth

Prefer a key? Create one at https://remixviral.ai/developers; the tools need the `video:read` scope (`create_video` also needs `video:create`, `get_credit_balance` needs `credits:read`), and a key without a needed scope is refused with a 403. Add the header to the server in `~/.gemini/settings.json` (keep the key out of any repository):

```json
{
  "mcpServers": {
    "remixviral": {
      "httpUrl": "https://remixviral.ai/api/mcp",
      "headers": { "Authorization": "Bearer YOUR_API_KEY" }
    }
  }
}
```

### Antigravity CLI

This repository is also laid out as an Antigravity plugin (`.antigravity-plugin/plugin.json`, `mcp_config.json`, `rules/AGENTS.md`), connecting the same MCP server. We have not run it end to end in Antigravity; slash commands are Gemini CLI only.

## Tools

| Tool | What it does | Charges credits |
|---|---|---|
| `list_clip_jobs` | The clip jobs of your account, newest first. | No |
| `get_clip_job` | One job: status, then the ranked clips with start/end seconds, the reason each was picked, a score and a render status. Optional full transcript. | No |
| `create_video` | Starts a faceless Short from a topic (30, 60 or 90 s). Returns an id; the render runs asynchronously. | **Yes** |
| `get_video` | Status and, when done, the video link. | No |
| `list_videos` | Your videos, newest first, optional status filter. | No |
| `list_niches` | Valid niches, art styles, qualities and durations for `create_video`. | No |
| `get_credit_balance` | Credit balance, monthly allocation and plan. | No |

## Commands

| Command | What it does |
|---|---|
| `/remixviral:clips [job]` | Ranked clips of a clip job with times, reasons and scores. Read-only. |
| `/remixviral:short <topic>` | Faceless Short from a topic: balance and cost first, create only after your yes. |
| `/remixviral:videos [n]` | Your latest videos with status and links. Read-only. |
| `/remixviral:credits` | Your credit balance. Read-only. |

You can also just describe what you want; `GEMINI.md` carries the same rules.

## What it will and will not do

- Reading clip jobs, clips, transcripts, videos, niches and your balance is free and changes nothing.
- `create_video` is the one tool that spends credits. Those credits are real and a started render cannot be undone. The agent is told to check your balance, state the cost and wait for your explicit yes first.
- Starting a clip job is done in the RemixViral app by uploading a video you own; no tool takes a link.
- Nothing here posts, schedules, buys credits or changes your plan.
- Faceless videos use AI-generated images and voice. Switch on the platform's AI-content label when you post.
- No result is promised: nobody can tell you how a clip or video will perform.
- Gemini CLI asks you to confirm MCP tool calls. If you mark the `remixviral` server as trusted in your own settings, that confirmation is skipped, including for `create_video`; the extension does not set `trust` and we recommend you leave it off.

## Contents

```text
gemini-extension.json            manifest: name, version, remote MCP server https://remixviral.ai/api/mcp
.antigravity-plugin/plugin.json  Antigravity plugin manifest
mcp_config.json                  Antigravity MCP server definition (same server)
rules/AGENTS.md                  Antigravity always-on rule (a copy of GEMINI.md: keep both in sync)
GEMINI.md                        context: what RemixViral is and the rules above
commands/remixviral/*.toml       /remixviral:clips, :short, :videos, :credits
assets/icon.png                  icon
scripts/validate.mjs             schema checks, no dependencies: node scripts/validate.mjs
```

## Data and privacy

The MCP server receives what the tools need: the ids, topics and settings you ask your agent to send, and the clip, video and credit data on your RemixViral account. This repository stores nothing and runs no code on your machine; it is a server address, Markdown instructions and a logo. See the [privacy policy](https://remixviral.ai/en/privacy) and [terms](https://remixviral.ai/en/terms). Keys and sign-in tokens are revocable in your RemixViral dashboard under API keys.

## Support

https://remixviral.ai/en/support or support@remixviral.ai. Documentation: https://remixviral.ai/en/developers.

## License

MIT for the files in this repository (see `LICENSE`). The RemixViral service is governed by its terms.
