pipeline {
    agent any 

    stages {
        stage('Git Packer Template') { 
            steps { 
                sh 'rm -rvf bakery_base_azn'
                sh 'git clone https://github.com/jakehigg/bakery_base_azn.git' 
            }
        }
        stage('Bake') {
            steps {
                withCredentials([string(credentialsId: 'aws_access_key', variable: 'aws_access_key')]) {
                withCredentials([string(credentialsId: 'aws_secret_key', variable: 'aws_secret_key')]) {
                withCredentials([string(credentialsId: 'aws_access_key', variable: 'AWS_ACCESS_KEY_ID')]) {
                withCredentials([string(credentialsId: 'aws_secret_key', variable: 'AWS_SECRET_ACCESS_KEY')]) {
                sh '''
		rm -f manifest.json
		export latestbase = $(aws --region us-east-1 ssm get-parameter --name "latest-base" | jq -r  .Parameter.Value)
                packer build -var "aws_access_key=$aws_access_key" -var "aws_secret_key=$aws_secret_key" -var "source_ami=$latestbase" -var "vpc_id=vpc-04bea57d" -var "subnet_id=subnet-86bb84aa" bakery_base_azn/packer/bakery.json
		export newgold = $(cat manifest.json | jq -r .builds[0].artifact_id |  cut -d":" -f2)
		aws --region us-east-1 ssm put-parameter --name "latestgold" --value "$newgold" --type "String" --overwrite

                '''
                }}}}
               
                }
            }
        }
    }

