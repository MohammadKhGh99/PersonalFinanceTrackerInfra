pipeline {
    agent {
        label 'general'
    }

    parameters {
        string(name: 'SERVICE_NAMES', defaultValue: '', description: 'Comma-separated list of service names')
        string(name: 'IMAGE_FULL_NAMES', defaultValue: '', description: 'Comma-separated list of image full names corresponding to the services')
    }

    stages {
        stage('Deploy') {
            steps {
                script {
                    def serviceNames = SERVICE_NAMES.split(',')
                    def imageFullNames = IMAGE_FULL_NAMES.split(',')
                    
                    if (serviceNames.size() != imageFullNames.size()) {
                        error("The number of service names must match the number of image full names.")
                    }

                    for (int i = 0; i < serviceNames.size(); i++) {
                        def serviceName = serviceNames[i].trim()
                        def imageFullName = imageFullNames[i].trim()
                        
                        sh """
                           cd k8s/${serviceName}
                           sed -i 's|image: .*|image: ${imageFullName}|' ${serviceName}-deployment.yaml
                        """
                    }
                    withCredentials([usernamePassword(credentialsId: 'github', usernameVariable: 'GIT_USERNAME', passwordVariable: 'GIT_PASSWORD')]) {
                        sh """
                            cd k8s
                            git config --global user.name "MohammadKhGh99"
                            git config --global user.email "mohammad.gh454@gmail.com"
                            git add .
                            git commit -m 'Update images of services ${SERVICE_NAMES}'
                            git push https://${GIT_USERNAME}:${GIT_PASSWORD}@github.com/MohammadKhGh99/PersonalFinanceTrackerInfra.git HEAD:main
                        """
                    }
                }
            }
        }
    }
}