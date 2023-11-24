pipeline {
    agent any
    
    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        // stage('Install tools') {
        //     agent { label 'n6c' }
        //     steps {
        //         echo 'Installing the required tools'
        //         ansiblePlaybook(
        //             playbook: '01.installations.yml',
        //             inventory: 'hosts.ini'
        //         )
        //     }
        // }

        stage('Build') {
            agent { label 'n6c' }
            steps {
                echo 'Building the webapp on the build server'
                ansiblePlaybook(
                    playbook: '02.build.yml',
                    inventory: 'hosts.ini'
                )
            }
        }

        stage('Test') {
            agent { label 'n6c' }
            steps {
                echo 'Testing the webapp on the build server'
                ansiblePlaybook(
                    playbook: '03.test.yml',
                    inventory: 'hosts.ini'
                )
            }
        }

        stage('Deploy Artifacts') {
            agent { label 'n6c' }
            steps {
                echo 'Copying the war file from the build server to tomcat servers'
                ansiblePlaybook(
                    playbook: '04.warfile.yml',
                    inventory: 'hosts.ini'
                )
            }
        }

        stage('Restart Tomcat') {
            agent { label 'n6c' }
            steps {
                echo 'Starting tomcat'
                ansiblePlaybook(
                    playbook: '05.restart.yml',
                    inventory: 'hosts.ini'
                )
            }
        }

        // stage('Clean Workspace') {
        //     agent { label 'n6c' }
        //     steps {
        //         echo 'Clean out workspace'
        //         ansiblePlaybook(
        //             playbook: '06.clean.yml',
        //             inventory: 'hosts.ini'
        //         )
        //     }
        // }
    }

    post {
        success {
            echo 'Pipeline succeeded! Send success notification.'
            mail to: 'olawalemada@gmail.com',
                 subject: "Success: ${currentBuild.fullDisplayName}",
                 body: "Build, test, and deployment were successful. Congratulations!"
        }
        failure {
            echo 'Pipeline failed! Send failure notification.'
            mail to: 'olawalemada@gmail.com',
                 subject: "Failed: ${currentBuild.fullDisplayName}",
                 body: "Something went wrong. Please check the build, test, and deployment logs."
        }
    }
}
