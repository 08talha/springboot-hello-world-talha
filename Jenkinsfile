pipeline{
    agent any
    stages{
        stage('Git clone'){
            steps{
                git 'https://github.com/08talha/springboot-hello-world-talha.git'
            }
        }
        
        stage('maven build'){
            steps{
                bat 'mvn package'
            }
        }
        stage('Create Dockerimage'){
            steps{
                bat 'docker build -t springboot:latest .'
            }
        }
        stage('Deployment'){
            steps{
                bat '''@echo off
@REM kubectl get deployment springboot | findstr springboot
set deployment=""
set empty=''

echo Executing Command: kubectl get deployments -o=jsonpath='{$.items[0].spec.template.spec.containers[0].image}'
@FOR /f %%i in ('kubectl get deployment springboot') DO set deployment=%%i
echo Current Deployment %deployment%
echo Version to be deployed: %version%

kubectl get deployments -o=jsonpath='{$.items[0].spec.template.spec.containers[0].image}' > imageversion.csv
@FOR /F %%i in ('type imageversion.csv') DO set kubeVersion=%%i
echo Currently Deployed Version: %kubeVersion%

if %deployment% == "" (
	echo Deployment not found
	echo Executing command: kubectl apply -f %WORKSPACE%\deployments\deployment.yaml
	kubectl apply -f %WORKSPACE%\deployments\deployment.yaml
	
	if %version% == latest (
		echo Deployed the latest version of the build
	) else (
		echo Executing command: kubectl set image deployment springboot infotrends=vicky2911/springboot:%version%
		kubectl set image deployment springboot infotrends=vicky2911/springboot:%version%
		echo Deployed the %version% version of the build
	)
	echo Successfully deployed the service
	echo ------------------------------------------------------------
	echo Deployments:
	kubectl get deployment springboot
	echo Services Deployed:
	kubectl get service springboot
	echo ------------------------------------------------------------
) else (
	echo Deployment found
	
	if %version% == latest (

		if %kubeVersion%=='vicky2911/springboot:latest' (
			echo Executing Command: kubectl rollout restart deployment/springboot
			kubectl rollout restart deployment/springboot
		) else (
			echo Currently Older Version Of the Build is Deployed
			echo Executing command: kubectl set image deployment springboot infotrends=vicky2911/springboot:%version%
			kubectl set image deployment springboot infotrends=vicky2911/springboot:%version%
		)
		
	) else (
		echo Executing command: kubectl set image deployment springboot infotrends=vicky2911/springboot:%version%
		kubectl set image deployment springboot infotrends=vicky2911/springboot:%version%
	)
	echo Deployed the %version% version of the build
	echo Successfully redeployed the service
	echo ------------------------------------------------------------
	echo Deployments:
	kubectl get deployment springboot
	echo Services Deployed:
	kubectl get service springboot
	echo ------------------------------------------------------------
)'''
            }
        }

        
        
    }
}
