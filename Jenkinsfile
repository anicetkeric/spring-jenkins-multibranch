pipeline {
  agent any

  environment {
    MAVEN_ARGS=" -B -e -U clean org.jacoco:jacoco-maven-plugin:prepare-agent deploy"
  }

  stages {
        stage('Build') {
           steps {
                withMaven(maven: 'MAVEN_ENV') {
                    sh "mvn ${MAVEN_ARGS}"
                }
           }
          post {
              success {
                        junit allowEmptyResults: true, testResults: '**/*-reports/*.xml'
                    }
              }
        }
        stage('Code quality - sonar') {
            steps {
                sh """
                echo "Running sonar Analysis"
                """
            }
        }

        stage('OWASP Dependency-Check Vulnerabilities') {
            steps {
                dependencyCheck additionalArguments: '''
                            -o './'
                            -s './'
                            -f 'ALL'
                            --prettyPrint''', odcInstallation: 'OWASP Dependency-Check Vulnerabilities'

                dependencyCheckPublisher pattern: 'dependency-check-report.xml'
            }
        }
 }
}  
