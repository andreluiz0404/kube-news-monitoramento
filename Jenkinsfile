pipeline
{
	agent any
	
	stages
	{
		// CI snippet
		stage ('Build Docker Image')
		{
			steps
			{
				script
				{
					dockerapp = docker.build("aluizsys/kube-news:v${env.BUILD_ID}", '-f ./src/Dockerfile ./src')
				}
			}
		}
		
		stage ('Push Docker Image')
		{
			steps
			{
				script
				{
					docker.withRegistry('https://registry.hub.docker.com', 'dockerhub')
					{
						dockerapp.push('latest')
						dockerapp.push("v${env.BUILD_ID}")
					}
				}
			}
		}

		// CD snippet
		stage ('Deploy Kubernetes')
		{
			environment { tag_version = "${env.BUILD_ID}" }
			steps
			{
				withKubeConfig([credentialsId: 'kube_config'])
				{
					sh 'sed -i "s/{{TAG}}/$tag_version/g" ./k8s/deployment.yaml'
					sh 'kubectl apply -f ./k8s/deployment.yaml'
				}
			}
		}
	}
}