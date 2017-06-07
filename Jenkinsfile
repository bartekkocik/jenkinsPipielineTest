
properties([[$class: 'BuildDiscarderProperty', strategy: [$class: 'LogRotator', artifactNumToKeepStr: '5', numToKeepStr: '5']]])


stage('checkout')
try {
    node {
        checkout scm
    }
} catch (e) {
    throw e
}

stage('validate')
try {
    node {
        sh 'yamllint -d "{extends: default, rules: {comments-indentation: disable, line-length: disable, new-line-at-end-of-file: { level: warning }, indentation: { indent-sequences: no}}}" .'
    }
} catch (e) {
    throw e
}

if (env.BRANCH_NAME == 'master') {

    stage('promote-staging')

    input "Promote to staging?"


    stage('rollout-staging')
    node {
        try {
            sh "git checkout staging"
            sh "git merge -s theirs master"
            sh "git push origin HEAD"
        } catch (e) {
            throw e
        }
    }

    stage('promote-production')

    input "Promote to production?"


    stage('rollout-production')
    node {
        try {
            sh "git checkout production"
            sh "git merge -s theirs staging"
            sh "git push origin HEAD"
        } catch (e) {
            throw e
        }
    }
}

