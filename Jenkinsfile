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
                    args '-u root'
                }
            }
            steps {
                sh '''
                apk update && apk add --no-cache bash
                npm ci
                npm run build
                '''
                // LANGKAH BARU: Simpan direktori yang dibutuhkan untuk stage selanjutnya
                echo "Stashing node_modules and build directories..."
                stash name: 'build-artifacts', includes: 'build/, node_modules/'
            }
        }
        stage('Test'){
            agent {
                docker {
                    image 'node:18-alpine'
                    reuseNode true
                    args '-u root'
                }
            }
            steps {
                // LANGKAH BARU: Kembalikan file yang dibutuhkan untuk testing
                unstash 'build-artifacts'
                sh '''
                apk update && apk add --no-cache bash
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
                    args '-u root'
                }
            }
            steps {
                // LANGKAH BARU: Kembalikan direktori sebelum menjalankan perintah deploy
                echo "Unstashing artifacts..."
                unstash 'build-artifacts'

                sh '''
                apk update && apk add --no-cache bash
                node_modules/.bin/netlify --version
                echo "Deploying to production. Site ID: $NETLIFY_SITE_ID"
                node_modules/.bin/netlify status
                node_modules/.bin/netlify deploy --dir=build --prod
                '''
            }
        }
    }
}