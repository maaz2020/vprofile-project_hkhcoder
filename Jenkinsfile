pipeline {
    agent any

    tools {
        maven "MAVEN3"
        jdk "OracleJDK8"
    }

    environment {
        SNAP_REPO = 'vprofile-snapshot'
        NEXUS_USER = 'admin'
        NEXUS_PASS = 'admin123'
        RELEASE_REPO = 'vprofile-release'
        CENTRAL_REPOR = 'vprofile-maven-central'
        NEXUS_IP = '172.31.86.50'
        NEXUS_PORT = '8081'
        NEXUS_GRP_REPO = 'vprofile-maven-group'
        NEXUS_LOGIN = 'nexuslogin'
        SONAR_SERVER = 'sonarserver'
        SONAR_SCANNER = 'sonarscanner'
    }

    stages {

         stage('BUILD'){
            steps {
                sh 'mvn -s settings.xml -DskipTests install'
            }
            post {
                success {
                    echo 'Archiving the artifact now'
                    archiveArtifacts artifacts:'**/*.war'
                }
            }
    }
      stage('TEST'){
            steps {
                sh 'mvn -s settings.xml test'
            }  
    }
      stage('Checkstyle Analysis'){
            steps {
                sh 'mvn -s settings.xml checkstyle:checkstyle'
            }
    }
      stage('Sonar Analysis') {
            environment {
                scannerHome = tool "${SONAR_SCANNER}"
            }
            steps {
               withSonarQubeEnv("${SONAR_SERVER}") {
                   sh '''${scannerHome}/bin/sonar-scanner -Dsonar.projectKey=vprofile \
                   -Dsonar.projectName=vprofile \
                   -Dsonar.projectVersion=1.0 \
                   -Dsonar.sources=src/ \
                   -Dsonar.java.binaries=target/test-classes/com/visualpathit/account/controllerTest/ \
                   -Dsonar.junit.reportsPath=target/surefire-reports/ \
                   -Dsonar.jacoco.reportsPath=target/jacoco.exec \
                   -Dsonar.java.checkstyle.reportPaths=target/checkstyle-result.xml'''
              }
            }
        }
         stage("Quality Gate") {
            steps {
                sleep(60)
                timeout(time: 1, unit: 'MINUTES') {
                    // Parameter indicates whether to set pipeline to UNSTABLE if Quality Gate fails
                    // true = set pipeline to UNSTABLE, false = don't
                    waitForQualityGate abortPipeline: true
                }
            }
        }
        stage("UploadArtifact") {
           steps {
                nexusArtifactUploader(
                    nexusVersion: 'nexus3',
                    protocol: 'http',
                    nexusUrl: '${NEXUS_IP}:${NEXUS_PORT}',
                    groupId: 'QA',
                    version: '${env.BUILD_ID}-${BUILD_TIMESTAMP}',
                    repository: '${RELEASE_REPO}',
                    credentialsId: '${NEXUS_LOGIN}',
                    artifacts: [
                        [artifactId: 'vproapp',
                        classifier: '',
                        file: 'target/vprofile-v2.war',
                        type: 'war']
        ]
     )
            }
        }

}
}