pipeline{  
    agent any
    environment 
    {
        PROJECT_ID = 'wired-climate-262921'
        CLUSTER_NAME = 'k8-demo-6'
        LOCATION = 'us-central1-c'
        CREDENTIALS_ID = 'devops-pankaj'
    }
    stages 
    {
        stage("Checkout code") 
        {
            steps {
                checkout scm
            }
        }
        stage("Build") 
        {
            steps 
            {
                echo "cleaning and packaging"
                sh 'mvn package'
            }
        }
        stage("Test") 
        {
            steps 
            {
                echo "Testing"
                sh 'mvn test'
            }
        }
        stage("Build image") 
        {
            steps 
            {
                script 
                {
                     myapp = docker.build("gcr.io/wired-climate-262921/mywebappgcr:${env.BUILD_ID}")
                }
            }
        }
        stage("Push image") 
        {
            steps 
            {
                script 
                {
                    docker.withRegistry("https://gcr.io", "devops-pankaj")
                    {
                        myapp.push("${env.BUILD_ID}")
                    }                
                }
            }
        }
        stage('Deploy') 
        {
            steps
            {
                echo "Deployment started"
                sh 'ls -ltr'
                sh 'pwd'
                sh "sed -i 's/tagversion/${env.BUILD_ID}/g' deployment.yaml"
                step([$class: 'KubernetesEngineBuilder', projectId: env.PROJECT_ID, clusterName:
                env.CLUSTER_NAME, location: env.LOCATION, manifestPattern: 'deployment.yaml', credentialsId:
                env.CREDENTIALS_ID, verifyDeployments: true
                ])
                echo "Deployment Finished"
            }            
        }
    }
}
