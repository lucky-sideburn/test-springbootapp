#!groovy​

properties([[$class: 'BuildDiscarderProperty', strategy: [$class: 'LogRotator', numToKeepStr: '10']]])

stage('build') {
    node(label: 'slave') {
        checkout scm
        def v = version()
        currentBuild.displayName = "${env.BRANCH_NAME}-${v}-${env.BUILD_NUMBER}"
        echo currentBuild.displayName
        mvn "clean install verify"
    }
}

stage('build docker image') {
    node(label: 'slave'){
      pom = readMavenPom file: 'pom.xml'
      pom.version
      repo_docker_base = "https://docker-registry-default.origin36.local/devopsguru/"
      echo "Versione Pom: " +  pom.version
      echo "Versione Artefatto: " + pom.artifactId
      jar_file = pom.artifactId + "-" + pom.version + ".jar"
      image_name =  pom.artifactId + ":" + pom.version
      sh "whoami;   env;    docker info"
      sh "docker build --build-arg JAR_FILE=target/" + jar_file + " . -t " + repo_docker_base + image_name
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
