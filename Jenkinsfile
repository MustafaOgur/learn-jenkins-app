pipeline {
    agent any

    environment{
        REACT_APP_VERSION = "1.0.$BUILD_ID"
        AWS_DEFAULT_REGION = 'us-east-2'
    }

    stages {
        
        stage('Build') {
            agent{
                docker{
                    image 'node:18-alpine'
                    reuseNode true
                }
            }
            steps {
                sh '''
                    echo "Small change"
                    npm ci
                    npm run build
                '''
            }
        }

        stage('Deploy to AWS') {
            agent{
                docker{
                    image 'amazon/aws-cli'
                    args "--entrypoint=''"
                    reuseNode true
                }
            }
            
            environment{
                AWS_S3_BUCKET = 'learn-jenkins-19102025'
            }

            steps {
                withCredentials([usernamePassword(credentialsId: 'my-aws', passwordVariable: 'AWS_SECRET_ACCESS_KEY', usernameVariable: 'AWS_ACCESS_KEY_ID')]) {
                    sh '''
                        aws --version
                        aws ecs register-task-definition --cli-input-json file://aws/task-definition.json --region $AWS_DEFAULT_REGION
                        aws ecs update-service --cluster LearnJenkinsApp-Cluster-Prod --service LearnJenkinsApp-TaskDefinition-Prod-service-h425laeu --task-definition LearnJenkinsApp-TaskDefinition-Prod:2
                    '''
                }
            }
        }


        // stage('Run Tests') {
        //     parallel {
        //         stage('Unit Test') {
        //             agent {
        //                 docker {
        //                     image 'node:18-alpine'
        //                     reuseNode true
        //                 }
        //             }
        //             steps {
        //                 sh '''
        //                     test -f build/index.html
        //                     npm test
        //                 '''
        //             }
        //             post {
        //                 always {
        //                     junit 'jest-results/junit.xml'
        //                 }
        //             }
        //         }

        //         stage('E2E') {
        //             agent {
        //                 docker {
        //                     image 'my-playwright'
        //                     reuseNode true
        //                 }
        //             }
        //             steps {
        //                 sh '''
        //                     serve -s build &
        //                     sleep 10
        //                     npx playwright test --reporter=html
        //                 '''
        //             }
        //             post {
        //                 always {
        //                     publishHTML([
        //                         allowMissing: false,
        //                         alwaysLinkToLastBuild: false,
        //                         icon: '',
        //                         keepAll: false,
        //                         reportDir: 'playwright-report',
        //                         reportFiles: 'index.html',
        //                         reportName: 'Playwright Local',
        //                         reportTitles: '',
        //                         useWrapperFileDirectly: true
        //                     ])
        //                 }
        //             }
        //         }
        //     }
        // }


        // stage('Deploy staging') {
        //     agent {
        //         docker {
        //             image 'my-playwright'
        //             reuseNode true
        //         }
        //     }

        //     environment{
        //         CI_ENVIRONMENT_URL = 'STAGING_URL_TO_BE_SET'
        //     }

        //     steps {
        //         sh '''
        //             echo "Deploying to production. Site ID: $NETLIFY_SITE_ID"
        //             netlify status
        //             netlify deploy --no-build --dir=build --json > deploy-output.json
        //             CI_ENVIRONMENT_URL=$(jq -r '.deploy_url' deploy-output.json)
        //             npx playwright test --reporter=html
        //         '''
        //     }
        //     post {
        //         always {
        //             publishHTML([
        //                 allowMissing: false,
        //                 alwaysLinkToLastBuild: false,
        //                 icon: '',
        //                 keepAll: false,
        //                 reportDir: 'playwright-report',
        //                 reportFiles: 'index.html',
        //                 reportName: 'Staging E2E',
        //                 reportTitles: '',
        //                 useWrapperFileDirectly: true
        //             ])
        //         }
        //     }
        // }

        // stage('Deploy prod') {
        //     agent{
        //         docker{
        //             image 'my-playwright'
        //             reuseNode true
        //         }
        //     }

        //     environment{
        //         CI_ENVIRONMENT_URL='https://warm-capybara-9dc19c.netlify.app/'
        //     }

        //     steps {
        //         sh '''
        //             node --version
        //             echo "Deploying to production. Site ID: $NETLIFY_SITE_ID"
        //             netlify status
        //             netlify deploy --no-build --dir=build --prod
        //             npx playwright test --reporter=html
        //         '''
        //     }

        //     post {
        //         always {
        //             publishHTML([
        //                 allowMissing: false,
        //                 alwaysLinkToLastBuild: false,
        //                 icon: '',
        //                 keepAll: false,
        //                 reportDir: 'playwright-report',
        //                 reportFiles: 'index.html',
        //                 reportName: 'Prod E2E',
        //                 reportTitles: '',
        //                 useWrapperFileDirectly: true
        //             ])
        //         }
        //     }
        // }
    }
}
