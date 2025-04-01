pipeline
{
	agent any
	
    tools
	{
		maven 'Maven_3.8.6'
    }
	
    environment
    {
		Build_Number = "${BUILD_NUMBER}"
    }
	
	stages
	{
		stage('Git Checkout')
		{
			steps()
			{
				git 'https://github.com/TestAutomationDevOps/maven-web-application.git'
			}
		}
		
		stage('Build Project')
		{
			steps()
			{
				sh 'mvn clean package'
			}
		}
		
		stage('Build Docker Image')
		{
			steps()
			{
				sh "docker build -t 873892298042.dkr.ecr.ap-south-1.amazonaws.com/java-maven-application:$Build_Number ."
			}
		}
		
		stage('Push Docker Image to AWS ECR')
		{
			steps()
			{
				sh "aws ecr get-login-password --region ap-south-1 | docker login --username AWS --password-stdin 873892298042.dkr.ecr.ap-south-1.amazonaws.com"
				sh "docker push 873892298042.dkr.ecr.ap-south-1.amazonaws.com/java-maven-application:$Build_Number"
			}
		}
		
		stage("Update Image Tag in Kubernetes Manifest")
		{
			steps()
            {
				//Build_Tag - To Be Added in Manifest File RegistryURL/java-maven-application:Build_Tag
				//Build_Number - Parameterised Build Number.
				sh "sed -i 's/Build_Tag/${Build_Number}/g' mavenwebappdeployment.yaml"
            }
        }
		
		stage('Deploy Application in EKS Kubernetes Cluster')
		{
			steps()
			{
				sh 'kubectl delete deployment springbootmongo-deployment -n production || true'
				sh 'kubectl apply -f SpringBootMongo.yaml'
			}
		}
	}
}