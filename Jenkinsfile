#!/usr/bin/env groovy
import groovy.json.JsonOutput
import groovy.transform.Field

def gitCommit = ""
def author = ""
def errorString
def commit

node {
    try {
        timestamps {
            stage('Checkout') {
                checkout scm
                gitCommit = sh(script: "git log -1 --pretty=%B", returnStdout: true).trim().replace("'", "")
                commit = sh(returnStdout: true, script: 'git rev-parse HEAD')
                author = sh(returnStdout: true, script: "git --no-pager show -s --format='%an' ${commit}").trim()

                gitBranch = scm.branches[0].name.replaceAll("/", "_")
                echo "My branch is: ${env.BRANCH_NAME}"

            }

             stage('Stop applications') {
                    sh "docker-compose down -v"
            }
           
            stage('deploy') {
                    sh "docker-compose up -d"
            }
        }
    } catch (hudson.AbortException ae) {
        echo "hudson.AbortException"
        currentBuild.result = currentBuild.result == null ? 'FAILURE' : currentBuild.result
    } catch (e) {
        currentBuild.result = 'FAILURE'
        def stringWriter = new StringWriter()
        e.printStackTrace(new PrintWriter(stringWriter))
        errorString = stringWriter.toString()
        echo "Error : ${errorString}"
    }

    currentBuild.result = currentBuild.result == null ? 'SUCCESS' : currentBuild.result
    def jobName = "${env.JOB_NAME}"
    def buildColor = "danger"
    def buildStatus = "Failed"
    if (currentBuild.result == null || currentBuild.result == "SUCCESS") {
        buildColor = "good"
        buildStatus = "Success"
    } else if (currentBuild.result == "UNSTABLE") {
        buildColor = "warning"
        buildStatus = "Unstable"
    } else if (currentBuild.result == "ABORTED") {
        buildColor = "#E8E9EB"
        buildStatus = "Aborted"
    }
}



