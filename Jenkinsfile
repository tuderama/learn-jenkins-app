pipeline {
    agent any

    environment {
        NETLIFY_SITE_ID = 'dd4a3d45-cf36-4501-89da-d19eec55628b'
        NETLIFY_AUTH_TOKEN = credentials('netlify-token')
    }

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
                apk add --no-cache bash
                node --version
                npm --version
                npm ci
                npm install netlify-cli
                npm run build
                '''
                stash includes: 'build/**,node_modules/**,package.json,package-lock.json', name: 'buildCache'
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
                unstash 'buildCache'
                sh '''
                apk add --no-cache bash
                test -f build/index.html
                npm test
                '''
            }
        }

        stage('Deploy') {
            agent {
                docker {
                    image 'node:18-alpine'
                    reuseNode true
                }
            }
            steps {
                unstash 'buildCache'
                sh '''
                apk add --no-cache bash
                echo "Deploying to production. Site ID: $NETLIFY_SITE_ID"
                node_modules/.bin/netlify --version
                node_modules/.bin/netlify status
                # Deploy hanya upload hasil build, tanpa build ulang di Netlify
                node_modules/.bin/netlify deploy --prod --dir=build
                '''
            }
        }
    }
}
