// Inspired from
// https://raw.githubusercontent.com/csrrmrvll/some-exercises/main/Jenkinsfile

def litecoin_repo = 'https://github.com/csrrmrvll/some-exercises.git'

properties([
  parameters([
    string(name: 'litecoin_repo_branch', description: 'Branch to build and deploy Litecoin from', defaultValue: 'main'),
    string(name: 'image_tag', description: 'Image tag value.', defaultValue: '0.18.1'),
    string(name: 'image_repo_name', description: 'Full Docker image name with repository included.', defaultValue: 'c3542/litecoin'),
    string(name: 'image_tag', description: 'Full Docker image name with repository included.', defaultValue: 'c3542/litecoin'),
    string(name: 'kubeconfig', description: 'The name of the kubeconfig file in your Jenkins .kube directory', defaultValue: 'minikube')
  ])
])

throttle([]) {
  node() {
    timestamps {
      try {

        git url: litecoin_repo, branch: params.litecoin_repo_branch

        stage('Build') {
          sh """
            docker build -t ${params.image_repo_name}:${params.image_tag} .
            docker push ${params.image_repo_name}:${params.image_tag}
          """
        }

        stage('Deploy') {
          sh """
            kubectl --kubeconfig ~/.kube/${params.kubeconfig} apply -f litecoin-statefulset.yaml
          """
        }
      } catch (ex) {
        currentBuild.result = 'FAILURE'

        sh """
          echo FAILURE
        """
      }

    }
  }
}
