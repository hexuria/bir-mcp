# MCP tools

Each tool name is the MCP name. The daemon verb is what `gpui-agent invoke` sends. Verbs were checked against `bir-desktop` `AgentHost::invoke` (do not invent others).

| MCP tool | Daemon verb | Notes |
| --- | --- | --- |
| `profile_list` | `profile.list` | Every listed profile: tin, name, last4, selected, archived. **Use this to list profiles.** |
| `profile_search` | `profile.search` | `q` (TIN substring or name). Empty `q` lists all. Does not create. |
| `profile_set` | `profile.set` | `tin` or `q`. Optional `view`: `dashboard` (default) or `profile-manager`. |
| `profile_html` | `profile.html` | Read-only identity card. Optional `tin` / `q` / `year`. |
| `profile_forms_set_get` | `profile.forms_set.get` | Read one year's Forms Set. `year` required. |
| `profile_forms_set` | `profile.forms_set` | Replaces that year's Forms Set. `confirm` must be JSON boolean `true`. |
| `profile_create` | `profile.create` | Opens the editor. Does not save. |
| `profile_save` | `profile.save` | Persists the editor. Tool-local `confirm: true` is required and is not sent to the host. Ask a person first on a live taxpayer DB. |
| `dues_html` | `dues.html` | Demo HTML for dues. Optional filter, scope, limit, tin, q. |
| `calendar_html` | `calendar.html` | Host alias of `dues.html`. Same bundle. |
| `nav_go` | `nav.go` | `page` slug (`profile-manager`, `dashboard`, `global-dashboard`, `settings`, `form-1601c`, …). |
| `filing_start` | `filing.start` | Opens a form. `code`, `year`, `period`. Does not file. |
| `form_fill` | `form.fill` | Fillable keys only. Does not queue or file. |
| `filing_validate` | `filing.validate` | Validator for the open 1601-C or 2551Q. |
| `form_save_draft` | `form.save_draft` | Draft only. |
| `form_pdf` | `form.pdf` | Frozen HTML path. Does not validate or file. |

Not exposed, on purpose: `filing.submit`, `filing.queue`, `form.queue`, `form.submit`, `form.file`, `filing.file`, `form.submit_external`, `profile.ensure`.

`form.fill` keys the host documents: 1601-C `tax_14`, `tax_25`, `sheets`, `any_taxes_withheld`, `category_of_agent`. 2551Q `creditable_tax_withheld`, `other_tax_credit`, `taxable_amount`. Unknown keys are refused by the host.
