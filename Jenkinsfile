pipeline {
    agent any
     
    stages {
        
        stage('Cleanup Workspace') {
            steps {
                cleanWs()
                sh """
                echo "Cleaned Up Workspace For Project"
                """
            }
        }

        stage('Build - dev or feature') {
            when {environment(name: "VERSIONED", value: 'true')}
            steps {
                sh 'docker rm -f feedback'
                sh '''echo "Pulling Repo"'''
                git 'http://github.com/meirya21/feedback.git'
                sh 'git checkput dev/${env.BRANCH}'
                sh 'git config pull.rebase false'
                sh 'git pull origin dev/${env.BRANCH}'
                sh '''echo v${env.BRANCH} > v.txt'''
                sh '''if [ -z $1 ]; then
	                $1=8000
                    fi
                    docker build -t feedback .'''
            }
        }

        stage('Build only') {
            when {environment(name: "VERSIONED", value: 'true')}
            steps {
                sh 'docker rm -f feedback'
                sh '''echo "Pulling Repo"'''
                git 'http://github.com/meirya21/feedback.git'
                sh 'git checkput dev/${env.BRANCH}'
                sh 'git config pull.rebase false'
                sh 'git pull origin dev/${env.BRANCH}'''
                sh '''echo v${env.BRANCH} > v.txt'''
                sh '''if [ -z $1 ]; then
	                $1=8000
                    fi
                    docker build -t feedback .'''
            }
        }

        stage('Test') {
            steps {
            sh 'python test.py'
            }
        }

        stage('Publish') {
            when {environment(name: "VERSIONED", value: 'true')}
                steps{
                    sh 'docker tag feed gcr.io/astral-archive-351007/feed'
                    sh 'docker push gcr.io/astral-archive-351007/feed:latest'
                }
        }

        stage('Tag') {
            sh 'docker push gcr.io/astral-archive-351007/feed:latest'
        }
        
        stage('Deploy'){
            when {environment(name: "VERSIONED", value: 'true')}
                steps{
                    sh 'docker run -d --name feedback -p 8000:${1} feed ${1}'
                }
            }
        }
    }

    post {
        always {
            emailext attachlog: true, to: 'yaa.meir@gmail.com', subject: 'LOG MAIL'
        }
    }
}