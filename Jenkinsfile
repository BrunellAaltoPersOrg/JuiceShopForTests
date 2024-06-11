/*------------------------------------------------------------------------------
 File Name              : Jenkinsfile
 Template Author        : Bertrand Rochette (you@example.com)
 Template Creation Date : 2021/10/20
 Template Version       : 0.0.1
 Top : com
 Company : engie
 BU : <Bu>
 BE : <Be>
 Project : <project>
 Service Provider : <team>
 Description : Jenkinsfile for the project <project>
 Updates history :
 -------+--------+------------+------------------------------------------------
 Version|  Date  |   Author   | Description
 -------+--------+------------+------------------------------------------------
 0.0.1  |        |            | Initial code release
 -------+--------+------------+------------------------------------------------
        |        |            |
 -------+--------+------------+------------------------------------------------
------------------------------------------------------------------------------*/
//All the following variables have to be in lowercase
BU_NAME = "gbs"
BE_NAME = "engiedigital"
PROJECT_NAME = "walnut"
TEAM_NAME = "sqap"
MODULE_NAME = "sqap-testsPR"  // Name of the GitHub Repository
   
pipeline {
  /* insert Declarative Pipeline here */
    environment {
        SONARQUBE= credentials("${PROJECT_NAME}-${TEAM_NAME}-sonarqube")
        // The above line will automatically generate 3 environment variables : SONARQUBE (user:password), SONARQUBE_USR (user) and SONARQUBE_PSW (password)
        PROJECT_KEY = "com.engie.${BU_NAME}.${BE_NAME}-${PROJECT_NAME}.${TEAM_NAME}:${MODULE_NAME}"
        SONAR_URL = "https://sonar.tools.digital.engie.com/sonar"
        VERSION = "0.1"
        GITHUB_URL = "https://github.tools.digital.engie.com/GBSEngieDigitalSQAP/sqap-testsPR.git"
        GIT_CREDENTIALS= 'GITHUBrs6257'
        SONAR_SOURCES = "."
        scannerHome = tool "SonarQube 4.6.0.2311"   
    }
       
    agent {label "agent-walnut-shared"}
    stages {
        stage('Code Quality Analysis') {
            steps {
                container("walnut-shared") {
                  withSonarQubeEnv("${PROJECT_NAME}-${TEAM_NAME}-sonar") {
                     sh """
                        sonar-scanner -X \
                        -D sonar.login=${SONARQUBE_PSW} \
                        -D sonar.projectKey=${PROJECT_KEY} \
                        -D sonar.projectName=${PROJECT_NAME}_${MODULE_NAME} \
                        -D sonar.projectVersion=${VERSION} \
                        -D sonar.sources=${SONAR_SOURCES} \
                        -D sonar.sourceEncoding=UTF-8 \
                        -D sonar.links.scm=${GITHUB_URL} \
                        -D sonar.verbose=false
                    """
                  }
                }
            }
        }
        stage("SonarQube Quality Gate") {
            steps {
              container("walnut-shared") {  
                withSonarQubeEnv("${PROJECT_NAME}-${TEAM_NAME}-sonar") {
                    timeout(time: 10, unit: 'MINUTES') {
                        waitForQualityGate abortPipeline: false
                    }
                }
              } 
            }
        }
    }
}
