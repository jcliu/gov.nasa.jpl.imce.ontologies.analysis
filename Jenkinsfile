#!/usr/bin/env groovy

pipeline {
    agent any

    stages {
        stage('Checkout') {
            steps {
                echo "Checking out project from SCM..."

                checkout scm
            }
        }

        stage('Setup') {
            steps {
                echo "Setting up environment..."

                sh "${tool name: 'default-sbt', type: 'org.jvnet.hudson.plugins.SbtPluginBuilder$SbtInstallation'}/bin/sbt setupTools setupFuseki"
                sh ". workflow/env.sh"
            }
        }

        stage('Build') {
            steps {
                sh "${tool name: 'default-sbt', type: 'org.jvnet.hudson.plugins.SbtPluginBuilder$SbtInstallation'}/bin/sbt compile test:compile"
                archiveArtifacts artifacts: '**/target/*.jar', fingerprint: true
            }
        }

        stage('Validate-Ontologies') {
            steps {
                echo "Validating ontologies..."

                sh "workflow/make ${env.WORKFLOW}/Makefile"
                sh "workflow/make location-mapping"
                sh "workflow/make validate-roots"

                junit '**/target/*.xml'
            }
        }
    }
}
