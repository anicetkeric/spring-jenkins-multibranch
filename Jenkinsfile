pipeline {
  agent any

  environment {
    MAVEN_ARGS=" -B -e -U"
  }

  stages {

        stage('Build') {
            steps {
                withMaven(maven: 'MAVEN_ENV') {
                    sh "mvn clean install -DskipTests=true -Dmaven.javadoc.skip=true -Dcheckstyle.skip=true ${MAVEN_ARGS}"
                }
            }
        }

        stage('Unit tests') {
            steps {
                withMaven(maven: 'MAVEN_ENV') {
                    sh "mvn clean test-compile surefire:test ${MAVEN_ARGS}"
                }
            }
        }

        stage('Integration tests') {
           steps {
                withMaven(maven: 'MAVEN_ENV') {
                    sh "mvn clean verify -Dskip.unit.tests=true ${MAVEN_ARGS}"
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
