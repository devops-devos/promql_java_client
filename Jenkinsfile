def SERVICE_GROUP = "devops"
def SERVICE_NAME = "promql_java_client"
def IMAGE_NAME = "${SERVICE_GROUP}-${SERVICE_NAME}"
def REPOSITORY_URL = "https://github.com/devops-devos/promql_java_client.git"
def BRANCH_NAME = "devos"


@Library("bespin-pipeline-library")
def butler = new com.bespin.devops.JenkinsPipeline()
def label = "maven-jdk8"

properties([
  buildDiscarder(logRotator(daysToKeepStr: "60", numToKeepStr: "30"))
])
podTemplate {
  node(label) {
    stage("Prepare") {
      container("builder") {
        butler.prepare(IMAGE_NAME)
      }
    }
    stage("Checkout") {
      container("builder") {
        butler.scan("java")
      }
    }
    stage("Build") {
      container("maven") {
        try {
          butler.mvn_build()
        } catch (e) {
          throw e
        }
      }
    }
    if (BRANCH_NAME == "master") {
      stage("deploy Plugin") {
        container("maven") {
          try {
            butler.mvn_deploy("", "-Dspotbugs.skip=true")
          } catch (e) {
            throw e
          }
        }
      }
    }
  }
}