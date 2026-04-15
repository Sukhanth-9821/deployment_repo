pipeline{
    agent any
    parameters {
        string(name: "IMAGE_TAG", defaultValue: "70", description: "Enter Image Tag:")
    }
    environment{
        GIT_REPO = "https:///${gituser}:${gitpasswd}@github.com/Sukhanth-9821/deployment_repo.git"
        BRANCH = 'master'
        FILE_PATH = 'gistapi/values.yaml'
    }
    stages{
        stage ("SCM Clone"){
            steps{
                git 'https://github.com/Sukhanth-9821/deployment_repo.git'
            }

        }
        stage ("Update Version"){
            steps{
                sh """ sed -i 's/tag: .*/tag: ${IMAGE_TAG}/g' ${FILE_PATH} 
                cat ${FILE_PATH} 
                """
            }

        }
        stage ("git push"){
            steps{
            withCredentials([usernamePassword(credentialsId: 'gitCred', passwordVariable: 'gitpasswd', usernameVariable: 'gituser')]) {


            sh """ 

            git add ${FILE_PATH} 
            git commit -m "Update image tag to ${IMAGE_TAG}" || echo "No changes to commit" 
            git push "https:///${gituser}:${gitpasswd}@github.com/Sukhanth-9821/deployment_repo.git" ${BRANCH} 
            
            """

                }
            }
        }
        stage ("Argo deployment"){
            steps{
                sh """
                echo "Argo URL:https://localhost:8090/applications/argocd/gistapi-repo-app"
                echo "Deploying using Argocd"
                """
            }
        }
        stage ("deployment validation"){
            steps{
                sh """
                kubectl get po -n gistapi
                kubectl rollout status deployment gistapi -n gistapi
                """
            }
        }
    }
}