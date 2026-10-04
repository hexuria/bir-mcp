---
name: bir-profiles
description: List and open eBIRforms taxpayer profiles through bir-headless, not NativeChat. Use when the user asks for tax profiles, TINs, Forms Set, dues or calendar HTML, or to fill a 1601-C or 2551Q draft. To list profiles call the MCP tool profile_list (daemon verb profile.list on 127.0.0.1:17421). Do not file or submit a return.
compatibility: Requires the gpui-agent CLI on PATH and bir-headless (hello.app bir-desktop) at GPUI_AGENT_ADDR, default 127.0.0.1:17421, with GPUI_AGENT_TOKEN set. A release bir-headless also needs GPUI_AGENT_ALLOW_RELEASE=1.
---

# eBIRforms profiles (bir-headless)

Talk only to **bir-headless**. `hello.app` must be `bir-desktop`. If a call returns another app (often NativeChat), the wrong process is bound to the address. Do not keep guessing invoke names against it.

## List tax profiles

Call the MCP tool **`profile_list`**. It has no arguments.

That tool runs `gpui-agent invoke profile.list` against `127.0.0.1:17421` (override with `GPUI_AGENT_ADDR` only when bir-headless is actually bound there). The daemon verb contains a dot. These names are **not** registered and will fail: `profile_list`, `profiles`, `profile.ensure`.

A successful list item looks like `{tin, name, last4, selected, archived}`.

## Do not file

This plugin has no tool that queues, files, or submits a return. Do not look for `filing.submit`, `filing.queue`, `form.queue`, `form.submit`, or `form.file`. `form_save_draft` writes a draft only. `profile_save` persists the open profile editor only after you pass `confirm: true`, and only after a person agreed to that live-database write.

## Address and token

- Address default: `127.0.0.1:17421`. The server always passes `--addr`. It refuses the call unless `hello.app` is `bir-desktop`.
- `GPUI_AGENT_TOKEN` is required and is not stored in this package. A local smoke daemon often uses `dev-secret`.
- This plugin does not start the daemon. A **release** `bir-headless` exits unless `GPUI_AGENT_ALLOW_RELEASE=1` is set in the daemon's environment (`GPUI_AGENT=1` as well).

Other tools and the exact daemon verbs are in [references/tools.md](references/tools.md).
