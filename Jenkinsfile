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
                    // Match your devDependencies: v1.39.0
                    // We use 'jammy' (Ubuntu 22.04) because 'noble' didn't exist for v1.39.0
                    image 'mcr.microsoft.com/playwright:v1.39.0-jammy'
                    reuseNode true
                }
            }
            steps {
                sh '''
                    # 1. Install dependencies (Crucial because we switched from Alpine to Ubuntu)
                    npm install serve

                    # 2. Start your app
                    node_modules/.bin/serve -s build -l 3000 &

                    # 3. Wait for the server to be ready (installing wait-on on the fly)
                    npx wait-on http://localhost:3000 --timeout 30000

                    # 4. Run tests
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
