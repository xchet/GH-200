# Automate dev tasks using GitHub Actions

What is GitHub Actions?
An event-driven automation platform built into GitHub.

The 4 core components
1. Workflow - YAML file that defines everything
2. Job - a group of steps that runs on one machine
3. Step - a single command or action
4. Actions - a reseable unit of work.

YAML Workflow structure

File lives at .github/workflows/name.yml
Key fields: on: (trigger event), jobs: (what to run), steps: (individual tasks). Use uses: for pre-built actions and run: for shell commands.

Writing Workflows
Set trigger as on: push. Add one job. First step should always be uses: actions/checkout@v3 to pull repo code onto the runner. Add a second step with run: echo "Hello World" to confirm it works.

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
ii. Use `needs:build` - on a job to force it to wait for another.
iii. Use `needs:test`

Buils a three stage pipeline - build > test (`needs:test`) > report (`needs:report`)

# Buil and deploy applications to Azure
## How HitHub authenticates with Azure
The two options are Publish profile and Service Principle - You can store either of these in GitHub Secrets.

1. Publish profile  (download from Azure portal, paste as a secret ) - simpler for app service
2. Service Principle (JSON - more flexible)

Store and use Azure credentials
In a repo >  Settings > Secrets and Varables > actions

Status badges and artifact clean up

Envrinment setup

# Automate GitHub by using GitHub Script
What is GitHu Scripts?
the action that lets you write inline JS code indode your YAML - this calls HitHubs' OctoKit API directly.

We have two key objects  - github and context
1. github is the authenticated OctoKit client-  it  calls any GitHub API
2. context - this is the event payload - (repo name, other details, etc)

Common automation tasks
Auto-comment - github.issues.createComment()
Add label - github.rest.issues.addLabels()
close stale issues - github.rest.issues.update({state: 'closed'})
Post summary - uses:actions/

Pass output 
core.setOutput('mykey', value)

${{steps.MY_STEP_ID.outputs.mykey}}

# Leverage GitHub Actions to publish to GitHub packages

What is Github pacakges?
Is a packeage registry buillt directly into GitHub
It allows you to store, version and share your software package in the same place where your source code already lives.

Supported package types
npm - JavaScript/TypeScript
Maven - Java/JVM
NuGet - .NET
RudyGems
Containers (via GitHub container registry) GHCR

Access model
Access is controlled by GitHub authentication
Permission inheritance is from repository access/organization roles.
No seaparate user account is needed - npmjs.org or Docker Hub

Authenticate in the Workflow
Authentication is where hitHub Packages really shines.

GITHUB_TOKEN

Every GitHub Actions workflow automatically gets: ${{ secret.GITHUB_TOKEN}}

You do not need to:
1. Create personal access tokens
2. Store passwords
3. Manage secrets manually (in most cases)

This token:
1. Is short-lived
2. Is scoped to the repo
3. Has permissions you define in the workflow

For GHCR (Docker Images)

You explicitly login - ref: .github/workflows/ghcr.yml

Build and publish a package
