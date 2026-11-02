---
title: Discord Security
description: Anti-nuke, join gate, raid detection, heat spam filtering, lockdown, quarantine, verification, backups and moderation commands for the Supervisor Discord bot.
slug: integrations/discord-security
nav: Discord Security
---

# Discord Security

Supervisor protects the structure of your server as well as its messages. The security features never call the AI models, so they use no credits and keep working when a server runs out of credits or turns AI moderation off.

Configure everything per server in the **Security** section of the [dashboard](https://supervisor.gg/dashboard/integrations/discord), then press **Save**.

## Free and Premium

The base version of every feature is free. **Advanced Security** is included with Supervisor Premium:

| Feature | Free | Premium |
| --- | --- | --- |
| Anti-nuke limits and protections | Yes | Yes |
| Panic mode, strict mode, prune detection | | Yes |
| Join gate | Yes | Yes |
| Join raid detection | | Yes |
| Heat spam filter | Fixed tuning | Tunable weights, limit, cooling, multiplier, heat panic mode |
| Auto lockdown, `/lock` channel, server and joins | Yes | Yes |
| `/lock` role permissions and everything | | Yes |
| Quarantine | Yes | Yes |
| Verification | Yes | Yes |
| Backups | | Yes |
| Moderation commands, cases, notes, warn thresholds | Yes | Yes |

Premium settings can be saved on a free server. They take effect once the server's billing admin (the owner, or an opted-in admin) has Premium. If Premium lapses, the settings are kept and the free behaviour applies.

## Set up

1. Give Supervisor these permissions: **View Audit Log**, **Manage Roles**, **Manage Channels**, **Ban Members**, **Kick Members**, **Timeout Members**, **Manage Messages** and **Manage Webhooks**.
2. Drag Supervisor's role near the top of the role list. It can only act on members whose top role is below its own.
3. Run `/security status`. It lists missing permissions and warns if the role is too low.
4. Run `/security setup` to create the **Quarantined** role and hide every channel from it.
5. Add your trusted users in the dashboard, switch on the features you want, and save.

## Trusted users and immune roles

- **Trusted users** (by Discord user ID) are exempt from every system, including anti-nuke. The owner is always trusted. Only trusted users and the owner can manage backups. Keep this list short.
- **Immune roles** skip heat, the join gate and auto lockdown. Anti-nuke still applies to them.
- **Moderator roles** can use moderation commands without holding the matching Discord permission.

## Anti-nuke

Off by default. When on, Supervisor reads the audit log and counts each member's destructive actions.

| Action | Default limit (per minute / per hour) |
| --- | --- |
| Channel creation | 5 / 20 |
| Channel deletion | 3 / 10 |
| Role creation | 5 / 20 |
| Role deletion | 3 / 10 |
| Bans | 5 / 30 |
| Kicks | 5 / 30 |
| Webhook creation | 3 / 10 |
| Webhook deletion | 3 / 10 |
| Member prune (Premium) | 1 / 2 |

Going over either limit applies the **punishment**: quarantine (default), timeout, kick, ban, or log only. Set a limit to 0 to turn that window off. The owner is sent a DM.

These are punished on the first occurrence:

- **Quarantine hold:** removing the quarantine role by hand, or giving it dangerous permissions. With hold on, only trusted users, the owner and administrators can use `/unquarantine`.
- **Vanity protection:** changing the vanity URL. Discord does not let bots set it back, so restore it yourself.
- **Monitor public roles:** dangerous permissions added to @everyone, or to a role at least half the members hold (servers with 10 or more members). The change is reverted.
- **Monitor channel permissions:** dangerous permissions given to @everyone in a channel. The change is reverted.
- **Bot addition filter:** a bot added by anyone other than the owner or a trusted user. The bot is kicked.
- **Strict mode (Premium):** dangerous permissions added to any role. The change is reverted.

Dangerous permissions are Administrator, Manage Server, Manage Roles, Manage Channels, Manage Webhooks, Ban Members, Kick Members, Manage Expressions, Mention Everyone, Timeout Members, Manage Messages, Manage Nicknames, Manage Events and Manage Threads.

### Panic mode (Premium)

When anti-nuke fires on a limit, panic mode:

1. Deletes the channels, roles and webhooks the member created in the last hour.
2. Locks every channel, except categories you exclude (for example tickets), and kicks new joins.
3. Restores the server from the latest backup.
4. Unlocks after 30 minutes (configurable; 0 keeps it locked until `/unlock`).

## Join gate

Checks members as they join. Each filter has its own action (log, timeout, kick, ban or quarantine):

- **New accounts:** younger than the minimum age (default 3 days)
- **No profile picture**
- **Unverified bots:** bots without Discord's verified badge
- **Invite links in names:** in the username or display name
- **Username filter:** names matching your word list. `*` matches partially (`free*`, `*nitro*`). Look-alike characters are folded before matching.
- **Harmful usernames:** names Supervisor's username model flags

**DM the reason** tells removed members why.

## Join raid detection (Premium)

Treats a burst of joins as a raid: by default 10 joins within 60 seconds. Count every account, or only suspicious ones (younger than 7 days, or without an avatar). The action applies to every account in the burst and to further raid joins. **Lock joins** kicks all new joins for 10 minutes after a raid starts.

## Heat

Each message adds heat. Heat cools every second. At the limit, the member is timed out and, if **Delete the spam** is on, the messages that built the heat are deleted.

Heat comes from: the message itself, repeating the previous message, emojis, length, new lines, mentions, @everyone and @here, attachments and stickers, Discord invites, and links on a public phishing and malware domain list (refreshed every 6 hours).

Free settings: first timeout (default 60 seconds), longest timeout (default 1 hour), delete the spam, include webhooks.

Premium settings:

- **Heat limit** (default 100), **cooling per second** (default 2), and the weight of every factor
- **Timeout multiplier** (default 2): each repeat offence within 24 hours multiplies the previous timeout, up to the longest timeout
- **Heat panic mode:** when 3 members hit the limit within 10 minutes (configurable), the limit drops to a quarter for 30 minutes

## Auto lockdown

Locks every channel when mentions from members without immunity reach the threshold: by default 50 within 30 seconds. @everyone and @here count as 10. Unlocks after 15 minutes by default, or with `/unlock`.

## Lockdown

`/lock` and `/unlock` take a scope:

- **This channel** and **Every channel:** deny @everyone sending, reacting, threads and voice. **Blind** also hides the channels.
- **Kick new joins** and **Ban new joins**
- **Dangerous role permissions (Premium):** removes dangerous permissions from every role below Supervisor
- **Everything (Premium):** every channel, role permissions, and ban new joins

Unlocking removes only what the lock added.

## Quarantine

`/quarantine` removes a member's roles (except managed roles such as boosts) and gives them the Quarantined role, which cannot see any channel. Their roles are stored with the case. `/unquarantine` gives them back. A quarantined bot is also kicked.

## Verification

Turn on verification and pick a verified role and verify channel in the dashboard, then send the verify message. Members verify with a Cloudflare check on supervisor.gg.

Options: **only suspicious accounts** (younger than 7 days or without an avatar; everyone else gets the role on join), and a **time limit** with an action for members who have not verified.

## Warns

Warns from `/warn` are cases. **Warn thresholds** apply an action when a member reaches a number of active warns. Warns expire after 30 days by default (0 keeps them forever). Close a warn with `/case open:false` to stop it counting.

## Backups (Premium)

Supervisor backs up roles, channels, permission overwrites and server settings every 3 hours by default (1 to 168) and keeps the latest 10 automatic backups. Manual backups are kept until deleted. Backups do not include messages or which members hold which roles.

- `/backup create`, `/backup list`, `/backup delete`
- `/backup load id:latest` recreates missing channels and roles and resets changed ones. `prune:true` also deletes channels and roles the backup does not have.

## Commands

| Command | Permission needed |
| --- | --- |
| `/ban user [reason] [duration] [delete_days]` | Ban Members |
| `/unban user [reason]` | Ban Members |
| `/kick user [reason]` | Kick Members |
| `/timeout user [duration] [reason]` | Timeout Members |
| `/untimeout user [reason]` | Timeout Members |
| `/warn user reason` | Manage Server |
| `/purge count [user] [filter] [contains]` | Manage Messages |
| `/slowmode delay [channel]` | Manage Channels |
| `/sanitize user` | Manage Nicknames |
| `/quarantine user [reason]`, `/unquarantine user [reason]` | Manage Roles |
| `/lock scope [blind] [channel]`, `/unlock scope [channel]` | Manage Channels |
| `/note add`, `/note list`, `/note remove` | Timeout Members |
| `/cases [user] [moderator] [kind] [number]`, `/case number [reason] [open]` | Timeout Members |
| `/verify user` | Manage Roles |
| `/backup create`, `list`, `load`, `delete` | Owner or trusted user (Premium) |
| `/security status`, `/security setup` | Manage Server |

Durations accept `30s`, `10m`, `2h`, `7d`, `1w` and combinations such as `1h30m`. A bare number is minutes. Timeouts are capped at 28 days. `/purge` cannot delete messages older than 14 days.

Moderator roles and trusted users can use these without the Discord permission. No one can act on a member whose top role is at or above their own or Supervisor's.

## Logs

Security events go to the **security log channel**, or the moderation alerts channel if none is set.

## Data retention

Cases, notes and backups are kept while Supervisor is in the server and deleted 30 days after it leaves. See the [privacy policy](https://supervisor.gg/privacy).
