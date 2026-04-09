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

WRiting Workflows
on push
run echo "Hello world"