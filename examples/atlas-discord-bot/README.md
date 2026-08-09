# Atlas Bot — Forum Post Notifier (role-gated)

A custom [Atlas](https://atlas.bot/) **Action** that posts an alert to a notification
channel whenever a member **with a specific role** creates a **forum thread** whose
message matches your keyword(s). Role gating and channel scoping are enforced by Atlas
**restrictions**, so no role-check logic is needed in the script itself.

> Atlas is a hosted Discord bot configured through its dashboard — this is not code you
> deploy. The `.atlas` file here is the exact script body to paste into an Action.

## Files

- **`forum-role-notify.atlas`** — the script body to paste into the Action's editor.

## Setup

1. **Create the Action.** Atlas dashboard → your server → **Actions** → **New Action**.
2. **Trigger type: `Keyword`.** Set the pattern to your trigger word(s):
   - Wildcards expand loosely (`bug*report` behaves like `*bug*report*`), so for precise
     phrase matching prefer a regex, e.g. `/urgent|critical/i` to match either word,
     case-insensitively.
3. **Add restrictions** on the Action so it only fires when:
   - the author **has the target role** (role restriction), and
   - _(optional but recommended)_ the message is **in the specific Forum channel**
     (channel restriction) so it never fires elsewhere.
4. **Paste the script** from `forum-role-notify.atlas` into the Action editor.
5. **Replace `NOTIFY_CHANNEL_ID`** with the ID of the channel that should receive the
   alerts (right-click the channel → **Copy Channel ID**; Developer Mode must be on).
6. Save and test by creating a forum post that matches your keyword as a user who has the
   role.

## What the script does

```
{if;{channel.isThread};                                  ← only continue for thread/forum posts
  {responder.channel;NOTIFY_CHANNEL_ID}                  ← target the alert channel
  {responder.text;📣 New forum post from {user.mention}  ← who posted
     in **{channel.name}**                               ← the thread's title
     {message.jumpUrl}}                                  ← direct link to the post
  {responder.send}}                                      ← send it
```

- `{channel.isThread}` is truthy for forum posts (each forum post is a thread), so the
  `{if}` body runs only for real threads — not stray messages in a text channel.
- `{responder.channel;…}` redirects the outgoing message to your alert channel; without it
  the reply would go back to the thread.
- `{responder.send}` sends explicitly. Keep **all** message text inside `{responder.text;…}`
  — any literal text placed *outside* a `{responder.*}` tag becomes an auto-reply that
  Atlas posts back into the trigger context.

## Caveats

- **Limits & cooldown.** Keyword actions are capped (2 on the free tier, more on Prime)
  and enforce a minimum cooldown per trigger, so rapid repeat posts may not each fire.
  Check your plan's current limits in the Atlas dashboard.
- **Permissions.** Atlas needs **View Channel** in the forum to read message text, and
  **Send Messages** in the notification channel.
- **Message Content intent.** Required to match on message text; Atlas has this as a
  verified bot, but the bot must still be able to see the channel.
- **Precise matching.** Because wildcards expand loosely, use a regex pattern when you need
  exact phrase or multi-keyword matching.

## Sources

- [Atlas — {responder} tags](https://docs.atlas.bot/actions/tags/responder)
- [Atlas — {channel} tags](https://docs.atlas.bot/actions/tags/channel)
- [Atlas — Getting Started / Actions](https://docs.atlas.bot/)
- [atlas.bot](https://atlas.bot/)
