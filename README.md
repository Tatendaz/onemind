# onemind

One personal context store that Claude, ChatGPT and Grok can all read.

Today each assistant keeps its own memory. Ask Grok to write a CV and it does not know how I like CVs done or what my career goals are. Start a task in ChatGPT and move to Claude, and I explain it all again. onemind holds that context once, on a Cloudflare Worker, and serves it to every assistant over MCP.

**Status:** design only. No code yet. Idea and discussion: [Tatendaz/ideas-inbox#23](https://github.com/Tatendaz/ideas-inbox/issues/23).

## How it works

- A Cloudflare Worker serves a remote MCP server at `/mcp`.
- I log in with GitHub through OAuth. Only my GitHub account is allowed.
- Context lives in D1 (Cloudflare's SQLite) as short facts, grouped in packs: `core`, `career`, `writing`, `projects`, and so on.
- A bot loads the small `core` pack when a chat starts and fetches other packs when the topic changes. It can also search.
- A bot can only *propose* a new fact. Nothing is saved until I approve it on the `/review` page.
- Each bot gets its own scopes. Grok can read `career` without reading `money`.
- A weekly cron job flags stale and duplicate facts and backs everything up to R2.

The full design is in [docs/DESIGN.md](docs/DESIGN.md).

## Why not an existing product

- **OpenMemory (mem0):** mem0 sunset it in July 2026 with an open critical bug, CVE-2026-59705: the API has no authentication ([mem0ai/mem0#6080](https://github.com/mem0ai/mem0/issues/6080)).
- **AI Context Flow, Supermemory:** hosted by a third party. I want my own data on infrastructure I control, with my own approval step on writes.

## Cost

The free Workers plan should cover one person. It gives 100,000 requests a day, and I estimate about 2,000 a day at most. That number is a guess, not a measurement. Limits are in [docs/DESIGN.md](docs/DESIGN.md#free-plan-budget).

## License

MIT. See [LICENSE](LICENSE).
