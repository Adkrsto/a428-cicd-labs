pipeline {
    agent {
        docker {
            image 'node:16-buster-slim'
            args '-p 3000:3000'
        }
    }
    stages {
        stage('Build') {
            steps {
                sh 'npm install'
            }
        }
        stage('Test') { 
            steps {
                sh './jenkins/scripts/test.sh' 
            }
        }
        stage('Manual Approval') {
            steps {
                script {
                    def userInput = input(
                        id: 'userInput',
                        message: 'Lanjutkan ke tahap Deploy?',
                        parameters: [choice(name: 'ACTION', choices: ['Proceed', 'Abort'], description: 'Pilih tindakan')],
                        submitter: 'user'
                    )

                    if (userInput == 'Abort') {
                        currentBuild.result = 'ABORTED'
                        error('Pipeline dihentikan oleh pengguna.')
                    }
                }
            }
        }
        stage('Deploy') {
            steps {
                sh './jenkins/scripts/deliver.sh'
                sh 'sleep 60' // Menunggu hingga 1 menit
                sh './jenkins/scripts/kill.sh'
            }
        }
    }
}
