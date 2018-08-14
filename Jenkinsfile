#!groovy​
agent { label 'docker' }

properties([[$class: 'BuildDiscarderProperty', strategy: [$class: 'LogRotator', numToKeepStr: '10']]])

stage('build') {
    node {
        checkout scm
        def v = version()
        currentBuild.displayName = "${env.BRANCH_NAME}-${v}-${env.BUILD_NUMBER}"
        echo currentBuild.displayName
        mvn "clean install verify"
    }
}

stage('build docker image') {
    node {
      pom = readMavenPom file: 'pom.xml'
      pom.version
      echo "Versione Pom: " +  pom.version
      sh "whoami;   env;    docker info"
      sh "docker build --build-arg JAR_FILE=target/gs-spring-boot-docker-0.1.0.jar . -t myspringboot:latest "
    }
}

def mvn(String goals) {
    def mvnHome = tool "Maven-3.5.4"
    def javaHome = tool "JDK1.8.0"

    withEnv(["JAVA_HOME=${javaHome}", "PATH+MAVEN=${mvnHome}/bin"]) {
        sh "mvn -B ${goals}"
    }
}

def version() {
    def matcher = readFile('pom.xml') =~ '<version>(.+)</version>'
    return matcher ? matcher[0][1] : null
}
