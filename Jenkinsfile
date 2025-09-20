pipeline {
    agent any

    stages {
        stage('Test') {
            agent {
                docker {
                    image 'node:18-alpine'
                    reuseNode true
                }
            }
            steps {
                sh '''
                    # Run tests with Jest and output to a JUnit XML file
                    npm test -- --testResultsProcessor="jest-junit" --outputFile=jest-results/junit.xml
                '''
            }
        }

        stage('E2E') {
            agent {
                docker {
                    image 'mcr.microsoft.com/playwright:v1.39.0-jammy'
                    reuseNode true
                }
            }
            steps {
                sh '''
                    npm install serve
                    node_modules/.bin/serve -s build & 
                    sleep 10
                    npx playwright test
                '''
            }
        }
    }

    post {
        always {
            junit '**/jest-results/junit.xml'  // Path to the Jest test results XML
        }
    }
}
