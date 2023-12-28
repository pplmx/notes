---
categories:
    - general
date: 2020-04-25T21:58:58Z
description: Let's start a simple schedule in github actions.
keywords: schedule, github actions
lastmod: 2020-11-11T16:31:07Z
tags:
    - schedule
    - github
title: How to set schedule by github actions?
---



# Github Actions: Schedule

Here is a trick to record a day the Miliky Way hasn't collided with the Andromeda Galaxy.

```yaml
name: Has the Milky Way collided with the Andromeda Galaxy?

# daily job
on:
    schedule:
        -
            cron: 0 0 * * *

# A workflow run is made up of one or more jobs that can run sequentially or in parallel
jobs:
    # This workflow contains a single job called "build"
    build:
        # The type of runner that the job will run on
        runs-on: ubuntu-latest

        # Steps represent a sequence of tasks that will be executed as part of the job
        steps:
            # Checks-out your repository under $GITHUB_WORKSPACE, so your job can access it
            -
                uses: actions/checkout@v2

            # Setup git
            -
                name: Setup Git Infomation
                run: |
                    git config --global user.name 'user'
                    git config --global user.email 'email'
            # Record (use record.sh or record2.sh)
            -
                name: Recording
                run: |
                    sh ./record.sh
            -
                name: Pushing
                run: |
                    git push https://${{github.actor}}:${{secrets.GITHUB_TOKEN}}@github.com/${{github.repository}}.git HEAD:${{ github.ref }} || echo "No changes to commit"

```

For the complete project, you can follow [here](https://github.com/pplmx/galaxy).
