pipeline {
    agent any
    stages {
        // Package the application using Maven
        stage('Package') {
            steps {
                // Checkout code from the main branch
                checkout scm
                // Run Maven to package the application, skipping unit tests
                sh 'mvn -B -DskipTests clean package'
            }
        }
        // Build the Docker image
        stage('Building image') {
            steps {
                script {
                    dockerImage = docker.build("sreynytha/teedy:latest")
                }
            }
        }
                stage('K8s') {
            steps {
                sh 'kubectl set image deployments/hello-node docs=sreynytha/teedy:latest'
                }
            }
    
        // Upload the Docker image to Docker Hub
        stage('Upload image') {
            steps {
                script {
                    docker.withRegistry('https://registry.hub.docker.com', '12113053') {
                        dockerImage.push("latest")
                    }
                }
            }
        }
        // Run Docker containers
        stage('Run containers') {
            steps {
                script {
                    // Correctly run the container using the dockerImage object
                    def runArgs = '-d -p 8090:8080'
                    def container1 = dockerImage.run(runArgs)
                    def container2 = dockerImage.run(runArgs.replace("8090", "8088"))
        

                    // Optionally, you can manage the lifecycle of these containers as needed
                    // sleep 30
                    // container1.stop()
                    // container2.stop()
                    // container3.stop()
                }
            }
        }
    }
}
