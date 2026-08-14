---
categories:
    - ci
date: 2024-04-11T14:25:42Z
description: Get Patchset Number from a Gerrit Event in a Jenkins Pipeline
keywords:
    - ci
    - jenkins
    - gerrit
    - pipeline
    - groovy
lastmod: 2026-08-14T00:00:00Z
tags:
    - ci
    - jenkins
    - gerrit
title: How to get the Patchset Number from a Gerrit Event in a Jenkins Pipeline
---




# Retrieving the Patchset Number from a Gerrit Event

> **Note (2026-08-14):** The env var names used below are still injected by the current [Gerrit Trigger plugin](https://plugins.jenkins.io/gerrit-trigger/) — `GERRIT_REFSPEC`, `GERRIT_HOST`, `GERRIT_PORT`, `GERRIT_PROJECT`, and also `GERRIT_PATCHSET_NUMBER` / `GERRIT_CHANGE_NUMBER` (both confirmed present in the plugin source). `checkout scmGit(...)` remains the current recommended Declarative-pipeline git step.

## clone by http

```groovy
pipeline {
    agent any
    options { skipDefaultCheckout() }
    stages {
        stage('PatchSet Number') {
            steps {
                script {
                    // injected by the Gerrit Trigger plugin
                    if (!env.GERRIT_PATCHSET_NUMBER) {
                        error 'GERRIT_PATCHSET_NUMBER not set - is this job triggered by Gerrit Trigger?'
                    }
                    println "Change:    ${env.GERRIT_CHANGE_NUMBER}"
                    println "PatchSet:  ${env.GERRIT_PATCHSET_NUMBER}"
                    println "Refspec:   ${env.GERRIT_REFSPEC}"
                    currentBuild.description = "refs/changes/${env.GERRIT_CHANGE_NUMBER}/patchset ${env.GERRIT_PATCHSET_NUMBER}"
                }
            }
        }
        stage('Checkout PatchSet') {
            steps {
                checkout scmGit(
                    branches: [[name: 'FETCH_HEAD']],
                    extensions: [
                        cloneOption(
                            depth: 1,
                            shallow: true,
                            noTags: true,
                        )
                    ],
                    userRemoteConfigs: [[
                        refspec: '${GERRIT_REFSPEC}',
                        url: 'https://${GERRIT_HOST}/${GERRIT_PROJECT}'
                    ]]
                )
            }
        }
    }
}

```

## clone by ssh

```groovy
pipeline {
    agent any
    options { skipDefaultCheckout() }
    stages {
        stage('PatchSet Number') {
            steps {
                script {
                    if (!env.GERRIT_PATCHSET_NUMBER) {
                        error 'GERRIT_PATCHSET_NUMBER not set - is this job triggered by Gerrit Trigger?'
                    }
                    println "PatchSet: ${env.GERRIT_PATCHSET_NUMBER}"
                    println "Refspec:  ${env.GERRIT_REFSPEC}"
                }
            }
        }
        stage('Checkout PatchSet') {
            steps {
                checkout scmGit(
                    branches: [[name: 'FETCH_HEAD']],
                    extensions: [
                        cloneOption(
                            depth: 1,
                            shallow: true,
                            noTags: true,
                        ),
                        [$class: 'UserIdentity', email: 'jenkins@x.internal', name: 'Jenkins']
                    ],
                    userRemoteConfigs: [[
                        credentialsId: '<CRED_ID>',
                        refspec: '${GERRIT_REFSPEC}',
                        url: "ssh://jenkins@${GERRIT_HOST}:${GERRIT_PORT}/${GERRIT_PROJECT}"
                    ]]
                )
            }
        }
    }
}

```
