# Mapping

Single source of truth for project and area name → folder path resolution. Used by all automations and workflows.

> **Vault root:** `[VAULT_ROOT]`
> To construct an absolute path: prepend vault root + folder path from the table below.

## Items

| Name | Aliases | Category | Sub-type | Parent Area | Folder | Todoist Project ID |
|------|---------|----------|----------|-------------|--------|--------------------|
|      |         |          |          |             |        |                    |

<!--
Category: Project or Area
Sub-type: ggt-client, ggt-internal, personal
Parent Area: for Projects — the Area this project belongs to (leave blank if none)
Todoist Project ID: numeric ID from Todoist (leave blank if not using Todoist, or if this item has no matching Todoist project)
  To find a project's ID: open Todoist, right-click the project → Copy link — the ID is the number at the end of the URL.

Examples:
| Acme Corp          | acme       | Area    | ggt-client   | —         | Areas/acme-corp/                   | 2345678901         |
| Acme Website       | acme-web   | Project | ggt-client   | Acme Corp | Projects/acme-website/             | 2345678902         |
| Q2 Perf Reviews    | perf       | Project | ggt-internal | —         | Projects/q2-perf-reviews/          |                    |
| Team Leadership    | team-lead  | Area    | ggt-internal | —         | Areas/team-leadership/             | 2345678903         |
| Health & Fitness   | health     | Area    | personal     | —         | Areas/health-fitness/              | 2345678904         |
| Learn Spanish      | spanish    | Project | personal     | —         | Projects/learn-spanish/            |                    |
-->

## Aliases

Used for fuzzy matching calendar events and meeting titles:

| Alias | Maps to |
|-------|---------|
|       |         |

## Meeting Filter

<!-- This section is configured during setup. If you have a company email domain,
     external meetings are identified by attendees whose email does NOT match your domain. -->
