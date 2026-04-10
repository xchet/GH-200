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

This step is where code becomes an artifact.

Publishing an npm package
Step 1: Configure package.json

{
  "name": "@OWNER/my-package",
  "publishConfig": {
    "registry": "https://npm.pkg.github.com"
  }
}

Step 2: Publish from GitHub Actions

- name: Publish npm package
  run: npm publish

Verify and amange published packages

# Create and publish custom GitHub actions

there are three types of custom actions

1. JavaScript actions - runs directly on the runner - (fastest)
2. Docker container actions - consistent environment - Linux only
3. Composite actions - combine existing workflow steps into one resusable action - no code need.

The action.yml metadata file - Every action requires an the action.yml file (action.yaml)
it defines - name, inputs, outputs and runs

Built a JavaScrip action
create a repo
add the action.yml
write index.js using the @actions/core and @actions/github package
Use core.getInput('name') - to read inputs 
core.setOutputs('key', value) - to emit outputs

Test your action locally -(do this first)
./
tag release - eg, v1.0.0

Publish to GitHub Marketplace
you action repo must be public

Manage Github Actions in Enterprise
GitHub enterprise has 2 models
1. GitHub Enterprise Cloud (GHEC) - hosted by GitHub, orgs lives under an enterprise account with a central billing and policy
2. GitHub Enterprise Server (GHES) - Self hosted on your infrastructure - full control, air-gap capable.

Enterprise account allows admis enforce policies accross all orgs from one place.

Managing actiona and access

Manage reusable component

Runners
GitHub hosted runner - fresh VMs per job - zero maintenance. They cannot access internal network.
Self Hosted runner - run on your own machine - they an reach internal database, artifact store, and private registries. - this is required for GHES an any job that  needs VPN/intranet access.

Secrets
this can be managed at three different levels
1. Repo - affect only the repo
2. Org - shared accross selected repos
3. Enterprise - accross all orgs

ref - ${{ secret.NAME }} - in all YAML files

Best practices 
rotate secrets regularly
limit org secrets to only the repos that needs them

