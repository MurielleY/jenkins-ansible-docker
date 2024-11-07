node {
    def registryProjet='registry.gitlab.com/murielley/presentations-jenkins/wartest'
    def IMAGE="${registryProjet}:version-${env.BUILD_ID}"
    
    stage('Build - Clone') { 
        git 'https://github.com/MurielleY/war-build-docker.git'
    }

    stage('Build - Maven Package') {
        sh 'mvn package'
    }
    def img = stage('Build') {
        docker.build("$IMAGE",  '.')
    }

    stage('Build - Test') {
        img.withRun("--name run-$BUILD_ID -p 8084:8080") { c ->
        sh 'docker ps'
        sh 'netstat -ntaup'
        sh 'sleep 15s'
        sh 'curl 127.0.0.1:8084'
        sh 'docker ps'
        }
    }
    stage ('Build - Push') {
        docker.withRegistry('https://registry.gitlab.com', 'reg1') {
        img.push('latest')
        img.push()
        }
    }
    stage('Deploy - Clone') {
        git 'https://github.com/MurielleY/jenkins-ansible-docker.git'
    }
    stage('Deploy - End') { // Exécuter le playbook Ansible
        ansiblePlaybook (
            colorized: true,
            become: true,
            playbook: 'playbook.yml',
            inventory: '${HOST}',
            credentialsId: 'jenkins',
            extraVars: [
                ansible_sudo_pass: '1234',
                IMAGE: "${IMAGE}"
            ]
        )
    }
}
