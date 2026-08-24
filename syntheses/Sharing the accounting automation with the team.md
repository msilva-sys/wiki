---
title: Sharing the accounting automation with the team — distribution options
type: synthesis
status: active
updated: 2026-08-17
date: 2026-08-14
aliases: [distribution options, sharing automations]
tags: [claude, plugins, skills, distribution, admin, accounting]
related: "[[Claude capabilities map - accounting data scope]]"
---

# Getting one person's workflow to the whole team

Companion to [[Claude capabilities map - accounting data scope]] and
[[Meeting prep - accounting data in Claude - 2026-08-17]].

> [!tip] Short answer
> **Package it as a plugin and publish it to a private marketplace for your org.**
> A plugin bundles the skills (the procedure), the MCP servers (the data access), and
> commands into one installable unit — and admins control who gets it, whether it
> auto-installs, and whether it's optional or required. Everything else on this page is
> either a lighter-weight step on the way there, or a piece the plugin can't carry.

---

## The options, lightest to heaviest

| # | Mechanism | What it distributes | Who can do it | Good for |
|---|---|---|---|---|
| 1 | **Share a chat / artifact** | One conversation or output | Anyone | Showing the result. Not automation. |
| 2 | **Shared Project** | Instructions + knowledge (chart of accounts, close calendar, policy) | Anyone; org-visible or invite-only, view/edit | Shared *context*. Note: project chats stay private unless explicitly shared. |
| 3 | **Peer-to-peer skill share** | A single skill, colleague-to-colleague | Any member, if the admin toggles allow it | Piloting with one or two people before formalising |
| 4 | **Org-provisioned skills** | Skills to everyone, on by default | **Owners only**, Team/Enterprise, via Organization settings → Skills (upload .zip with SKILL.md) | A procedure everyone should have, with no connectors attached |
| 5 | **Plugin in a private marketplace** ⭐ | Skills + MCP servers + commands + hooks as one unit | Owners / Primary Owners, Team & Enterprise | **The real answer.** Versioned, governed, group-targeted |
| 6 | **Admin-deployed Claude for Excel** | The add-in itself, org-wide | M365 admin, via Microsoft 365 Admin Center → Integrated apps | If the team works in workbooks |
| 7 | **Org-level connectors** | Authenticated access to a data source, configured once | Admin | So each person doesn't wire up their own ERP/warehouse access |
| 8 | **Managed Agents / Agent SDK** | A shared service others call | Engineering | When it stops being a workflow and becomes a product |

### On #5, the important details

**Two ways to publish a plugin to your org:**

- **ZIP upload** — max 50 MB each, up to 100 per marketplace. Good for fast iteration and
  one-off internal tools.
- **Private GitHub repo sync** — up to 500 plugins; syncs automatically when a version
  bump merges to the default branch. This is the one you want long-term: the plugin lives
  in git, gets reviewed in PRs, and ships on merge. Your team already works this way.

**Four install preferences per plugin**, set org-wide:

- *Installed by default* — everyone gets it, can remove it
- *Available for install* — self-service (this is how the `finance` plugin sits today)
- *Not available* — hidden
- *Required* — automatic and permanent

**Enterprise plans** can override those per group, so the accounting group gets the finance
plugin auto-installed while everyone else doesn't see it. Where a person is in multiple
groups, the most permissive setting wins. Changes apply on the member's next session.

**Prerequisites:** Cowork and Skills must both be enabled for the org, and you need an
Owner or Primary Owner to do any of this. Worth confirming which plan LiveMode is on
before promising group-level targeting — that part is Enterprise-only.

---

## Which to use for what

- The **procedure** ("how we reconcile GL to subledger") → a **skill**, shipped in a plugin.
- The **reference material** ("our chart of accounts, our close calendar") → **project
  knowledge**, or a file bundled inside the skill.
- The **data access** ("read-only warehouse credentials") → an **MCP server** in the
  plugin, or an org-level connector.
- The **schedule** ("3rd business day, every month") → a Cowork scheduled task. Note this
  is currently **per-user** — each person sets their own, or one owner runs it and
  publishes the output.
- The **explanation** ("here's how to use it") → an onboarding doc. Cheap, and the thing
  most often skipped.

---

## Don't build before you fork

The `finance` plugin already in your catalogue contains `reconciliation`,
`close-management`, `journal-entry`, `variance-analysis`, `financial-statements`,
`audit-support` and `sox-testing`. The sane sequence is:

1. **Enable it for the one person** and use it on their real data for a close cycle.
2. **Note where it diverges** from how LiveMode actually does things — chart of accounts,
   entity structure, BR-specific obligations (SPED/ECD), approval chain, materiality
   thresholds.
3. **Fork it into a `livemode-finance` plugin** carrying those deltas, plus your own MCP
   server if/when the ERP connection exists.
4. **Publish to a private marketplace** — ZIP upload while iterating, GitHub sync once stable.
5. **Target the accounting group**, auto-install, and leave it removable at first.

Fork-then-publish beats build-from-scratch here by a wide margin: the pre-built skills
encode a lot of accounting procedure you'd otherwise write yourself.

---

## Governance — the part finance and security will ask about

Have these ready, because "we're putting the ledger into an AI tool" invites scrutiny:

- **Skill and plugin scanning** (Enterprise) flags malicious content and rejects the plugin.
- **Audit logs** record sharing as `role_assignment` events — who shared what, with whom.
- **Compliance API** captures Cowork sessions.
- **OpenTelemetry** streams Cowork events (tool calls, file access, approval decisions) to
  your SIEM.
- **Connector tool approval** and **network egress** controls are admin-configurable.
- **Enterprise-only default:** "Run Cowork in the cloud" is **off by default** and must be
  granted deliberately — relevant if the data can't leave your environment.
- **Known gap:** Claude for Excel activity is *not* in Enterprise audit logs or the
  Compliance API, and its chat history is local to the browser. If conversation-level
  auditability is a requirement, route the work through Cowork rather than the add-in.

---

## What to say Monday

Don't lead with this. Distribution is the *second* meeting. But if they ask "could the
rest of the team use this?", the answer is:

> "Yes — and we wouldn't rebuild it for each person. We'd package your workflow as a
> plugin and publish it to a private marketplace for the accounting group, so everyone
> gets the same procedure, the same data access, and the same audit trail. But let's get
> it working for you first, for one close cycle, and let the real version emerge from that."

That sequencing also protects you: a plugin published before the workflow is proven is a
plugin you'll be maintaining for people who don't use it.

## Sources

- [Provision and manage skills for your organization](https://support.claude.com/en/articles/13119606-provision-and-manage-skills-for-your-organization)
- [Manage plugins for your organization](https://support.claude.com/en/articles/13837433-manage-plugins-for-your-organization)
- [Cowork and plugins for teams across the enterprise](https://claude.com/blog/cowork-plugins-across-enterprise)
- [Create and distribute a plugin marketplace](https://code.claude.com/docs/en/plugin-marketplaces)
- [Manage project visibility and sharing](https://support.claude.com/en/articles/9519189-manage-project-visibility-and-sharing)
- [Use Claude Cowork on Team and Enterprise plans](https://support.claude.com/en/articles/13455879-use-claude-cowork-on-team-and-enterprise-plans)
- [Use Claude for Excel — org deployment](https://claude.com/docs/office-agents/excel)
- [Skills explained](https://claude.com/blog/skills-explained)
