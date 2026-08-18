# Changelog

The source of truth for what each release contains. The release workflow parses
the top section of this file into `latest.json`, which is what a running
deployment fetches to decide whether it is up to date and what an upgrade would
bring — so these bullets are read by customers, not just by us.

Format, and it matters because it is parsed:

```
## X.Y.Z — YYYY-MM-DD
### New features
- one line per item
### Bugs & fixes
- one line per item
```

Write for the person running the platform, not for the person who wrote the
patch: say what changes for them, not which function moved.

---

## 2.2.0 — 2026-08-18

### New features
- **A NICAR that misses its update target now tells its owner.** The queue has
  always shown when a ticket was past its SLA, but nothing acted on it — a P1
  could sit overdue indefinitely and the only way to find out was to go and
  look. The owning analyst is now notified when a ticket breaches, and
  reminded again at the ticket's own SLA interval: hourly for a P1, every five
  days for a P4, so the reminder is as loud as the priority somebody assigned.
- **Working the ticket stops the reminders.** Adding a note or updating the
  ticket restarts the clock and ends the alerts — the way to stop the nagging
  is to do the work. If it falls behind again, the reminders resume.
- **The first breach is emailed; the repeats stay in the app.** An hourly
  email loop on a P1 is how a mail server gets treated as a spam source, which
  would cost you the incident notices that actually have to leave the building.
- **The dashboard has a "NICARs past SLA" tile**, naming the worst offender,
  and it links straight into the queue filtered to breached tickets.
- **The NICAR queue can be filtered by SLA state** — breached, due soon, on
  track — from the filter bar.

---

## 2.1.1 — 2026-08-17

### Bugs & fixes
- **The "up to date" badge no longer trusts a stale check.** If the daily
  update check stops running — its background container is down, or the host
  has lost outbound internet after one earlier success — the footer used to go
  on showing "(Latest)" indefinitely: a reassuring answer that was no longer
  being confirmed. It now drops the "(Latest)" claim once the last check is
  older than three days, so a stalled checker is visible instead of silent. A
  release that is already known to be available is still flagged regardless of
  the check's age.

---

## 2.1.0 — 2026-08-13

### Upgrade note
- **This release includes a web-server change that you must deploy for one of
  the security fixes to take effect.** Uploaded files are now access-controlled
  by the application, which only works once the new `deploy/nginx.conf` is in
  place and nginx has been reloaded. `docker compose pull && up -d` updates the
  application but **not** this file — deploy the updated `nginx.conf` and run
  `docker compose exec nginx nginx -s reload` as part of this upgrade.
  (`update.sh` and the release kit ship the file for you.) Until nginx is
  reloaded, uploaded files stay downloadable without signing in.
- Includes a database migration; it runs automatically on container start.

### Bugs & fixes
- **Uploaded files can no longer be downloaded without signing in.** Incident
  (NICAR) evidence, policy attachments and internal documents were being served
  straight off the web server, so anyone who had — or guessed — a file's link
  could retrieve it without an account. Downloading now requires an
  authenticated session. (Uploading always required signing in; this was a
  read-access gap, not an upload one.)
- **Every page now sends a Content-Security-Policy.** This limits what injected
  code could do in a visitor's browser — in particular it blocks a script from
  loading attacker code or sending data to another site. The sign-in page is
  covered too.
- **The global footer note is now sanitised.** Only safe text formatting and
  links are rendered; scripts and other active content placed in that field are
  stripped, so it can no longer be used to run code on the sign-in page.
- **Exported CSV reports can no longer smuggle a spreadsheet formula.** A cell
  beginning with `=`, `+`, `-` or `@` is now kept as text, closing a way a
  crafted entry could run when the export is opened in Excel or Google Sheets.
- **The backup restore checks extracted file paths more strictly**, hardening it
  against a maliciously crafted archive.

### New features
- **Optional stricter Content-Security-Policy for administrators who want it.**
  A nonce-based, no-`unsafe-inline` script policy now ships in report-only mode
  (it observes without blocking). Once you have confirmed the browser console is
  clean across the admin, set `DJANGO_CSP_ENFORCE_STRICT_SCRIPTS=True` to
  enforce it. Left off, the platform keeps its existing, working policy.

## 2.0.2 — 2026-08-11

### Upgrade note
- The licence has changed. From this release the platform is proprietary
  software licensed for evaluation, not AGPL-3.0. Version 2.0.1 and earlier
  remain under the terms they were issued with. Read `LICENSE` in the release
  kit before deploying this version.

### New features
- **Restoring no longer depends on the platform being able to start.** A
  restore can now be run from the command line, which is what you need when a
  failed update leaves the application restart-looping and the Restore page
  unreachable. See `docs/DISASTER-RECOVERY.md`.
- **`update.sh` applies a release in one command.** It backs up first and stops
  if the backup fails, pins the version, restarts, then asks the running
  application which version it is — and puts the previous one back by itself if
  the new one does not answer. No more editing the compose file by hand.
- **A guided installer for Amazon Web Services**, plus a step-by-step hosting
  guide written for someone who is not a cloud engineer.
- **Approval, review and alert emails have been rebuilt.** They now match the
  incident notices rather than arriving as plain text, and they carry a working
  link back to the item that needs attention.
- **The email delivery test now reports how the message reached you** — the
  mail server, the sender address and which deployment sent it — so the person
  reading it in an inbox can tell whether the configuration is right.

### Bugs & fixes
- The change history on an incident now names the people involved. Handing a
  ticket to a new owner recorded the change as two internal identifiers, which
  no one could read and which made the trail useless as evidence.
- Status and priority changes in that history now read as words rather than
  stored codes.
- Emails now carry your own product name. Every notice used the built-in name
  regardless of what the platform had been renamed to.
- The plain-text version of an email no longer shows stray `&lt;` characters
  around addresses.
- A restore now refuses an archive taken on a newer version than the one
  running, instead of leaving a database the software cannot read.
- `backup_system --once` works. Every instruction we have published used that
  wording and it was rejected as an unrecognised option, which meant the backup
  step of the documented upgrade failed.

## 2.0.1 — 2026-08-10

### Bugs & fixes
- Single sign-on now works when the identity provider is configured from the
  Authentication screen. It could not: sign-in failed after the password had
  already been typed, because the platform advertised the wrong token-signing
  algorithm to the provider. Only deployments that set the provider up in
  `.env` were unaffected.
- Signing out of the identity provider as well as the platform now works on
  those same deployments — the switch existed and could not fire.
- **Site address** now appears under Administration. The screen that sets the
  platform's own address, and whether HTTPS sits in front of it, previously
  could only be reached by typing its URL.
- Pasting a full web address into the Site address field no longer takes the
  platform offline. It is reduced to the hostname, and the screen now says
  plainly that this is the address you are connected through.
- The warning about a redirect address that cannot be registered now leads
  with the usual cause — HTTPS is in front but the platform has not been told
  — instead of advising you to move to localhost.

## 2.0.0 — 2026-08-10

### New features
- Incident Response: raise a non-conformity against a department, track it, and
  close it with a record that stands up to an audit. New "NICAR Queue" in the
  sidebar.
- Each notice gets its own reference — IR-YYYYMMDD-XXX — allocated when it is
  submitted, and an email goes to the recipients you list, copying the GRC
  distribution address.
- Recipients do not need an account. They reply to the email with their evidence
  and an analyst files it against the ticket.
- The queue shows at a glance which notices need attention: priority, how long
  is left before an update is due, and which have been chased already.
- Notes, SLA, Resolution and Log trail are tabs on the ticket. The log trail
  merges who changed what with every email sent, and nobody can edit it.
- Resolving a notice requires a resolution code and notes, and sends a closure
  message to the recipients.
- Attachments are restricted to document and image formats. Files under 5 MB
  travel with the email; larger ones are referenced by the case-file link.
- Services is a new administrator-managed list, alongside Business Units.

### Upgrade note
- Set the JIRA base URL and the GRC distribution list under System settings →
  Incident response before raising the first notice. Without the distribution
  address no notice is copied to your team; without the JIRA URL a ticket
  reference shows as plain text rather than a link.

## 1.1.1 — 2026-08-10

### Bugs & fixes
- The site no longer returns "502 Bad Gateway" after an update. The web server
  was holding on to the application's previous network address, so every
  upgrade took the platform offline until it was restarted by hand.

### Upgrade note
- This one is not carried by the image. Replace `deploy/nginx.conf` on the host
  with the copy in this release's kit before running the upgrade commands —
  that file is read from disk, not from the container.

## 1.1.0 — 2026-08-10

### New features
- The version this deployment is running now appears in the footer of every
  page, marked "(Latest)" or "(New Update Available)".
- Administrators are told at sign-in when a newer release exists, with the
  current and new version side by side and a list of what changed.
- A "Software updates" page sets out exactly how to apply a release, including
  taking a backup first.
- The daily update check can be switched off, and sends nothing about your
  installation when it is on.

### Bugs & fixes
- Version History no longer fails with a server error on Policies, Procedures,
  Standards, Work Instructions and Guidelines.
- Edits to any governance document are now recorded in its history. Previously
  nothing done on those five pages was written to the audit trail at all.
- Version History is read-only, so an approved document can no longer be
  reverted without going through a revision and sign-off.
- Categories can be added directly from a document form again, using the "+"
  beside the Category dropdown, for administrators.
- App version and build reference on System settings are now read from the
  running build instead of being typed in and left to go stale.
- The dashboard's health panel no longer fails as a whole when a single check
  cannot read the disk.

## 1.0.0 — 2026-08-10

### New features
- First release delivered as a published container image.
