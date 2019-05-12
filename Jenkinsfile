pipeline {
    agent { label 'jenkins-slave-2' }
    options {
        buildDiscarder(logRotator(numToKeepStr: '5'))
        timeout(time: 10, unit: 'MINUTES')
        timestamps()  // Requires the "Timestamper Plugin"
    }
    triggers {
        pollSCM('H/5 * * * *')
    }
    stages {
        stage('Build') {
            steps {
                withEnv(["JAVA_HOME=${tool 'jdk1.8'}", "PATH+MAVEN=${tool 'M3'}/bin:${env.JAVA_HOME}/bin"]) {
                    sh 'java -version'

                    sh 'mvn --version'
                }
            }
        }
    }
}
