# Workflow Dispatcher | GitHub Action

[![GitHub release](https://img.shields.io/badge/release-v1.0-blue?logo=github&style=for-the-badge)](https://github.com/Eaton-Vance-Corp/workflow-dispatcher/releases/latest)

## About

Lightweight composite steps Github Action for triggering a workflow in a different repository and waiting until its completion. Inspired by [Trigger Workflow And Wait Action](https://github.com/convictional/trigger-workflow-and-wait).

**Why use this Action?**

* Being a composite run steps Action, it is lightweight compared to the original Docker based Action.
* It adds checks to ensure that the correct workflow is identified and waited upon.
* It provides a link to the output of the remote workflow and shows details about the last commit in the branch/tag of the remote repo.

**When should you use it?**

When creating a workflow which deploys an entire distributed application in one go, spread across multiple repos. Since this Action waits for each workflow to complete, it will provide a holistic view of the deployment status in a single place.

## Pre-requisites

* The target workflow should have a Repository Dispatch trigger.
* A personal access token (PAT) will be needed to trigger the remote workflow.
    * The scope of the default GitHub token is limited to the current repository only. Hence, it cannot be used.
    * The PAT should have access to run workflows in the target repository.
    * GitHub guide for creating a PAT is available [here](https://docs.github.com/en/free-pro-team@latest/github/authenticating-to-github/creating-a-personal-access-token).
* Preferably, use this Action on a GitHub hosted runner. This is needed for avoiding issues arising from inconsistencies between timestamps returned from the runner and the GitHub API. This will not be a concern if the system clock on the self-hosted runner is accurate. 

## Inputs

| Name   | Required   | Default     | Description           |
| --------------- | ---------- | ----------- | --------------------- |
| `owner`           | True       | `null`         | The owner of the target repository where the workflow is to be triggered. |
| `repo`            | True       | `null`        | The target repository where the workflow is to be triggered. |
| `token `          | True       | `null`         | The Github access token with access to the target repository. Its recommended you put it under secrets. |
| `event_type`      | True       | `null`      | The event type that is configured in the repository dispatch trigger in the target workflow. |
| `wait_time`       | False      | `10`      | The number of seconds delay between checking for the result of the target workflow. |
| `max_time`        | False      | `600`    | Maximum amount of time to wait for workflow to complete (seconds). |
| `client_payload`  | False      | `"{}"`    | Payload for the repository dispatch event. Usually used for passing a Git Ref to the target workflow. |

## Outputs

| Name   | Description           |
| --------------- | --------------------- |
| `workflow-id`           | ID of the triggered workflow. |
| `conclusion`            | Result of the triggered workflow. |


## Example Usage

``` yaml
    - name: Deploy Your Service
      uses: adityakar/workflow-dispatcher
      with:
        owner: your-github-org
        repo: your-repo
        token: ${{ secrets.PAT }}
        event_type: dev-deploy
        client_payload: '{"git-ref": "${{ github.event.inputs.git-ref }}"}'
        wait_time: 5
        max_time: 180
```
