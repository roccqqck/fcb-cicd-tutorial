### references
https://helm.sh/docs/helm/helm_template/


# oc 
```
        stage ('Helm Upgrade') {
            steps {
                sh 'oc login ${ocp} -u ${user} -p ${password} --insecure-skip-tls-verify'
                sh 'oc project ${project}'
                dir("${repository}") {
                    // sh "helm list"
                    // sh "helm uninstall ${repository} || true"
                    sh "helm list"
                    sh "helm upgrade --install -f deploy/helm/values-uat.yaml ${repository} deploy/helm/"
                    sh "helm list"
                }      
            }
        }    
```