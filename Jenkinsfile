pipeline {
    agent any

    stages {
        stage('Clone Repository') {
            steps {
                git 'https://github.com/jenkins-docs/simple-node-js-react-npm-app.git'
            }
        }

        stage('Install Dependencies') {
            steps {
                sh 'npm install'
                sh 'npm audit fix || true'  // Fix vulnerabilities without failing the build
            }
        }

        stage('Test') {
            steps {
                sh 'npm test'
            }
        }

        stage('Create Dockerfile') {
            steps {
                writeFile file: 'Dockerfile', text: '''
                # Use the latest Node.js LTS version
                FROM node:18

                # Set the working directory
                WORKDIR /app

                # Copy the package.json and package-lock.json files
                COPY package*.json ./

                # Install app dependencies
                RUN npm install

                # Copy the rest of the application code
                COPY . .

                # Expose the port the app runs on
                EXPOSE 3000

                # Command to run the application
                CMD ["npm", "start"]
                '''
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    def dockerImage = docker.build("your-docker-repo/nodejs-app:latest")
                }
            }
        }

        stage('Push Docker Image') {
            steps {
                script {
                    docker.withRegistry('https://your-docker-repo-url', 'docker-credentials-id') {
                        dockerImage.push()
                    }
                }
            }
        }
    }

    post {
        always {
            cleanWs()  // Clean up the workspace after the build
        }
    }
}

