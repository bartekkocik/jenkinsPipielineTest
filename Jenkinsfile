@Library('falcon-pipeline-support') _

properties([[$class: 'BuildDiscarderProperty', strategy: [$class: 'LogRotator', artifactNumToKeepStr: '5', numToKeepStr: '5']]])

slackNotify('STARTED')

String name = 'falcon-kubernetes'
String tag

stage('checkout')
try {
    node {
        checkout scm
        tag = sh(returnStdout: true, script: 'git rev-parse --short HEAD').trim()
    }
} catch (e) {
    slackNotify('FAILED on checkout')
}

stage('test')
try {
    node {
        sh 'yamllint -d "{extends: default, rules: {comments-indentation: disable, line-length: disable, new-line-at-end-of-file: { level: warning }, indentation: { indent-sequences: no}}}" .'

    }
} catch (e) {
    slackNotify('FAILED on test')
    throw e
}

if (env.BRANCH_NAME == 'develop') {
    stage('rollout-develop')
    node {
        try {
            withKubectl('kubectl-config', 'dev.falcon') {
                sh "ls dev/*secret*yml | xargs -n 1 kubectl delete -f "
                sh "kubectl apply -f dev"
            }
        } catch (e) {
            slackNotify('FAILED on building docker image')
            throw e
        }
    }
}

if (env.BRANCH_NAME == 'master') {
    stage('promote-staging')
    milestone()
    input "Promote ${tag} to staging?"
    milestone()

    stage('rollout-staging')
    node {
        try {
            withKubectl('kubectl-config', 'staging.falcon') {
                sh "ls staging/*secret*yml | xargs -n 1 kubectl delete -f "
                sh "kubectl apply -f staging"
            }
        } catch (e) {
            slackNotify('FAILED deploying to staging')
            throw e
        }
    }

    stage('promote-production')
    milestone()
    input "Promote ${tag} to production?"
    milestone()

    stage('rollout-production')
    node {
        try {
            withKubectl('kubectl-config', 'prod.falcon') {
                sh "ls prod/*secret*yml | xargs -n 1 kubectl delete -f "
                sh "kubectl apply -f prod"
            }
        } catch (e) {
            slackNotify('FAILED deploying to production')
            throw e
        }
    }

}
slackNotify('SUCCESSFUL')
