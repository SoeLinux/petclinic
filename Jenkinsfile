node {
    //def mvnHome = tool name: 'Maven_3', type: 'maven'
    def mvnCli = "/usr/bin/mvn"

    stage('Checkout SCM'){
        git branch: 'master', credentialsId: 'github-creds', url: 'https://github.com/SoeLinux/petclinic.git'
    }
    stage('Read praram'){
        echo "The environment chosen during the Job execution is ${params.environment}"
        echo "$JENKINS_URL"
    }
    stage('maven compile'){
        // def mvnHome = tool name: 'Maven_3.6', type: 'maven'
        // def mvnCli = "${mvnHome}/bin/mvn"
        sh "${mvnCli} clean compile"
    }
    stage('maven package'){
        sh "${mvnCli} package -Dmaven.test.skip=true"
    }
    stage('Archive atifacts'){
        archiveArtifacts artifacts: '**/*.war', onlyIfSuccessful: true
    }
    stage('Archive Test Results'){
        junit allowEmptyResults: true, testResults: '**/surefire-reports/*.xml'
    }
    stage('Deploy To Tomcat'){
        sshagent(['app-server']) {
            sh 'scp -o StrictHostKeyChecking=no target/*.war ec2-user@ec2-52-70-39-48.compute-1.amazonaws.com:/opt/apache-tomcat-8.5.38/webapps/'
        }
    }
    stage('Smoke Test'){
        sleep 5
        sh "curl ec2-52-70-39-48.compute-1.amazonaws.com:8080/petclinic"
    }

}
