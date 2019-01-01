pipeline {
    // 1. runs in any agent, otherwise specify a slave node
    agent any
    parameters {
// 2.variables for the parametrized execution of the test: Text and options
        choice(choices: 'yes\nno', description: 'Are you sure you want to execute this test?', name: 'run_test_only')
        choice(choices: 'yes\nno', description: 'Archived war?', name: 'archive_war')
        string(defaultValue: "your.email@gmail.com", description: 'email for notifications', name: 'notification_email')
    }
//3. Environment variables
environment {
firstEnvVar= 'FIRST_VAR'
secondEnvVar= 'SECOND_VAR'
thirdEnvVar= 'THIRD_VAR'
}
//4. Stages
    stages {
        stage('Test'){
             //conditional for parameter
            when {
                environment name: 'run_test_only', value: 'yes'
            }
            steps{
                sh 'cd examples/java-calculator && mvn clean integration-test'
            }
        }
//5. demo parallel stage with script
        stage ('Run demo parallel stages') {
steps {
        parallel(
        "Parallel stage #1":
                  {
                  //running a script instead of DSL. In this case to run an if/else
                  script{
                    if (env.run_test_only =='yes')
                        {
                        echo env.firstEnvVar
                        }
                    else
                        {
                        echo env.secondEnvVar
                        }
                  }
         },
        "Parallel stage #2":{
                echo "${thirdEnvVar}"
                }
                )
             }
        }
    }
//6. post actions for success or failure of job. Commented out in the following code: Example on how to add a node where a stage is specifically executed. Also, PublishHTML is also a good plugin to expose Cucumber reports but we are using a plugin using Json.
    
post {
        success {
        //node('node1'){
echo "Test succeeded"
            script {
    // configured from using gmail smtp Manage Jenkins-> Configure System -> Email Notification
    // SMTP server: smtp.gmail.com
    // Advanced: Gmail user and pass, use SSL and SMTP Port 465
    // Capitalized variables are Jenkins variables – see https://wiki.jenkins.io/display/JENKINS/Building+a+software+project
                mail(bcc: '',
                     body: "Run ${JOB_NAME}-#${BUILD_NUMBER} succeeded. To get more details, visit the build results page: ${BUILD_URL}.",
                     cc: '',
                     from: 'jenkins-admin@gmail.com',
                     replyTo: '',
                     subject: "${JOB_NAME} ${BUILD_NUMBER} succeeded",
                     to: env.notification_email)
                     if (env.archive_war =='yes')
                     {
             // ArchiveArtifact plugin
                        archiveArtifacts '**/java-calculator-*-SNAPSHOT.jar'
                      }
                       // Cucumber report plugin
                      cucumber fileIncludePattern: '**/java-calculator/target/cucumber-report.json', sortingMethod: 'ALPHABETICAL'
            //publishHTML([allowMissing: false, alwaysLinkToLastBuild: false, keepAll: true, reportDir: '/home/reports', reportFiles: 'reports.html', reportName: 'Performance Test Report', reportTitles: ''])
            }
        //}
        }
        failure {
            echo "Test failed"
            mail(bcc: '',
                body: "Run ${JOB_NAME}-#${BUILD_NUMBER} succeeded. To get more details, visit the build results page: ${BUILD_URL}.",
                 cc: '',
                 from: 'jenkins-admin@gmail.com',
                 replyTo: '',
                 subject: "${JOB_NAME} ${BUILD_NUMBER} failed",
                 to: env.notification_email)
                 cucumber fileIncludePattern: '**/java-calculator/target/cucumber-report.json', sortingMethod: 'ALPHABETICAL'
//publishHTML([allowMissing: true, alwaysLinkToLastBuild: false, keepAll: true, reportDir: '/home/tester/reports', reportFiles: 'reports.html', reportName: 'Performance Test Report', reportTitles: ''])
        }
    }
}
