pipeline{
    agent any
    environment{
        DOCKER_IMAGE_NAME = "thebibang/testimage"
    }
    stages{
        stage("Build Application"){
            steps{
                echo "========executing Build Application========"
                sh '././gradlew build --no-daemon'
                archiveArtifacts artifacts: 'dist/trainSchedule.zip'
            }
            post{
                always{
                    echo "========always========"
                }
                success{
                    echo "========A executed successfully========"
                }
                failure{
                    echo "========A execution failed========"
                }
            }
        }
        stage("Build Docker Image of test app"){
            when {
                branch 'master'
            }
            steps {
                script {
                    app = docker.build(DOCKER_IMAGE_NAME)
                    app.inside {
                        sh 'echo hello, World!'
                    }
                }
                
            }
        }
        stage("Push Docker Image to my Repository"){
            when {
                branch 'master'
            }
            steps {
                script {
                    docker.withRegistry('https://registry.hub.docker.com', 'dockerhub') {
                        app.push("${env.BUILD_NUMBER}")
                        app.push("new")
                    }
                }

            }
        }
        stage ("Deploy to Production") {
            when {
                branch 'master'
            }
            steps {
                input 'Deploy to Production ?'
                milestone(1)
                kubernetesDeploy(
                    kubeconfigId: 'kubernetes',
                    configs: 'train-schedule-kube.yml',
                    enableConfigSubstitution: true
                )
            }
        }
    }
    post{
        always{
            echo "========always========"
        }
        success{
            echo "========pipeline executed successfully ========"
        }
        failure{
            echo "========pipeline execution failed========"
        }
    }
}
