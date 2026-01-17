pipeline {
    agent any

    stages {
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
                    npm ci
                    npm run build
                    ls -la
                '''
            }
        }
        stage('Test') {
            agent {
                docker {
                    image 'node:18-alpine'
                    reuseNode true
                }
            }
            steps {
                sh '''
                    echo "Running tests..."
                    test -f build/index.html && echo "File exists" || echo "File does not exist"
                    npm test
                '''
            }
        }
        stage('E2E Tests') {
            agent {
                docker {
                    // Using a stable, existing version tag
                    image 'mcr.microsoft.com/playwright:v1.49.0-jammy'
                    reuseNode true
                }
            }
            steps {
                sh '''
                    npm install serve
                    node_modules/.bin/serve -s build &
                    npx wait-on http://localhost:3000
                    npx playwright test
                '''
            }
        }
    }
    post {
        always {
            junit '**/test-results/**/*.xml'
        }
    }
}
