#!/usr/bin/env groovy

node {
    stage('checkout') {
        checkout scm
    }

    stage('check java') {
        sh "java -version"
    }

    stage('clean') {
        sh "chmod +x mvnw"
        sh "./mvnw clean"
    }

    stage('install tools') {
        sh "./mvnw com.github.eirslett:frontend-maven-plugin:install-node-and-npm -DnodeVersion=v10.15.0 -DnpmVersion=6.4.1"
    }

    stage('npm install') {
        sh "./mvnw com.github.eirslett:frontend-maven-plugin:npm"
    }

//    stage('backend tests') {
//        try {
//            sh "./mvnw test"
//        } catch(err) {
//            throw err
//        } finally {
//            junit '**/target/surefire-reports/TEST-*.xml'
//        }
//    }

//    stage('frontend tests') {
//        try {
//            sh "./mvnw com.github.eirslett:frontend-maven-plugin:npm -Dfrontend.npm.arguments='run test'"
//        } catch(err) {
//            throw err
//        } finally {
//            junit '**/target/test-results/TESTS-*.xml'
//        }
//    }

    stage('packaging') {
        sh "./mvnw verify -Pprod -DskipTests"
        archiveArtifacts artifacts: '**/target/*.war', fingerprint: true
    }

//    stage('quality analysis') {
//        withSonarQubeEnv('sonar') {
//            sh "./mvnw sonar:sonar"
//        }
//    }

//    def dockerImage
//    stage('build docker') {
//        sh "cp -R src/main/docker target/"
//        sh "cp target/*.war target/docker/"
//        dockerImage = docker.build('docker-login/testapp', 'target/docker')
//    }

    stage('build docker') {
        sh "cp -R src/main/docker target/"
        sh "cp target/*.war target/docker/"
        sh "docker build -t testapp ./target/docker/"
    }

//    stage('publish docker') {
//        docker.withRegistry('https://registry.hub.docker.com', 'docker-login') {
//            dockerImage.push 'latest'
//        }
//    }


    stage('start docker') {
        sh "docker-compose -f ./target/docker/app.yml down"
        sh "docker-compose -f ./target/docker/app.yml up -d"
    }
}
