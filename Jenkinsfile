node('tap4fun-jnlp') {
  stage('Clone') {
    echo "1.Clone Stage"
    git url: "https://github.com/maodahua/jenkins-demo.git"
    script {
        build_tag = sh(returnStdout: true, script: 'git rev-parse --short HEAD').trim()
    }
  }
  stage('Test') {
    echo "2.Test Stage"
  }
  stage('Build') {
    echo "3.Build Docker Image Stage"
    sh "docker build -t maodahua16/jenkins-demo:${build_tag} ."
  }
  stage('Push') {
    echo "4. Push Docker Image Stage"
    withCredentials([usernamePassword(credentialsId: 'dockerhub', passwordVariable: 'dockerHubPassword', usernameVariable: 'dockerHubUser')]) {
        sh "docker login -u ${dockerHubUser} -p ${dockerHubPassword}"
        sh "docker push maodahua16/jenkins-demo:${build_tag}"
    }
  }
  stage('Deploy') {
    echo "5. Change YAML File Stage"
    def userInput = input(
        id: 'userInput',
        message: 'Choose a deploy environment',
        parameters: [
            [
                $class: 'ChoiceParameterDefinition',
                choices: "Dev\nQA\nProd",
                name: 'Env'
            ]
        ]
    )
    echo "This is a deploy step to ${userInput}"
    sh "sed -i 's/<BUILD_TAG>/${build_tag}/' k8s.yaml"
    sh "sed -i 's/<BRANCH_NAME>/${userInput}/' k8s.yaml"
    if (userInput == "Dev") {
        //deploy dev
    } else if (userInput == "QA") {
        //deploy qa
    } else {
        //deploy prod
    }
    sh "kubectl apply -f k8s.yaml"
  }
}