# bir-mcp

Agent Plugin ([Agent Plugins 1.0.0](https://agent-plugins.org/specification)) that drives **eBIRforms bir-headless** with named MCP tools. It wraps the installed `gpui-agent` CLI (`invoke`). It does not reimplement the HMAC v2 handshake, and it does not file or submit a BIR return.

Agents fail when they use generic `gpui-agent mcp`: that server exposes one `invoke` tool, so models attach to NativeChat or guess unregistered names (`profile_list`, `profiles`). This package only talks to a host whose `hello.app` is `bir-desktop`, and every tool already knows the real verb.

## List tax profiles

Call the MCP tool **`profile_list`**.

That runs:

```bash
gpui-agent --addr 127.0.0.1:17421 --token "$GPUI_AGENT_TOKEN" invoke profile.list
```

The daemon verb is `profile.list` (with a dot). `profile_list` is only the MCP tool name. A result row is `{tin, name, last4, selected, archived}`.

## Point it at bir-headless

| Variable | Role |
| --- | --- |
| `GPUI_AGENT_ADDR` | Default **`127.0.0.1:17421`** when unset. The server always passes `--addr`. Set this only if bir-headless is bound somewhere else. |
| `GPUI_AGENT_TOKEN` | **Required.** Not stored in this repo. A local smoke daemon often uses `dev-secret`. |
| `GPUI_AGENT_BIN` | Optional path to `gpui-agent` when it is not on `PATH`. |

Before every invoke the server runs `hello` and **refuses** the call unless `hello.app` is `bir-desktop`. A NativeChat (or any other) process on that port is an error, not a fallback.

`mcp.json` launches `./bin/bir-mcp` over stdio. Do not put the token in `mcp.json`. Put `GPUI_AGENT_TOKEN` in the environment of the client that starts the plugin.

This plugin does not start the daemon. Quit any other process on port 17421 first (painted `bir` and NativeChat included). A **release** `bir-headless` refuses to start the control plane unless its environment has `GPUI_AGENT_ALLOW_RELEASE=1` (and `GPUI_AGENT=1`). Debug builds need `GPUI_AGENT=1` and a token to bind. See buwiz-forms `crates/bir-desktop/docs/AGENT.md`.

```bash
export GPUI_AGENT=1
export GPUI_AGENT_TOKEN=dev-secret
export GPUI_AGENT_ADDR=127.0.0.1:17421
# release binary only:
# export GPUI_AGENT_ALLOW_RELEASE=1
bir-headless serve
```

## Tools

Sixteen tools, one daemon verb each, taken from `AgentHost::invoke` in `bir-desktop`. The skill `bir-profiles` tells an agent the same list. Detail: `skills/bir-profiles/references/tools.md`.

| MCP tool | Daemon verb |
| --- | --- |
| `profile_list` | `profile.list` |
| `profile_search` | `profile.search` |
| `profile_set` | `profile.set` |
| `profile_html` | `profile.html` |
| `profile_forms_set_get` | `profile.forms_set.get` |
| `profile_forms_set` | `profile.forms_set` (`confirm` JSON boolean `true`) |
| `profile_create` | `profile.create` (opens the editor, does not save) |
| `profile_save` | `profile.save` (tool requires `confirm: true` after a person agrees; that flag is not sent to the host) |
| `dues_html` | `dues.html` |
| `calendar_html` | `calendar.html` (host alias of `dues.html`) |
| `nav_go` | `nav.go` |
| `filing_start` | `filing.start` |
| `form_fill` | `form.fill` |
| `filing_validate` | `filing.validate` |
| `form_save_draft` | `form.save_draft` |
| `form_pdf` | `form.pdf` |

**Not tools:** `filing.submit`, `filing.queue`, `form.queue`, `form.submit`, `form.file`, `filing.file`, `form.submit_external`, `profile.ensure`. Nothing here queues a return or PUTs it to BIR. `form_save_draft` is a draft only.

## Layout

Follows the [portable package](https://agent-plugins.org/specification) (spec 1.0.0):

```text
bir-mcp/
├── plugin.json
├── mcp.json
├── bin/bir-mcp          # stdio MCP server
└── skills/bir-profiles/SKILL.md
```

`plugin.json` `$schema` is `https://agent-plugins.org/schemas/1.0.0/plugin.schema.json`. `mcp.json` uses `https://agent-plugins.org/schemas/1.0.0/mcp.schema.json` and one stdio server. The skill follows the [Agent Skills](https://agentskills.io/specification) frontmatter (`name` matches the directory).

## Check

```bash
BIR_MCP_SELFTEST=1 ./bin/bir-mcp
```

Needs Python 3 and `gpui-agent` on `PATH` for live calls. The self-test does not need a daemon.
