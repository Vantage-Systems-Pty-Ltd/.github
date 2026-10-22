# Contributing

Issue first, then a branch, then a PR that links the issue. No direct pushes to the default
branch: `main` moves only by merging PRs.

## Issues

Use the forms — blank issues are disabled. Each form sets the issue **type** for you.

The two things people skip, and why they matter:

**Set the type.** `Bug` / `Feature` / `Task` are configured org-wide. The forms set it
automatically. From the CLI, pass it yourself:

```bash
gh issue create --repo Vantage-Systems-Pty-Ltd/cnc-cartage-seed --type Bug ...
```

**Link dependencies structurally, not in prose.** Prose rots: a bare `repo#123` with no
owner is not an autolink.

Two different syntaxes, and mixing them up is the most common mistake:

| Where | Form | Example |
|---|---|---|
| **Issue/PR body prose** | `owner/repo#123` | `Relates to Vantage-Systems-Pty-Ltd/cnc-cartage-seed#123` |
| **`gh` CLI flags** | **full URL** | `--blocked-by https://github.com/Vantage-Systems-Pty-Ltd/cnc-cartage-seed/issues/123` |

`gh issue edit N --add-blocked-by owner/repo#123` fails with `invalid issue format`. Only
plain numbers (same repo) or full URLs (any repo) work as flag arguments.

```bash
# same repo
gh issue create --repo Vantage-Systems-Pty-Ltd/cnc-cartage-seed --type Bug \
  --title "..." --body-file /tmp/body.md --blocked-by 32

# cross repo — full URL
gh issue edit 51 --repo Vantage-Systems-Pty-Ltd/cnc-cartage-seed \
  --add-blocked-by https://github.com/Vantage-Systems-Pty-Ltd/other-repo/issues/14
```

Requires **gh >= 2.94.0**. Check with `gh --version`; older versions silently lack these
flags.

**Bring evidence.** An issue is ready to work when it carries what the fix will be measured
against: the record it affects (a job number, a report number, a request id), what was
observed, and where in the code it comes from (`path/to/file.ts:123`). "Seems broken" is a
question, not an issue.

## Pull requests

Link the issue — `Closes #123`, or `Refs #123` if it does not close it. Dependabot and
release PRs are exempt; anything else genuinely without an issue gets the `no-issue` label.

Fill in the test plan with what you actually ran and the counts you read. "Tested locally"
is not a test plan. Each repository's own `CLAUDE.md` or `README` names its gate; run the
whole of it once before the PR opens, and name any failure rather than re-running until it
passes.

A PR that carries a database migration says so, and says where the migration has already
been applied. A deployment that runs ahead of its migration is an outage.

## Tooling

A Claude Code plugin (`github-hygiene`) automates most of the above. A
Vantage marketplace carrying it is tracked in
Vantage-Systems-Pty-Ltd/cnc-cartage-seed#954; until it lands, the repository's own
`CLAUDE.md` is the rule and this page is its human-readable shape.

## Transferring a repository to Vantage Systems

When another company hands a repository to us, the transfer goes through GitHub's own
transfer, which keeps the full history, issues, pull requests, wiki, stars and watchers, and
redirects the old URL to the new one.

**Who does it.** Someone with admin access on the repository, signed in with a personal
account. GitHub only lets that person transfer straight into our organisation if their
account can create repositories here, which an outside account cannot. So the transfer takes
one of two routes:

1. **Via a Vantage owner's personal account.** Transfer the repository to the GitHub user a
   Vantage Systems owner names. They accept the transfer from the email GitHub sends (the
   invitation lapses after one day), then move the repository into `Vantage-Systems-Pty-Ltd`
   themselves. Two hops, no change to our organisation's membership.
2. **As a temporary member.** A Vantage owner invites the transferring account into the
   organisation for the day, with repository creation allowed, and removes it afterwards. One
   hop, but it means a day's membership for an outside account.

Agree the route and the receiving account with us before starting.

**Before the transfer**
- Pick the name it will have here and tell us. The organisation must not already hold a
  repository of that name.
- Note that webhooks, deploy keys, Actions secrets and GitHub Apps stay attached and keep
  working after the move. List them for us so we can rotate or remove what should not
  follow the code, and remove anything that points at your own systems.
- If the repository is a fork, say so: a fork stays linked to its upstream network after the
  transfer, and a lone fork of a private upstream cannot be transferred at all.
- GitHub Pages redirects are not kept; an existing Pages site needs its own plan.

**The transfer itself.** On the repository: **Settings**, scroll to **Danger Zone**,
**Transfer**, choose **Specify an organization or username**, enter the account agreed
above, type the repository name to confirm, and click **I understand, transfer this
repository**.

**After the transfer**
- Everyone with a clone updates the remote:
  `git remote set-url origin https://github.com/Vantage-Systems-Pty-Ltd/<name>.git`. The
  old URL redirects until someone creates a repository of that name at the old owner, so
  nobody should.
- Collaborators are not carried over from an organisation; we add the people who need
  access on our side.
- We review branch protection, Actions secrets, deploy keys and webhooks, and apply the
  conventions above: issue first, PRs into `main`, the forms and template this repository
  provides.
