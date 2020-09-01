pipeline {
      agent {
    kubernetes {
      yaml """
apiVersion: v1
kind: Pod
spec:
  containers:
  - name: kubectl
    image: bryandollery/terraform-packer-aws-alpine
    command:
    - cat
    tty: true
  - name: helm
    image: alpine/helm
    command:
    - cat
    tty: true
"""
    }
  }
  environment {
    TOKEN=credentials('cffc1cc9-775e-4507-86b8-54433e85e3ae')
  }
    stages {
      stage("Deploy") {
          steps {
              container('kubectl') {
                  sh '''
		      kubectl --token=$TOKEN create namespace elf
                      #kubectl --token=$TOKEN taint nodes k3d-labs-agent-1 key:NoExecute
                  '''
              }
          }
      }
        stage('helm-deploy') {
	  steps {
                  container('helm') {
                      sh '''
  			helm repo add elastic https://helm.elastic.co
			helm repo add fluent https://fluent.github.io/helm-charts
			helm repo update
			helm install elasticsearch elastic/elasticsearch --version=7.9.0 --namespace=elf -f elf-toleration.yaml
			helm install fluent-bit fluent/fluent-bit --namespace=elf -f elf-toleration.yaml
			helm install kibana elastic/kibana --version=7.9.0 --namespace=elf --set service.type=NodePort -f elf-toleration.yaml
		    '''
                    }
            }
        }
    }
}

