pipeline {
    agent any

    stages {
        stage('Clone') {
            steps {
                echo 'Cloning source code'
                git branch: 'main', url: 'https://github.com/Lehaibac05/PlanCraft_test.git'
            }
        }

        stage('Install dependencies') {
            steps {
                echo 'Installing npm packages'
                bat 'npm install'
            }
        } 
        
        // stage('Run') {
        //     steps {
        //         echo 'Run application' 
        //         bat 'npm run dev'
        //     }
        // }
//asdfasd
        stage('Build Docker Image') {
            steps {
                echo 'Building Docker image'
                bat 'docker build -t plantcraft .'
            }
        }
    
        // stage('Test') {
        //     steps {
        //         echo 'Running tests'
        //         // Nếu có test thì bỏ comment dòng dưới
        //         bat 'npm test'
        //     }
        // }

        stage('Deploy') {
            steps {
                echo 'Deploying application'
                // Nếu muốn chạy container, thêm lệnh dưới:
                bat 'docker run -d -p 3000:3000 --name plancraft_container -e DB_HOST=mysql -e DB_USER=public -e DB_PASSWORD=123456 -e DB_NAME=plancraft_db plantcraft'
                // bat 'npm start'
            }
        } 
    }
}