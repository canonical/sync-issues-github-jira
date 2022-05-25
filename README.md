# Sync issues from GitHub to Jira
Automation to sync issues from GitHub (using GitHub actions) to Jira (via Jira webhooks)

## Principle

Labelling any GitHub issue with `jira` (can be parameterized) will trigger a Jira issue to be created with the same
title and description. Starting from that moment in time, any supported actions on the GitHub issue will be mirrored
on your Jira board with the corresponding ticket.

### Supported actions

* Labelling a GitHub issue will create a corresponding Jira issue linking back to the GitHub issue. Title and description
  are imported and the issue will be tagged with the selected component.
* Removing the label on a GitHub issue will delete the corresponding Jira issue.

Once labelled:

* Any changes in title and description will update the corresponding Jira issue.
* Any new comments will be mirrored on Jira. If you want to mirror a comment that was posted before the issue was
  labelled, you can edit the comment.
* Editing a comment will post as a new comment on Jira (you can still see the previous comment content as a separate one).
* Closing an issue will set the Jira issue to `Done`.
* Reopening an issue will set the Jira issue to `Selected for development`.

### Known limitations

* Deleting an issue on GitHub (admin only action) will not delete it on Jira (we can not retrieve the label).
* The syncing is only one way: GitHub to Jira. Action on Jira issues will not be mirrored back on GitHub issue.

## Usage

```yaml
- uses: ubuntu/sync-issues-github-jira@v1
  with:
    # Jira integration webhook URL.
    # Store it as a secret as anyone who has access to it will be able to post to your Jira board.
    # This field is required.
    webhook-url: ''

    # Jira component to attach your issue to. This component should exists in your project.
    component: ''

    # Label which will trigger a Jira import.
    # This default to jira.
    label: 'jira'
```

To trigger your action, you need to listen on both `issues` and `issue_comment` events.

## Examples

### Use jira as importing label with no components

```yaml
name: Sync GitHub issues to Jira
on: [issues, issue_comment]
jobs:
  sync-issues:
    name: Sync GitHub issues to Jira
    runs-on: ubuntu-latest
    steps:
    - uses: ubuntu/sync-issues-github-jira@v1
      with:
        webhook-url: ${{ secrets.JIRA_WEBHOOK_URL }}
```

As stated in the description, use a secret to store your Jira webhook URL. Otherwise, anyone who has access to it
will be able to post to your Jira board.

This default configuration will not attach any Jira component to your issue and will trigger on any GitHub issue action
with the label `jira`.
