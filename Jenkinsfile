pipeline {
    agent any
options { buildDiscarder(logRotator(numToKeepStr: '1')) }
    stages {
        stage('checkout from git') {
            steps {
                checkout([$class: 'GitSCM', branches: [[name: '*/master']], extensions: [], userRemoteConfigs: [[url: 'https://github.com/Avenish180/hello-world-servlet.git']]])
            }
        }
        stage('Maven build') {
            steps {
                sh 'clean package cobertura:cobertura -Dcobertura.report.format=xml'
            }
        }
 stage('Sonarqube') {
    environment {
        scannerHome = tool 'sonarqube'
    }
    steps {
        withSonarQubeEnv('sonarqube') {
            sh "${scannerHome}/bin/sonar-scanner"
        }
  //      timeout(time: 10, unit: 'MINUTES') {
 //           waitForQualityGate abortPipeline: true
  //      }
    }
}
 
      stage('nexus upload') {
            steps {
                nexusPublisher nexusInstanceId: '1234', nexusRepositoryId: 'Hello-world-servlet', packages: [[$class: 'MavenPackage', mavenAssetList: [[classifier: '', extension: '', filePath: 'target/helloworld.war']], mavenCoordinate: [artifactId: 'hello-world-servlet-example', groupId: 'com.geekcap.vmturbo', packaging: 'war', version: '$BUILD_NUMBER']]]
            }
        }
        stage('deploy to tomcat') {
            steps {
                deploy adapters: [tomcat8(credentialsId: 'tomcat-cred', path: '', url: 'http://3.142.42.92:8080/')], contextPath: 'HelloWorldServlet', war: '**/*.war'
            }
        }
    }
}
post{
sucess{
cobertura autoUpdateHealth: false, autoUpdateStability: false, coberturaReportFile: '**/target/site/cobertura/coverage.xml', conditionalCoverageTargets: '70, 0, 0', failUnhealthy: false, failUnstable: false, lineCoverageTargets: '80, 0, 0', maxNumberOfBuilds: 0, methodCoverageTargets: '80, 0, 0', onlyStable: false, sourceEncoding: 'ASCII', zoomCoverageChart: false
junit '**/target/surefire-reports/*.xml'
      }
    }
