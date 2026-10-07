pipeline {

    agent any

    environment {

        DOCKER_USER = "aniketmedewar22"

        BACKEND_IMAGE = "${DOCKER_USER}/student-task-backend"

        FRONTEND_IMAGE = "${DOCKER_USER}/student-task-frontend"

    }

    stages {

        stage("Checkout") {

            steps {

                checkout scm

            }
        }

        stage("Build Backend") {

            steps {

                bat """
                docker build -t %BACKEND_IMAGE%:latest backend
                """

            }
        }

        stage("Build Frontend") {

            steps {

                bat """
                docker build -t %FRONTEND_IMAGE%:latest frontend
                """

            }
        }

        stage("Push Images") {

            steps {

                withCredentials([
                    usernamePassword(
                        credentialsId: "dockerhub",
                        usernameVariable: "DOCKER_USERNAME",
                        passwordVariable: "DOCKER_PASSWORD"
                    )
                ]) {

                    bat """

                    docker login -u %DOCKER_USERNAME% -p %DOCKER_PASSWORD%

                    docker push %BACKEND_IMAGE%:latest

                    docker push %FRONTEND_IMAGE%:latest

                    """

                }

            }
        }

        stage("Deploy") {
        steps {
            bat 'kubectl apply -f k8s\\ --kubeconfig="C:\\Users\\Lenovo\\.kube\\config"'
            bat 'kubectl rollout restart deployment backend frontend -n student-app --kubeconfig="C:\\Users\\Lenovo\\.kube\\config"'
        }
    }

        stage("Verify") {

            steps {

                bat """

                kubectl rollout status deployment/backend -n student-app

                kubectl rollout status deployment/frontend -n student-app

                kubectl get pods -n student-app

                """

            }
        }

    }

    post {

        success {

            echo "Deployment successful!"

        }

        failure {

            echo "Deployment failed!"

        }

    }
}
