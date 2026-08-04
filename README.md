# Sweeppea API v3 — Documentation

> **Base URL:** `https://api-v3.sweeppea.com`
> **Version:** 3.0.0
> **Format:** Markdown (GitHub-friendly)

Powerful REST API for managing sweepstakes, participants, winners, and promotional campaigns.

---

## Authentication

All requests must include your API key in the `Authorization` header:

```
Authorization: Bearer YOUR_API_KEY
```

Your account must be active (`Status: true`) to use the API. Invalid or missing tokens return `401` or `403`.

---

## Response Format

All endpoints return a consistent JSON structure:

**Success:**
```json
{
  "Response": true,
  "Data": { },
  "Message": "..."
}
```

**Error:**
```json
{
  "Response": false,
  "Message": "...",
  "Code": 400
}
```

---

## MCP Server

Sweeppea Renaissance includes a full **Model Context Protocol (MCP) Server** implementation, allowing you to integrate the entire platform with any LLM or AI-powered application. Connect your AI assistants, chatbots, and autonomous agents to manage sweepstakes, participants, winners, and more — all through natural language.

- Quick Start: https://mcpdocs.sweeppea.com/#quick-start
- Tools Reference: https://mcpdocs.sweeppea.com/#tools
- Examples: https://mcpdocs.sweeppea.com/#examples

---

## Command Line Interface (CLI)

The official **Sweeppea CLI** puts this same API in your terminal — a single native binary (written in Rust), distributed on npm, with no runtime to install. Every command supports `--json` for machine-readable output, and destructive actions confirm first and honor `--yes`.

```bash
npx @sweeppea/cli auth login
```

- Install: https://clidocs.sweeppea.com/#install
- Authentication: https://clidocs.sweeppea.com/#auth
- Commands: https://clidocs.sweeppea.com/#commands
- npm package: https://www.npmjs.com/package/@sweeppea/cli

---

## The Sweeppea Developer Ecosystem

Three clients, one platform, one API key — all of them talk to this same REST API v3.

| | For | Docs |
|---|---|---|
| **REST API v3** | Direct HTTP integration (OpenAPI spec included) | https://apidocs.sweeppea.com/ |
| **MCP Server** | AI assistants (Claude, Cursor, Windsurf, Gemini CLI…) | https://mcpdocs.sweeppea.com/ |
| **CLI** | Your shell and CI — `npx @sweeppea/cli` | https://clidocs.sweeppea.com/ |

---

## Endpoints

### Account
| Endpoint | Method | Description |
|----------|--------|-------------|
| [account/profile](account/profile.md) | POST | Fetch user profile info |
| [account/update-profile](account/update-profile.md) | POST | Update user profile |
| [account/business](account/business.md) | POST | Fetch business info |
| [account/plan](account/plan.md) | POST | Fetch current plan |
| [account/change-password](account/change-password.md) | POST | Change account password |
| [account/retrieve-password](account/retrieve-password.md) | POST | Retrieve/reset password |
| [account/health-check](account/health-check.md) | POST | API health check |

### Billing
| Endpoint | Method | Description |
|----------|--------|-------------|
| [billing/transactions](billing/transactions.md) | POST | Fetch billing transactions |
| [billing/consumptions](billing/consumptions.md) | POST | Fetch billing consumptions |
| [billing/transfer](billing/transfer.md) | POST | Transfer billing credits |
| [billing/datatransfer](billing/datatransfer.md) | POST | Fetch data transfer info |

### Calendar
| Endpoint | Method | Description |
|----------|--------|-------------|
| [calendar/create](calendar/create.md) | POST | Create a calendar event |
| [calendar/fetch](calendar/fetch.md) | POST | Fetch all calendar events |
| [calendar/single](calendar/single.md) | POST | Fetch a single calendar event |
| [calendar/update](calendar/update.md) | POST | Update a calendar event |
| [calendar/delete](calendar/delete.md) | POST | Delete a calendar event |

### Entry Page
| Endpoint | Method | Description |
|----------|--------|-------------|
| [entrypage/fetch](entrypage/fetch.md) | POST | Fetch entry page config |
| [entrypage/fields](entrypage/fields.md) | POST | Fetch entry page fields |
| [entrypage/settings](entrypage/settings.md) | POST | Fetch entry page settings |
| [entrypage/update](entrypage/update.md) | POST | Update entry page settings (1–5 fields per request) |

### Files (Drive)
| Endpoint | Method | Description |
|----------|--------|-------------|
| [files/upload](files/upload.md) | POST | Upload a file to the user's Drive |
| [files/fetch](files/fetch.md) | POST | Fetch all files with storage usage and pagination |
| [files/getFileUrl](files/geturl.md) | POST | Generate a short-lived presigned S3 URL to preview or download a file |
| [files/delete](files/delete.md) | POST | Permanently delete a file |
| [files/send](files/send.md) | POST | Send a file by email as an attachment |

### Help & Support Articles
| Endpoint | Method | Description |
|----------|--------|-------------|
| [helpsupport/fetch](helpsupport/fetch.md) | POST | Fetch help & support articles |

### Invoices
| Endpoint | Method | Description |
|----------|--------|-------------|
| [invoices/create](invoices/create.md) | POST | Create an invoice from a recipient and line items |
| [invoices/fetch](invoices/fetch.md) | POST | List invoices with summary totals |
| [invoices/single](invoices/single.md) | POST | Fetch one invoice with public link, QR and timeline |
| [invoices/update](invoices/update.md) | POST | Edit, publish or cancel an invoice |
| [invoices/delete](invoices/delete.md) | POST | Delete an invoice and its event trail |

### Notes
| Endpoint | Method | Description |
|----------|--------|-------------|
| [notes/create](notes/create.md) | POST | Create an encrypted note |
| [notes/fetch](notes/fetch.md) | POST | Fetch all notes |
| [notes/single](notes/single.md) | POST | Fetch a single note |
| [notes/update](notes/update.md) | POST | Update a note |
| [notes/delete](notes/delete.md) | POST | Delete a note |

### Participants
| Endpoint | Method | Description |
|----------|--------|-------------|
| [participants/add](participants/add.md) | POST | Add a participant |
| [participants/fetch](participants/fetch.md) | POST | Fetch participants |
| [participants/single](participants/single.md) | POST | Fetch a single participant |
| [participants/count](participants/count.md) | POST | Count participants |
| [participants/delete](participants/delete.md) | POST | Delete a participant |
| [participants/create-group](participants/create-group.md) | POST | Create a participant group |
| [participants/fetch-group](participants/fetch-group.md) | POST | Fetch participant groups |
| [participants/update-group](participants/update-group.md) | POST | Update a participant group |
| [participants/delete-group](participants/delete-group.md) | POST | Delete a participant group |
| [participants/update-bonus-entries](participants/update-bonus-entries.md) | POST | Update bonus entries for a participant |

### Rules
| Endpoint | Method | Description |
|----------|--------|-------------|
| [rules/create](rules/create.md) | POST | Create official rules (HTML) |
| [rules/create-wizard](rules/create-wizard.md) | POST | Create official rules (wizard) |
| [rules/fetch](rules/fetch.md) | POST | Fetch rules |
| [rules/update](rules/update.md) | POST | Update rules |
| [rules/delete](rules/delete.md) | POST | Delete rules |

### Support Tickets
| Endpoint | Method | Description |
|----------|--------|-------------|
| [support/create](support/create.md) | POST | Create a support ticket |
| [support/open](support/open.md) | POST | Fetch open tickets |
| [support/closed](support/closed.md) | POST | Fetch closed tickets |
| [support/single](support/single.md) | POST | Fetch a single ticket |
| [support/update](support/update.md) | POST | Update a ticket |
| [support/resolve](support/resolve.md) | POST | Resolve a ticket |
| [support/delete](support/delete.md) | POST | Delete a ticket |

### Surveys
| Endpoint | Method | Description |
|----------|--------|-------------|
| [surveys/create](surveys/create.md) | POST | Create a survey with its whole question set |
| [surveys/fetch](surveys/fetch.md) | POST | List surveys with question, page and response counters |
| [surveys/single](surveys/single.md) | POST | Fetch one survey with its complete question set |
| [surveys/update](surveys/update.md) | POST | Update a survey and optionally replace its question set |
| [surveys/delete](surveys/delete.md) | POST | Delete a survey with its questions, responses, stats and media |
| [surveys/responses](surveys/responses.md) | POST | Fetch the individual answers collected by a survey |
| [surveys/report](surveys/report.md) | POST | Fetch the aggregated report of a survey |

### Sweepstakes
| Endpoint | Method | Description |
|----------|--------|-------------|
| [sweepstakes/create](sweepstakes/create.md) | POST | Create a new sweepstakes |
| [sweepstakes/fetch](sweepstakes/fetch.md) | POST | Fetch all sweepstakes |
| [sweepstakes/update](sweepstakes/update.md) | POST | Update a sweepstakes |
| [sweepstakes/delete](sweepstakes/delete.md) | POST | Delete a sweepstakes |
| [sweepstakes/clone](sweepstakes/clone.md) | POST | Clone a sweepstakes |
| [sweepstakes/pause](sweepstakes/pause.md) | POST | Pause a sweepstakes |
| [sweepstakes/unpause](sweepstakes/unpause.md) | POST | Unpause a sweepstakes |

### Todos
| Endpoint | Method | Description |
|----------|--------|-------------|
| [todos/create](todos/create.md) | POST | Create a to-do item |
| [todos/fetch](todos/fetch.md) | POST | Fetch to-do items |

### Tools (Utility)
| Endpoint | Method | Description |
|----------|--------|-------------|
| [tools/timezones](tools/timezones.md) | POST | Fetch all timezones |
| [tools/countries](tools/countries.md) | POST | Fetch all countries |
| [tools/states](tools/states.md) | POST | Fetch US states |
| [tools/zipcodes](tools/zipcodes.md) | POST | Fetch zip codes |
| [tools/areacodes](tools/areacodes.md) | POST | Fetch area codes |

### Wallet
| Endpoint | Method | Description |
|----------|--------|-------------|
| [wallet/transactions](wallet/transactions.md) | POST | Fetch wallet transactions |

### Winners
| Endpoint | Method | Description |
|----------|--------|-------------|
| [winners/draw](winners/draw.md) | POST | Draw a winner |
| [winners/fetch](winners/fetch.md) | POST | Fetch winners |
| [winners/scheduled](winners/scheduled.md) | POST | Schedule a winner draw |
| [winners/fetchscheduled](winners/fetchscheduled.md) | POST | Fetch scheduled draws |
| [winners/deletescheduled](winners/deletescheduled.md) | POST | Delete a scheduled draw |

---

## Advanced Topics

- [Concurrency](concurrency.md) — Rate limits and concurrent request handling

---

## Error Codes

| Code | Meaning |
|------|---------|
| 200 | Success |
| 400 | Bad Request — missing or invalid parameters |
| 401 | Unauthorized — missing or invalid Bearer token |
| 403 | Forbidden — invalid API token or plan limit exceeded |
| 404 | Not Found — resource does not exist |
| 500 | Internal Server Error |

---

## Support

- Email: support@sweeppea.com
- Response time: Within 24 hours
- Website: https://www.sweeppea.com
