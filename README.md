# JavaMavenTestngSelenideExample
Example of UI automation testing project

## Technologies:
- Java
- Maven
- TestNG
- Selenide
- Allure

## Pipeline example
```
pipeline {
    agent any
    tools {maven 'Maven'}

    stages {
        stage('Git checkout') {
            steps {
                git credentialsId: 'Crushpowerx',
                    branch: 'main',
                    url: 'https://github.com/Crushpowerx/JavaMavenTestngSelenideExample.git'
            }
        }
		stage('Prepare Selenoid') {
            steps {
                sh 'docker pull selenoid/chrome'
                sh 'chmod +x src/test/resources/selenoid_manager/cm'
				sh 'src/test/resources/selenoid_manager/cm selenoid start'
				sh 'src/test/resources/selenoid_manager/cm selenoid status'
				sh 'curl http://localhost:4444/status'
            }
        }
		stage('Chrome') {
            steps {
				sh 'mvn clean test "-Dsurefire.suiteXmlFiles=src/test/resources/TestNG.xml" "-Dtestng.dtd.http=true"'
            }
        }
    }
    
    post {
        always { 
            script {
                sh 'docker stop selenoid'
                sh 'docker rm selenoid'
                allure([
                    includeProperties: false,
                    jdk: '',
                    properties: [],
                    reportBuildPolicy: 'ALWAYS',
                    results: [[path: 'target/allure-results']]
                    ])
            }
        }
    }
    
}
```
