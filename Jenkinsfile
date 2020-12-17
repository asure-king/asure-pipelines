pipeline {
    environment {
     JENKINS_BACKUP_FILE_NAME = ''
    }
    agent {
        label 'master'
    }
    stages {
        stage('Backing up Jenkins') {
            steps {
                script {
                    JENKINS_BACKUP_FILE_NAME = sh (
                        script: '''#!/bin/bash
                            # find latest backup
                            thinbackup_path="/opt/bitnami/jenkins/jenkins_home/thinBackup"
                            latest_backup_folder=$(ls -t $thinbackup_path | head -n1)
                            full_path="$thinbackup_path/$latest_backup_folder"
                           
                            # pack it up
                            backup_name="$latest_backup_folder.tar.gz"
                            tar cvzf $backup_name $full_path
                            echo ${backup_name}
                            ''',
                        returnStdout: true
                    ).trim().tokenize().last()
                }
                step([$class: 'ClassicUploadStep', credentialsId: 'asure-terraform-273003-serviceaccount',  
                bucket: "gs://asure-jenkins-backup",
                      pattern: JENKINS_BACKUP_FILE_NAME])
                sh '''
                 rm '''+JENKINS_BACKUP_FILE_NAME+'''
                '''
            }
        }
    }
}