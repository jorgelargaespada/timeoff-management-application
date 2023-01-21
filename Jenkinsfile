pipeline {
    agent none

    stages {

        stage('build'){
            agent {
                docker{
                    image 'node:13.0.1-buster-slim'
                    args '-p 5001:3000'   
                    }
                }
            }
            steps{
                echo 'Compiling app..'
                sh 'npm install'
            }
            
        }
        stage('Docker Package'){
            agent any
            steps{
                echo 'Building dev app..'
                script {
                        docker.withRegistry('https://index.docker.io/v1/', 'dockerlogin'){
                        def timeimage = docker.build("jlargaespada/worker:v${env.BUILD_ID}", ".")
                        timeimage.push()
                        timeimage.push("${env.BRANCH_NAME}")
                        timeimage.push("latest")
                }
                sh 'npm install'
                sh 'npm start &'
                input message: 'Finished using the web site? (Click "Proceed" to continue)' 
            }
            
        }
    //     stage('terraform init'){
    //         steps{
    //             dir ("infra"){
    //             sh 'terraform init'
    //         }}
    //     }

    //     stage('terraform plan'){
    //         steps{
    //              dir ("infra"){
    //             sh 'terraform plan -out=infra.out'
    //         }}
    //     }

    //     stage('Waiting for Approvals'){
    //         steps{
    //              dir ("infra"){
    //             input('Plan Validated? Please approve' )
    //         }}
    //     }

    //     stage('terraform Apply'){
    //         steps{
    //              dir ("infra"){
    //             sh 'terraform apply infra.out'
    //         }}
    //     }
    }
     post {
        always {
            echo "Pipeline for InstaApp run is complete.."
        }
        failure {
		slackSend (channel: "timeoff-management-application", message: "Build failure - ${env.JOB_NAME} ${env.BUILD_NUMBER} (<${env.BUILD_URL}|Open>)")
        }
        success {
		slackSend (channel: "timeoff-management-application", message: "Build succeeded - ${env.JOB_NAME} ${env.BUILD_NUMBER} (<${env.BUILD_URL}|Open>)")
        }
    }
}
