# Storybook visual regression example

An example of [storybook-vrt](https://github.com/krystxf/storybook-vrt), with before/after/diff
images posted in a single, updating pull request comment.

See [demo PR #21](https://github.com/krystxf/storybook-vrt-example/pull/21) for the attachment-based report.

## How it works

On each PR update, the action builds Storybook for the PR and its target commit, captures
stories in light and dark themes, and compares their pixels. Changed stories block the
`visual-regression` status until approved; added and removed stories are informational.

Images are uploaded as native GitHub attachments and embedded in the report comment.
No screenshots are committed to the repository, and no snapshot branch or external image
hosting is needed. A report artifact stores the comparison and attachment URLs so approval
comments can update the report without taking screenshots again.

## Workflow setup

The [workflow](.github/workflows/visual-regression.yml) uses `krystxf/storybook-vrt@v2`.

1. Create the repository Actions secret `VRT_ATTACHMENT_TOKEN` using a user PAT or OAuth token
   with push access. GitHub App installation tokens, including Actions `GITHUB_TOKEN`, cannot
   upload attachments.
2. Pass that secret through the action's `attachment-token` input. The normal workflow token
   handles comments and statuses, with `contents: read`, `actions: read`, `pull-requests: write`,
   and `statuses: write` permissions.
3. Keep the workflow on the default branch: GitHub uses that version for approval-comment events.
4. Require the `visual-regression` status in branch protection if visual approval should block merging.

The runner needs Node 24 and a recent GitHub CLI supporting `gh pr comment --attach` (≥ 2.99).
Report artifacts follow repository retention settings. If a report expires, rerun the PR checks.

## Approving changes

Repository owners, members, and collaborators can comment either:

```text
approve changes UI/Button
```

or, for a single story:

```text
approve changes UI/Button › Default
```

Editing out or deleting an approval revokes it. Fork PR workflows normally lack the secrets and
write permissions needed to publish reports.

## Migrating from snapshot storage

The workflow now uses attachments instead of `vrt-snapshots`. Remove `snapshot-branch` from
any custom action configuration and rerun open PR checks after migrating. The old snapshot
branch can then be deleted; older reports that link to its files may lose their images.
