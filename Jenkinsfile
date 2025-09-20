pipeline {
    agent any

    stages {
        /*

        stage('Build') {
            agent {
                docker {
                    image 'node:18-alpine'
                    reuseNode true
                }
            }
            steps {
                sh '''
                    ls -la
                    node --version
                    npm --version
                    npm ci
                    npm run build
                    ls -la
                '''
            }
        }
        */

        stage('Test') {
            agent {
                docker {
                    image 'node:18-alpine'
                    reuseNode true
                }
            }

            steps {
                sh '''
                    # Uncomment the line below if you want to test for a specific file
                    # test -f build/index.html
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
                    npx playwright test --reporter=html
                '''
            }
        }
    }

    post {
        always {
            // JUnit Report
            junit '**/jest-results/junit.xml'

            // Publish Playwright HTML report
            publishHTML(
                target: [
                    reportName: 'Playwright HTML Report',
                    reportDir: 'playwright-report',   // Ensure this is the directory with the HTML report
                    reportFiles: 'index.html',        // Ensure this file exists in the directory
                    alwaysLinkToLastBuild: false,
                    keepAll: false
                ]
            )
        }
    }
}
