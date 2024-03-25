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
    }

    stages {

         stage('BUILD'){
            steps {
                sh 'mvn -s settings.xml -DskipTests install'
            }
    }

}
}