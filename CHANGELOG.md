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

## 2.16.0 — 2026-09-25

### New features
- **Departments.** Every person can be placed in a department, and every page opens on
  their own department's work — the dashboard, documents, review trackers and reports —
  with "All departments" one click away. A department's people can open and edit their own
  documents but not another team's. Departments ticked as "GRC team" (Information Security
  & Cybersecurity, Risk & Compliance) see and work across the whole organisation.
- **Recipients answer a NICAR on a page of its own.** The notice email carries a "View and
  respond" button; after signing in, the recipient sees the finding, their deadline, the
  conversation so far, and a box to reply with evidence attached. Nobody else can open it,
  and every time someone opens a ticket it is recorded.
- **An SLA clock for each side, like ServiceNow's.** A NICAR is always waiting on either the
  recipient or the GRC team, and only that side's clock runs. The SLA tab shows a progress
  ring and one row per turn — who, when, time taken, % of the SLA used, breached or not.
  Only a reply the other side can see stops a clock; an internal work note does not.
- **Reply to the recipient, or record a reply that came by email,** from the ticket. An
  emailed reply is filed at the time it actually arrived, so the delay in filing it is not
  charged to the recipient.
- **Changing a NICAR's priority moves its deadline.** Raising a P3 to P1 re-measures the
  running clock from its start at the new priority — it can go overdue on the spot, which is
  the honest answer — and the log trail says where the deadline moved to.
- **A recipient who has ignored the chases shows as overdue.** The SLA tab reads "Overdue
  since … · 3 follow-ups unanswered", and the queue, the dashboard card and the SLA filter
  count it, even while the latest follow-up window still has time on it.
- **Escalations are on the record.** Adding a manager to a NICAR's To or Cc — or removing
  or moving someone — appears in the log trail with who did it and when. The people added
  receive every notice from then on, and the running clock names them.
- **A third slide on the sign-in page.**

### Bugs & fixes
- **Pages open in about a third of the time.** Every page was working out the sidebar, the
  header and the sign-in settings several times over before showing anything; most pages
  now answer in under 100 ms on the server.
- **NICARs issued before this release have their full SLA history.** The follow-up windows
  they had already missed — and any priority change along the way — are rebuilt from the
  notices that were sent.
- **Opening the History of a document or NICAR you may not see now says it does not exist,**
  instead of showing its change log.

### Upgrade note
- **The first start after upgrading takes a little longer** while the database is updated
  and existing NICARs' SLA history is rebuilt. Nothing needs to be done by hand.
- **Set the Site address** (Administration → Site address) if you have not: without it the
  notice emails cannot carry the "View and respond" button and fall back to the old wording.
- **New SSO accounts now start with no access** unless a group mapping gives them a profile.
  An existing `.env` that sets `DJANGO_SSO_DEFAULT_ROLE=viewer` keeps the old behaviour;
  change it to `none` to adopt the new default. Existing accounts are not changed.
- **Nobody's access changes until you place them in a department** (Administration →
  Users, "Set department"). People without one keep exactly the access they have today.

## 2.15.1 — 2026-09-24

### Bugs & fixes
- **Review reminders arrive on the right day.** The daily 07:00 check read the date in UTC,
  which in Manila is still the day before until 08:00 — so every reminder came a day late,
  nothing was sent on the due date, and the morning after it the email said "due today".
  A document's overdue-for-review badge had the same fault.
- **A restart no longer costs a day of reminders.** Started after the 07:00 slot — an
  upgrade, a reboot — the reminder job now runs straight away instead of waiting until the
  next morning, and a 15-, 10- or 3-day notice missed that way is sent late rather than never.

### Upgrade note
- **Expect a few review reminders straight after upgrading.** Any review within 15 days of
  its due date that has not had its latest reminder is sent it on the first run — which is
  immediately if you upgrade after 07:00.

## 2.15.0 — 2026-09-24

### New features
- **A new sign-in page.** Two panels: a slideshow of the product on the left that moves on
  every three seconds — the dots beneath it jump to a slide — and the sign-in card on the
  right, logo centred. The backdrop follows the light and dark themes.
- **The auditor's dashboard shows the work, not only the counts.** Four cards — For Review,
  With Findings, Being Prepared, Certificates Issued — above the audit queue itself and a
  feed of the latest activity on every readiness track.
- **The main dashboard's five cards sit on one line**, each with an icon behind its count,
  and read: NICAR SLA breached, Pending Approvals, Reviews Overdue, Residual Risks
  (Critical/High), Controls with no evidence.

### Bugs & fixes
- **"Return to site" is gone from the sign-in page.** On this platform the site is the
  sign-in page, so the link led straight back to where you were.

## 2.14.1 — 2026-09-23

### New features
- **A change of priority on a NICAR is announced.** Raising or lowering it moves both
  clocks — the update target and the recipient follow-up ladder — so whoever the ticket
  is assigned to is told, in their bell and by email, with the new intervals named.
- **Notices Sent** uses the same filter panel as every other list ("By sent"), instead of
  the old row of month links above it.
- **The Review load chart is clickable**: each month opens the review cycles it counts.

### Bugs & fixes
- **An auditor's dashboard is the audit.** It showed the whole InfoSec workload —
  approvals, review load, control coverage, the governance library. It now shows the
  audit queue and where to find the read-only library.
- **EasyComply readiness reads as a whole number** (100%, not 100.0%).

### Upgrade note
- **Restart every service, not only the web container.** The follow-up ladder, the SLA
  sweep and the scheduled reports run in the health-recorder container; a deployment that
  updates the application image but leaves that container on an older one will send no
  follow-ups. `./update.sh` does this correctly.

## 2.14.0 — 2026-09-23

### New features
- **Controls as a tree.** All Controls shows each parent control with ▶ and a count;
  its sub-controls open indented beneath it, several levels deep (Req 1 › 1.2 ›
  1.2.1). PCI DSS v4.0.1 is the framework shown when the page opens. The bar reads:
  search, Manage Scope, Expand all / Collapse all, Filters. Add a control as a Parent
  Control or a Sub-Control — a sub-control picks its parent from a searchable list
  and takes its framework and category. Controls also carry testing guidance.
- **PCI DSS v4.0.1 sub-controls.** Requirements 1–12, their 63 sections (1.1, 1.2…)
  and 250 sub-controls, added automatically where PCI DSS is set up, each with a
  summary and testing guidance.
- **Search as you type** on every list with a search box: rows filter while you type,
  forgiving typos and missing letters ("netwrk diagrm" finds "network diagram"); ×
  clears it; Enter still searches everything. On Controls a match shows with the
  controls above it.
- **Pages use the full width of the screen** instead of a centred column.
- **Manage Scope** (administrators). Switch sub-controls in or out of scope — with a
  required justification — or apply a preset: SAQ A, SAQ C, SAQ D, or full scope.
  Out-of-scope controls are hidden from everyone but administrators and never count
  towards readiness. After a change, **Save as preset** names the scope as it stands
  and adds it to the preset list, to apply again later.
- **Assign to Framework** in the Controls bulk menu (administrators): move the ticked
  controls, each with its sub-controls, to another framework.
- **EasyComply (Beta)** under Overview: audit readiness as a guided course. An
  administrator sets up a track in four steps (framework, scope, audit date and lead,
  launch) and the team is emailed. Each in-scope control becomes a task in four
  steps — guidance, policy mapping, risk check, evidence with an expiry date — that
  saves as you go. Completed tasks raise the readiness bar (also on the dashboard);
  an editor's completion waits for a manager's review; managers can re-open. Auditors
  get an inspection view with one-click evidence, Accept Evidence / Request
  Clarification, and Q&A per control. A track follows later scope changes: a control
  brought into scope gets its task, one taken out stops counting (its work is kept).
- **EasyComply audit cycle.** At 100% a manager clicks **Submit for Audit Review**; the
  track is read-only while auditors review it. Auditors mark each control **Approve**,
  **Needs Clarification** or **Reject** (with remarks, and for a rejection what must be
  replaced), watching **Audit Completion** climb. With findings, **Submit with Findings**
  returns the track: those controls re-open with the remarks, the team answers and
  replaces what was rejected, and resubmits. With none, **Mark as Complete, Issue a
  Certificate** opens the certificate form — live preview, a drawn signature — and
  closes the track. Every step is kept in an audit log.
- **Audit Review History** tab: every track with its status, dates, framework and
  auditor; completed ones open read-only and their certificates download.
- **Certificates.** Certification › Templates designs certificates in the app with a
  live preview: a formatting toolbar for the body (font, size, bold, italic, underline,
  colour, alignment, lists, indent, quote, strikethrough, clear formatting), variables
  such as {{company}} and {{framework_name}}, colour, frame and logo. The PDF carries
  the same formatting. Save stays off until something changes, and leaving with unsaved
  changes asks whether to save them. Certification › Records lists every certificate.
- **Certificates are emailed.** Issuing one sends a congratulatory email with the PDF
  attached — To, Cc and Bcc chosen on the form as on a NICAR, the team by default, and
  both distribution lists always in Cc.
- **Internal Audit Team distribution list** beside the GRC Team one (Email delivery).
  Tracks submitted for audit review go to the auditors and this list; certificates are
  copied to both.
- **Company name and address** in Branding, above the product name. {{company}} on a
  certificate is the company name.
- **Auditors get their own sidebar**: Dashboard (led by the audit), Audit › Audit
  Review / Audit History, Certification › Records / Templates, and a read-only Library
  (Policies, Controls, Risk Register).
- **One track per framework, stated plainly.** Choosing a framework already in
  progress in the setup wizard says so and offers the existing track.
- With two or more tracks open, everyone but an administrator starts EasyComply by
  choosing the framework to work on.
- The profile card says **Account Created**, not Date Joined.
- **Security alerts** name the MITRE ATT&CK technique in the subject (Privilege
  Escalation, Account Access Removal, Account Manipulation) and read SECURITY ALERT,
  not "Rejected".

### Upgrade note
- **Readiness percentages change.** They now count in-scope controls with no
  sub-controls of their own, not the headings above them — so PCI DSS is measured
  across its 250 sub-controls instead of its 12 requirements.
- **Security alerts go to the GRC distribution list only** — access changes, record
  status changes and sign-in settings changes. People involved still see them in
  their bell. Set Administration › Email delivery › GRC distribution list to the
  team's mailbox before upgrading.
- **Every notification email now has one recipient.** Personal notices go to each
  person separately, plus one copy to the distribution list. Email subjects no longer
  start with the product name.

### Bugs & fixes
- **Approving a control no longer fails.** Approve on a control awaiting approval hit an
  error page: the form it posts back has no framework field once the record is read-only.
- **"Awaiting approval" on the dashboard opens what is waiting.** It always opened the
  documents list, which was empty when the items waiting were controls or risks.
- **Disabling or re-enabling a user asks why.** The reason is kept in History with who
  did it, and named in the security alert. A disabled account's other fields are
  read-only until it is re-enabled, and bulk disable / re-enable are recorded too.
- **An access change now reaches the administrator who made it** as well as everyone
  else — their own bell keeps the record.
- **Auditors no longer see a track before it is submitted.** Audit History and Audit
  Review offered View on tracks still being prepared, showing each control's progress.
- **Addresses no longer leak between recipients.** One notification email used to
  list every administrator and the affected person together, visible to all of them.

## 2.13.0 — 2026-09-18

### New features
- **Pages keep themselves current.** A new or changed record appears in every open
  list (NICAR queue, documents, users…) within a few seconds, highlighted once —
  no refresh. The list waits while you have a row ticked or a pop-up open.
- **Who's viewing.** A record shows the initials of everyone else who has it open,
  beside History, and names them above the form. They appear within about two
  seconds and leave when that person closes the tab.
- **Work notes arrive while you read.** A colleague's note appears in the ticket's
  Notes tab without a reload, and the Notes counter turns red when you have unread
  notes. The ticket's owner is told by bell and email when a note is added.
- **"Someone else saved this record"** — if a colleague saves a record you have
  open, the page says so and offers Reload, before you overwrite their changes.
- **NICAR follow-ups are now actually sent, and an unanswered notice closes
  itself.** Three follow-ups, spaced by priority — 1 - Critical every 12 hours,
  2 - High every 24 hours, 3 - Moderate every 3 days, 4 - Low every 5 days. One
  interval after the third, the NICAR closes as **Non-responsive**, the closure
  notice goes out, and the owner is told. A recorded reply, or closing the ticket
  by hand, stops the ladder at once.
- **NICAR queue redesign.** Two tabs — *All open work* and *My tickets* — with
  Open and In progress shown by default; columns NICAR No., Opened (date and time),
  Priority (coloured dot), State, Short description, Department / Business unit and
  Assigned to. Department and Assigned to open a filtered queue in a new tab. The
  gear beside Filters lets each person choose and order their own columns.
- **NICAR ticket redesign.** Two-column form; recipients as To / Cc chips with
  address checking; attachments as chips; Resolve & close asks for the resolution
  code and notes in a pop-up; Notes, SLA, Resolution and Log trail as tabs.
- **Groups: one per person, managed from the group.** A group's page lists everyone
  who is in no group beside the group's members, with › and ‹ to move people.
  Joining a group gives that group's access profile; every move is recorded in the
  group's History and announced to administrators. Groups are disabled or enabled
  from the list (**Change state**), never deleted.
- **Users.** The list shows Username, First name, Last name, Position, Email, Member
  of, Access profile and Active. The user page is redesigned around a profile card
  (picture, name, position, status, last login, date joined). **Reset User
  Password** emails a one-time link (never the distribution list); **Set Password**
  sits beside it; **Revoke session / Log out this user** ends every session.
  Accounts are deactivated, never deleted.
- **Profile pictures, the account button and Profile Settings.** The button at the
  foot of the sidebar shows your picture, name and position and opens your profile
  card: Change Profile Picture, status, theme, Logout, and a gear to **Profile
  Settings** — your own page, read-only except for your picture, your card's colour
  (a palette of solid and gradient colours) and, for accounts that sign in with a
  password here, Change Password. Profile cards show the company logo. Pictures also
  appear in "who's viewing".
- **The user page saves in place.** Save stays disabled until something changes and
  returns to the same page; the Active switch sits on the profile card.
- **Bulk actions on users:** **Modify Position** and **Add Users to Group**, each with
  its own menu beside the action menu (moving people between groups asks first).
- **Add, change, view or delete a position — or any lookup behind a ⋮ — in a pop-up**
  on the page instead of a separate browser window.
- **Login page** has a new city illustration, drawn for light and dark themes.
- **NICAR No.** is shown as a link in the queue; document lists show the owner's
  name only.
- **Documents.** The attachment has a Download button, the file picker only offers
  accepted file types, and Filters has a Last updated range (Today, Yesterday, this
  week, month, year).
- **The sidebar has one width** (320 px). It could be dragged narrow enough to wrap
  and overlap the menu and the profile card.
- **Settings pages name themselves once** — "Branding settings" rather than
  "Branding settings › Branding settings (…)".

### Upgrade note
- **Each person now belongs to exactly one group.** Anyone who was in more than
  one keeps a single group — a team group matching their profile if there is one,
  otherwise their profile's own group — and each change is written to the log.
  Someone removed from their group has no access until they are added to another.
- **System Purge has been removed.** A one-screen wipe of the platform is the first
  thing a stolen administrator session looks for. Any purge request on record is
  written to the application log before its table is dropped.
- **Open NICARs that have gone unanswered will be chased on the first day after
  upgrading.** The follow-ups were described on every ticket but never sent, so an
  old ticket is due its first follow-up at once. Before upgrading, close in
  **NICAR Queue** any open ticket that was settled outside the platform, or record
  the reply date on it, and it will be left alone.

### Bugs & fixes
- **Actions that send email no longer make you wait for the mail server.**
  Notification emails are sent in the background; adding a note or approving a
  document returns at once.
- **A record left open no longer keeps an idle session signed in.** The live
  updates do not count as activity for the idle sign-out.
- **System settings shows the developers' logos** (777AppBuilders | ISNEK) in a neutral
  grey that reads on both themes; the developer's name is corrected to ISNEK.
- **Form labels sit against their fields** (right-aligned), and read-only values
  such as Author line up with their label.
- **The final follow-up's deadline is no longer in the past**, and the ticket page
  shows both the next follow-up and the Non-responsive closure date.

## 2.10.0 — 2026-09-15

### New features
- **NICAR queue bulk actions: Resolve & close, Assign to, Void / cancel.** Tick
  tickets, choose an action and press Run. *Resolve & close* opens a pop-up for the
  resolution code and resolution notes, and will not close anything without both.
  *Assign to…* shows an **Analyst** menu beside the action menu (Viewers and
  Auditors are not listed); Run asks "Assigning N tickets to Analyst: … Confirm
  this activity?" with **Yes** or **No, cancel**, and the analyst is notified.
  *Void / cancel draft* asks for a reason. The menu only offers what applies to
  every ticked ticket.
- **Groups use access profiles.** A group now carries one of the five access
  profiles instead of hand-picked permissions, and the Groups list shows its
  *Access profile* instead of a permission count. Anyone whose own profile is
  lower gains the group's profile while they are in it — every administrator is
  alerted when that happens, and again when it is taken away.
- **Save as Draft and Submit for Approval wait for a change.** On an existing
  draft, *Save as Draft* stays off until something is edited, and turns off again
  if the edit is undone. For a record that was sent back, *Submit for Approval*
  also stays off until something changes — and the platform refuses resubmitting
  the rejected text unchanged. A draft that was never sent back can still be
  submitted as it is.
- **A livelier risk score.** The live risk score card is transparent, centres each
  score, counts up or down to the new value as likelihood and impact change, and
  names the band underneath with an icon: Low (1–7), Medium (8–14), High (15–19),
  Critical (20–25).

### Upgrade note
- **NICARs can no longer be deleted, by anyone.** Draft NICARs could previously be
  deleted. A draft opened by mistake is now voided with a reason — from the queue,
  or with *Void / cancel draft* on its own page — and stays on record as Voided /
  Cancelled.
- **Existing custom groups keep their permissions until you choose a profile.**
  They show *Not set (hand-picked permissions)* on the Groups list. Open each one
  and pick the access profile that matches what it is for; saving replaces the
  hand-picked permissions with that profile.

### Bugs & fixes
- **Unnumbered closed NICARs no longer show as "draft" in the queue.** Tickets that
  left Draft without a number under an earlier version now read *no number*, and
  voided drafts read *void*.
- **The group form's Description box now appears.** It was on the form but not
  shown.
- **The dashboard tile reads "High & critical residual risk"**, and the residual
  band filter "High or critical after treatment (15+)", matching the risk score
  card's bands.

## 2.9.0 — 2026-09-15

### New features
- **A published document reads as one sheet.** Title, version, author, dates,
  category and business unit sit on the left; document status, approval status,
  who approved (or rejected) it and who submitted it sit on the right. Below them
  are the description, document link (with a copy button), attachment and copy
  holder, then the version history, and *Review schedule* and *Review history* as
  tabs — far less scrolling. The *Body* field is now called **Description**.
- **Change record status opens as a pop-up.** The button at the top of every
  document, risk and control opens a small form: pick the status, give a reason,
  confirm. Close it with the X or by clicking outside it.
- **Archive and mark inactive from the list.** Tick published documents, pick
  *Archive* or *Mark inactive / deprecated*, press Run, and the same pop-up asks
  for the reason — no page change. The action menu only offers what applies to
  every ticked record, so *Revise this document* no longer appears for a document
  still awaiting approval.
- **Revise from the list opens the new revision in a new tab.**
- **Security alerts for record status changes.** Whenever someone changes a
  record's status — Archived, Inactive / Deprecated, Void, or reinstated —
  administrators, GRC Managers and the
  record's owner get a bell notification and an email saying who changed it, from
  what, to what, and why — with a prompt to report it to the Security Team if they
  do not recognise the activity. Automatic deprecation when a revision is
  published does not raise an alert.
- **Security alerts for access changes.** When a user is disabled, re-enabled, or
  moved to a higher or lower access profile, every administrator and the user
  themselves are notified by bell and email.
- **GRC distribution list for every notification email.** *Administration › Email
  delivery* has a new *GRC distribution list* field. The addresses there receive a
  copy (BCC) of every notification email the platform sends.
- **Submitting for approval confirms who was told.** The submitter now gets a bell
  notification and a message saying how many approvers were notified — or that no
  other approver is set up yet.

### Upgrade note
- **The GRC distribution email moved from System settings to Email delivery.** The
  address already saved is carried over automatically, and it now receives every
  notification email, not only NICAR notices.

### Bugs & fixes
- **Breadcrumbs no longer repeat the section name** ("Policies › Policies › HR
  Policy" is now "Policies › HR Policy").
- **The Record status label is no longer repeated** on the record page.
- **The live risk score card no longer covers the record status bar** on the risk
  page.

## 2.8.0 — 2026-09-15

### New features
- **Records are marked, never deleted.** Documents, risks and controls now have a
  record status alongside their approval status: **Void / Cancelled** for
  something created by mistake or as a duplicate, **Archived** for something no
  longer in use, and **Inactive / Deprecated** for something replaced by a newer
  version. Open any record and use *Change record status* at the top. A reason is
  required and saved with your name and the time. The record stays on file and
  in its history, drops out of the working lists and every dashboard figure, and
  can be found again with the new *Record status* filter. Only GRC Managers and
  Administrators can change it.
- **Publishing a revision retires the version it replaces.** The previous version
  is marked Inactive / Deprecated automatically and its review cycle is closed,
  so there is only ever one version of a document in force.
- **Access profiles.** A user's *GRC role* is now called their **access profile**,
  and it is the only thing that decides what they can do. The Groups, User
  permissions and Staff boxes are gone from the user page, where they could
  contradict the profile. *Administration › Access profiles* shows in plain words
  what each profile allows. Anyone holding access outside their profile is
  flagged on their own page, with an action to reset them.
- **Two new dashboard columns.** *Review in progress* counts periodic reviews that
  have been started, and *Inactive* counts archived and deprecated records. The
  column that used to say *In review* now says **Awaiting approval**, which is
  what it has always counted.

### Upgrade note
- **Documents with more than one version in force are corrected automatically.**
  Earlier versions left the old version of a revised document Published, with
  its own review cycle and reminders. On upgrade, the most recently approved
  version of each document stays in force. Any others are marked Inactive /
  Deprecated with the reason written on the record, and their open review cycles
  are closed. Nothing is deleted. If a different version should be the one in
  force, archive the kept version and reinstate the other.
- **Documents, risks and controls can no longer be deleted, by anyone.**
  Administrators and GRC Managers could previously delete a draft document that
  had never been published, and any risk or control. A published document could
  not be deleted by anyone, even though the permission was listed. Use **Void /
  Cancelled** for something created by mistake instead: it stays on file with the
  reason, and stops counting. Draft NICARs can still be deleted, as before.
- **Documents marked Retired become Archived.** Retired could only be set by
  hand-editing a hidden field. Those documents keep their history, and their open
  review cycles are closed.

### Bugs & fixes
- **A review can no longer be completed before it is started.** *Complete review*
  appeared on a cycle nobody had begun, and the platform accepted it. It now
  appears only after *Start review*, and the platform refuses the shortcut even if
  it is attempted directly.
- **A review that went overdue before anyone started it can still be started.**
  *Start review* was only offered on Pending cycles.
- **Changing a document's review schedule now moves an overdue review too.** The
  due date only followed the change for cycles that were not yet overdue.
- **Due and effective dates no longer land a day early.** A review completed, or a
  document approved, before 8am Philippine time was dated the previous day.
- **Bulk *Complete review* reports what it actually did.** It showed a success
  message even when it had completed nothing.
- **Voiding a mistaken revision no longer blocks the next one.** The corrected
  revision takes the next free version number.
- **Refusal pages explain themselves.** A few pages answered with a bare
  "Forbidden" line, and an expired form showed a technical notice about tokens.
  Both now show the platform's own page, with the reason and a way back.
- **The delete page no longer promises what nobody can do.** It told people an
  administrator could remove the records, which was not true for anyone. An old
  delete link now opens *Change record status*, with the reason.

---

## 2.7.1 — 2026-09-03

### Bugs & fixes
- **Evidence files no longer silently lose the notice they were attached to.**
  Only affects deployments sending through Microsoft 365 over OAuth 2.0, which
  was added yesterday in 2.7.0. Microsoft caps a single send at 4 MB including
  the attachments, which travel a third larger than the file on disk. The
  platform attaches evidence up to 5 MB and scheduled reports up to 8 MB, sizes
  ordinary mail servers carry without complaint — so a non-conformity notice
  with a large photo or PDF was refused by Microsoft, and because delivery
  failures are deliberately swallowed, nobody was told. The notice simply never
  arrived. Files too large for that route are now left in the case file and
  named in the notice as being there, which is what the platform already did
  for genuinely oversized files. Deployments sending over SMTP are unaffected
  and keep the 5 MB and 8 MB limits.
- **A failed send no longer blames your client secret.** A refusal from
  Microsoft at the sending stage was reported as though the credentials were
  wrong, sending an administrator to reissue a secret that was working. It now
  says what it usually is: no Exchange Online licence on the tenant, or a
  sending address that is not a mailbox in it.

---

## 2.7.0 — 2026-09-02

### New features
- **Microsoft 365 outbound email without a password.** Microsoft has been
  switching off basic authentication for SMTP, and on a tenant where that has
  happened there was previously no way for the platform to send mail at all.
  The Email delivery screen now offers **Microsoft 365 (OAuth 2.0)**, which
  sends through the Graph API using an app registration instead of a mailbox
  password. Choose the method, enter the tenant, client ID, secret and the
  mailbox to send as, and press the test button. The secret is encrypted at
  rest, and the change applies to the next message with no restart. Existing
  SMTP configurations are untouched and keep working exactly as before. The new
  setup guide walks a Microsoft 365 administrator through it, including
  restricting the app to a single mailbox — without that step the permission
  Microsoft grants would let it send as anyone in the organisation.
- **Failures now say what to fix.** When Microsoft refuses a message the
  platform reports which thing to go and correct — a missing admin consent, a
  Delegated permission where an Application one was needed, an expired client
  secret, an access policy that excludes the mailbox — rather than a status
  code.

### Upgrade note
- **One line to change by hand, and updating alone will not do it.** The web
  server version is pinned in the `docker-compose.yml` file on your own server,
  which no update touches — updates replace the application, not that file. So
  after updating you are still running the old web server, and a vulnerability
  scan will still report it. In `/opt/grc/grc-platform/docker-compose.yml`
  change `nginx:1.27-alpine` to `nginx:1.30-alpine`, then run
  `docker compose up -d nginx`. It takes about a minute and the site stays up.
  Step-by-step instructions are in the outbound email guide, Part 2.

### Bugs & fixes
- **The web server is no longer an end-of-life version.** New installations were
  pinned to nginx 1.27, a release line that stopped receiving security updates
  in April 2025 and which vulnerability scanners correctly report as
  end-of-life. New installations now use nginx 1.30, the current supported line.
  Existing installations need the one-line change described above.

---

## 2.6.5 — 2026-08-20

### Bugs & fixes
- **A new installation no longer stops halfway through.** The installer creates
  the platform's three secrets by running the application image, but the image
  starts the application first, and the application refuses to start without the
  very secret being created. Every new installation stopped at step 5 of 7 with
  a page of technical output and no explanation. Installs now run to the end.
- **A new installation's secret key is no longer cut short.** The generated key
  could contain a `$`, and Docker Compose reads everything from that character
  onward as the name of something else — so the platform received only the part
  before it, while the copy saved for you showed the whole key and nothing
  reported a problem. Around half of installations were affected. Where the
  character fell early, the key protecting sign-in sessions, form submissions
  and password-reset links was only a few characters long. Keys are now built
  from characters Compose leaves alone. **If you installed with an earlier
  version, check yours** — Part 4 of the hosting guide shows how.
- **The first-run setup token can now be found.** The installer and the hosting
  guide both told you to search the log for "setup token" — wording that never
  appears in it, so the search always came back empty and there was no way to
  reach the setup screen from your own computer. Both now give a command that
  works, and both say that the token is only written to the log once you have
  opened the setup page in your browser.
- **Two smaller installer faults.** `hostname: command not found` was printed
  partway through on Amazon Linux 2023, and the reads of EC2 instance metadata
  had no time limit — on an instance with metadata switched off the installer
  looked frozen instead of carrying on. The closing summary also now reports the
  version actually installed rather than the version you asked for.
- **The "what's new" list no longer ends with a stray `---`.** The separator
  between releases was being read as part of the last item, so every upgrade
  notice shown in the platform has carried three dashes on the end of its final
  line. Cosmetic only, and it has been there for every release so far.

---

## 2.6.4 — 2026-08-20

### Bugs & fixes
- **The installer no longer names a version in its prompt.** It offered a real
  version as a format example, which went stale the moment the next release
  shipped — and anyone typing it would install that older version successfully,
  with nothing to say so. The prompt now shows the shape only (`vX.Y.Z`), so
  your hosting guide is the single place that names the version to install.

---

## 2.6.3 — 2026-08-20

### Bugs & fixes
- **The installer no longer suggests an old version to type.** Its prompt gave
  a specific version as an example, and anyone taking that at face value
  installed it — successfully and silently, ending up several releases behind
  with nothing to indicate it. It now asks for the version named in your
  hosting guide, so the guide is the single place a version is stated.

---

## 2.6.2 — 2026-08-20

### Upgrade note
- **If you host behind a load balancer, point its health check at `/healthz`.**
  A load balancer checks a server by its IP address, and the platform refuses
  addresses it has not been told to answer for — so a health check aimed at any
  ordinary page was refused and the server could be marked unhealthy even
  though the site worked. `/healthz` is answered by the web server itself and
  always replies. New installations are set up this way automatically; an
  existing one needs the path changed once, in the target group.

### Bugs & fixes
- **A brand-new AWS installation could finish looking like it had failed.** The
  installer's final "is it answering?" check used an address the platform is
  not configured to answer for, so a perfectly good install ended with a
  warning. It now uses the health endpoint, and allows the server to answer to
  itself.

---

## 2.6.1 — 2026-08-20

### Bugs & fixes
- **The remarks box on a review is now visible and where you need it.** It had
  no outline at all, so the one thing you had to fill in did not look like a
  box — and it sat above eight read-only rows, so being told "remarks are
  required" sent people hunting up the page. It is now a properly outlined box
  in its own "Complete this review" section, immediately above the button.
- **Saving System settings says what happened.** The message read *The system
  settings "System settings (Asia/Manila)" was changed successfully*, which
  named a timezone for no reason. It now says **Changes saved successfully**,
  and the breadcrumb shows your platform's name instead.
- **The dashboard has less noise.** Removed the "What needs you, then what you
  have" line and the "Site administration" heading — the latter is Django's own
  name for itself, and it made people ask whether they were supposed to be
  there.

### New features
- **`make_branding_assets` generates correctly sized logos.** Point it at an
  existing logo and it produces the light and dark logos, the favicon and the
  login banner at the right dimensions, or it draws placeholders from your
  product name. `--apply` puts them straight into Branding.

---

## 2.6.0 — 2026-08-20

### Bugs & fixes
- **Changing a document's review schedule now moves its open review cycle.**
  A policy published on an annual schedule and switched to monthly showed the
  new date on the document and the old one — a year out — on Review Trackers.
  The tracker is the page the review team works from, so the wrong date was the
  one people saw. The open cycle now follows the document. Reviews that have
  already been completed keep their original date, because that is the audit
  record of what was due when they were done.
- **The review cycle page no longer offers a Save button with nothing to save.**
  A review cycle has no fields anyone edits by hand — every one is filled in by
  the system as the cycle moves — so the page showed a Save button above an
  empty form, and pressing it did nothing. It now offers the two things a cycle
  can actually do: **Start review** and **Complete review**, with a box for the
  remarks a completion requires.

---

## 2.5.4 — 2026-08-19

### Bugs & fixes
- **The AWS installer now comes with the release.** The hosting guide told you
  to paste the contents of `install.sh`, but that file was not in the download —
  it had to be sent to you separately, and a new server could not be set up from
  the release alone. It is now attached to the release on its own, so it can be
  opened and copied in one step, and it is also inside the kit alongside a copy
  of the hosting guide.

---

## 2.5.3 — 2026-08-19

### New features
- **The AWS hosting guide has been rewritten for this release.** It now covers
  turning on IMDSv2 when the server is launched (two dropdowns that remove a
  whole class of attack), a "finish the job" section for the three things the
  installer cannot decide for you — getting backups off the instance, setting
  the idle sign-out, and checking the background worker is running — and a
  troubleshooting table extended with the problems people have actually hit:
  an old version showing after an update, a version badge that says "Latest"
  when it is not, scheduled reports that never arrive, and files that download
  without signing in.
- **Updating is documented as it now behaves.** `./update.sh` with no arguments
  reports what you are running and what is available without changing anything.
  The guide also gives the one-time extra step needed when coming from v2.0.1
  or earlier, whose update script predates two fixes it depends on.

---

## 2.5.2 — 2026-08-19

### Bugs & fixes
- **Two report addresses could produce an error page.** Asking for an
  impossibly long time range ("the last 99999999 months") or a report address
  carrying a mistyped person reference returned a server error instead of a
  report. Both are now handled: an absurd range is treated as "everything", and
  a reference that cannot exist is ignored rather than crashing. Reports are
  shareable links, so a hand-edited or truncated one should never break the page.

---

## 2.5.1 — 2026-08-19

### Bugs & fixes
- **The report filters are readable again.** Status, Role and the rest were
  rendered as a row of checkboxes with no spacing, labels running into the next
  box, wrapping across the form. Each filter is now a single button that opens
  a short list — and says what it is filtering on, so you can read the active
  filters without opening anything: "Status: Published", or "Status: 3
  selected". Filters where only one answer is possible, such as Account status,
  stay an ordinary dropdown.
- The underlying cause is fixed too: controls built while you use the page were
  relying on styling that is only assembled when the software is built, so it
  never reached them. They now carry their own.

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
