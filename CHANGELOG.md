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

## 2.5.0 — 2026-08-19

### New features
- **Report on the document type you actually want.** Instead of one
  "Governance documents" option, the list now offers **Policies, Procedures,
  Standards, Work instructions** and **Guidelines** separately — plus an "All
  governance documents" option when you do want them together. Each respects
  its own access: somebody who can read procedures but not policies is only
  offered procedures.
- **Filter by status, and by whatever else fits the data.** Choosing a dataset
  now offers the filters that belong to it — Draft / For approval / Published
  for a document, Draft / Open / Resolved and the priority for an incident,
  Active / Inactive and the role for a user account. The controls change with
  the dataset, without the page reloading.
- **The report form is tighter.** Controls are sized to their content rather
  than stretched across the screen, and the time range is a Relative /
  Absolute / All time switch that shows only the fields for the mode you
  picked — instead of both at once.
- **Download is one menu** with Excel, CSV and PDF, rather than three buttons.

### Bugs & fixes
- **The owner filter is usable immediately.** It stayed greyed out until a
  report had been generated, because the list of people was only fetched once
  the dataset had been submitted. It now enables the moment you choose a
  dataset — and says so plainly when a dataset has no owner to filter by.

---

## 2.4.0 — 2026-08-19

### Bugs & fixes
- **The Reports pages showed developer notes as text and did not work.** Blocks
  of explanatory comment appeared on screen and the dataset chooser was left
  unusable. Fixed, along with four other pages carrying the same fault in
  rarely-seen states (a suspended account's dashboard, the purge confirmation,
  the backup list and the approval forms).
- **A new incident can no longer be created into a broken state.** Changing the
  status away from Draft while creating a ticket produced "This NICAR has
  already been issued" and left a ticket in the queue with no number, which
  could then be neither issued nor deleted. Status is now fixed to Draft until
  the ticket is issued — issuing is what allocates the number and sends the
  notice — and becomes editable straight afterwards. The same protection covers
  editing a draft, which could produce the identical broken ticket.

### New features
- **Reports & exports is now a single "Generate a report" screen.** Instead of
  ten cards each with their own buttons, choose the data, optionally whose it
  is, and a time range — then Apply & Run.
- **Time ranges can be relative or absolute.** "Last 30 days" for a standing
  question, or explicit From and To dates for a defined period. The To calendar
  will not offer a date before the From date.
- **Results appear on the page** as a table, so you can check the report is what
  you meant before downloading it.
- **Download as Excel, CSV or PDF.** PDF is new — a landscape, print-ready
  table with the heading, the filters used and the row count, for putting
  straight into a pack.

---

## 2.3.2 — 2026-08-19

### Bugs & fixes
- **The Reports link now appears for everyone who can use it.** The sidebar
  only showed it to people who could see policies, risks or controls — the
  right list when reporting covered exactly those three. Now that reports cover
  ten datasets, somebody whose access is the incident queue or the document
  repository could use reporting but had no link to reach it. The menu now
  follows whatever data the person can actually read.

---

## 2.3.1 — 2026-08-19

### Bugs & fixes
- **Updating from version 2.0.1 or earlier no longer fails at the backup step.**
  `update.sh` takes its safety backup using the version that is still running,
  and the `--once` option it passes did not exist before 2.0.2 — so anyone
  upgrading from an older release was stopped by "unrecognized arguments:
  --once" at the exact moment they were doing the right thing. The updater now
  falls back to the older command automatically. The backup taken is identical;
  only the way it is asked for changes.

---

## 2.3.0 — 2026-08-18

### New features
- **Reports & exports is now a proper reporting module.** It used to offer three
  fixed downloads. You can now export **ten** datasets — governance documents,
  the risk register, controls, control mappings, framework coverage, review
  cycles, incidents, incident notices, repository documents and user accounts —
  and choose exactly what goes in the file.
- **Build a report and save it.** Pick the columns you want, filter by status,
  owner, priority, framework or a date range, choose the sort order, and save
  the whole thing as a named report. Running it again is one click, and it
  always reflects today's data rather than a stale snapshot.
- **Excel output.** Reports download as a formatted `.xlsx` — frozen header row,
  filter dropdowns, sensible column widths, and dates and numbers as real cells
  rather than text — or as CSV if you prefer. Both are safe to open: a cell can
  never be treated as a formula.
- **Reports can be emailed on a schedule.** Set a report to go out daily, weekly
  or monthly and it arrives in the recipients' inboxes with the file attached.
  Useful for a monthly risk pack or a quarterly access review nobody has to
  remember to prepare.
- **Reports can be shared.** A shared report is visible to colleagues, but it is
  always run against the reader's own access — so sharing a report can never
  show somebody data their role does not allow, and neither can emailing it.
- **Report history** records what was produced, by whom, when, and who a
  scheduled copy went to. The files themselves are not stored; re-running a
  report reproduces it.

### Bugs & fixes
- The three original export links keep working and now go through the same
  export path, so they benefit from the spreadsheet-safety handling too.

---

## 2.2.1 — 2026-08-18

### Upgrade note
- **If you are running 2.1.0, 2.1.1 or 2.2.0, update to this release.** Those
  versions carry a security policy that stops the admin interface's JavaScript
  from running: the theme switcher, tabs, dialogs and the sidebar all stop
  responding, and the keyboard-shortcuts panel appears stuck open over the
  page. Nothing is lost or exposed — the interface is simply unusable until
  this release. Going straight from 2.0.x to 2.2.1 avoids it entirely.

### Bugs & fixes
- **The admin interface works again under the new security policy.** The
  Content-Security-Policy added in 2.1.0 blocked the way the admin theme
  evaluates its own interface code, so every interactive element failed
  silently in the browser. The policy now permits it. Protection against
  loading outside code and against sending your data to another site is
  unchanged.

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
