pipeline {
    agent any

    environment{
        NETLIFY_SITE_ID='dd4a3d45-cf36-4501-89da-d19eec55628b'
        NETLIFY_AUTH_TOKEN = credentials('netlify-token')
    }

    stages {
        stage('Build') {
            agent {
                docker {
                    image 'node:18-alpine'
                    reuseNode true
                    // TAMBAHKAN BARIS INI UNTUK MENJALANKAN SEBAGAI ROOT
                    args '-u root'
                }
            }
            steps {
                sh '''
                apk add --no-cache bash
                ls -la
                node --version
                npm --version
                npm ci
                npm run build
                ls -la
                '''
            }
        }
        stage('Test'){
            agent {
                docker {
                    image 'node:18-alpine'
                    reuseNode true
                    // TAMBAHKAN JUGA DI SINI
                    args '-u root'
                }
            }
            steps {
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
                    // TAMBAHKAN JUGA DI SINI
                    args '-u root'
                }
            }
            steps {
                sh '''
                apk add --no-cache bash
                node_modules/.bin/netlify --version
                echo "Deploying to production. Site ID: $NETLIFY_SITE_ID"
                node_modules/.bin/netlify status
                node_modules/.bin/netlify deploy --dir=build --prod
                '''
            }
        }
    }
}