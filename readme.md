# Automate dev tasks using GitHub Actions

What is GitHub Actions?
An event-driven automation platform built into GitHub.

The 4 core components
1. Workflow - YAML file that defines everything
2. Job - a group of steps that runs on one machine
3. Step - a single command or action
4. Actions - a reseable unit of work.

YAML Workflow structure

Issue Tracker
    .github/workflow/name.yml
    Other directories/sub/sub1
    Other2
    .env
    .lint

Writing Workflows
on push
run echo "Hello world"

# Build continuous integration (CI) workflows

What is CI here? - we used to hear about CI/CD
Automatically building and testing codes on every request (push/pull).

Environment variables and secrets.
defines the values taht are reusable in the codes/solution - can be handy during deployment
define static vars with env: VAR_EMAIL: value m- (dev@domain.dev)
For sensitive value - (API keys, password, secret) - store using secret - access a secrete - ${{ secret.MY_SECRETE }}
Never hardcode credentials in YML

Artifacts - persist build output
`action/upload-artifact` - to save files (test reports, build binaries)
`action/download-artifact` - in a later to retrieve files

Artifact appear on the workflow summary page for download

Chain job with needs:
i. By default all jobs run in parallel.
ii. Use `need:build` - on a job to force it to wait for another.
iii. Use `need:test`

Buils a three stage pipeline - build > test (`needs:test`) > report (`needs:report`)

# Buil and deploy applications to Azure
## How HitHub authenticates with Azure
The two options are Publish profile and Service Principle - You can store either of these in GitHub Secrets.

1. Publish profile  (download from Azure portal, paste as a secret ) - simpler for app service
2. Service Principle (JSON - more flexible)

Store and use Azure credentials
In a repo >  Settings > Secrets and Varables > actions

Status badges and artifact clean up
