pipeline {
    agent { label 'JDK11' }
    options { 
        timeout(time: 1, unit: 'HOURS')
        // retry(2) 
    }
    triggers {
        cron('0 * * * *')
    }
    stages {
        stage('Source Code') {
            steps {
                git url: 'https://github.com/GitPracticeRepo/openmrs-core.git', 
                branch: 'master'
            }
            
        }
        stage('Artifactory-Configuration') {
            steps {
                rtMavenDeployer (
                    id: 'spc-deployer',
                    serverId: 'JFROG_INSTANCE',
                    releaseRepo: 'qtecomm-libs-release-local',
                    snapshotRepo: 'qtecomm-libs-snapshot-local',
                    
                )
            }
        }
         stage('Build the Code and sonarqube-analysis') {
             steps {
        //         withSonarQubeEnv('SONAR_LATEST') {
        //             sh script: "mvn package sonar:sonar"
        //         }
            rtMavenRun (
                    // Tool name from Jenkins configuration.
                    tool: 'MVN_DEFAULT',
                    pom: 'pom.xml',
                    goals: 'clean install',
                    // Maven options.
                    deployerId: 'spc-deployer',
                )
                
             }
         }
        stage('reporting') {
            steps {
                junit testResults: '**/surefire-reports/*.xml'
            }
        }
        // stage("Quality Gate") {
        //     steps {
        //       timeout(time: 1, unit: 'HOURS') {
        //         waitForQualityGate abortPipeline: true
        //       }
        //     }
        //   }
        
    }
    
}