pipeline {
    agent any
    parameters {
        string(name: 'AWS_REGIONS', defaultValue: 'us-east-1,us-west-2,ap-south-1,ap-northeast-1,eu-central-1', 
               description: 'Enter comma-separated AWS regions (e.g., us-east-1,us-west-2,ap-south-1)')
    }
    environment {
        AWS_CREDENTIALS_ID = 'e5a2e66c-c03a-4612-ba24-8e1f50b382d6' // Ensure this matches your Jenkins credentials ID
    }
    stages {
        stage('List AWS Resources') {
            steps {
                script {
                    // Using Jenkins credentials for AWS access
                    withCredentials([[$class: 'AmazonWebServicesCredentialsBinding', credentialsId: AWS_CREDENTIALS_ID]]) {
                        // Split user-provided regions into a list
                        def regions = params.AWS_REGIONS.tokenize(',')

                        // Iterate over each region provided by the user
                        for (region in regions) {
                            region = region.trim() // Remove extra spaces
                            echo "==========================="
                            echo "Processing region: ${region}"
                            echo "==========================="

                            // List EC2 Instances
                            echo "Listing EC2 Instances in ${region}"
                            sh """
                                aws ec2 describe-instances --region ${region} \
                                --query "Reservations[*].Instances[*].[InstanceId,InstanceType,State.Name,PublicIpAddress,PrivateIpAddress,Placement.AvailabilityZone]" \
                                --output table || echo "No instances found in ${region}"
                            """

                            // List EBS Volumes
                            echo "Listing EBS Volumes in ${region}"
                            sh """
                                aws ec2 describe-volumes --region ${region} \
                                --query "Volumes[*].[VolumeId,Size,State,AvailabilityZone,VolumeType,Attachments[0].InstanceId]" \
                                --output table || echo "No volumes found in ${region}"
                            """

                            // List Security Groups
                            echo "Listing Security Groups in ${region}"
                            sh """
                                aws ec2 describe-security-groups --region ${region} \
                                --query "SecurityGroups[*].[GroupId,GroupName,Description,VpcId]" \
                                --output table || echo "No security groups found in ${region}"
                            """

                            // List Elastic IPs
                            echo "Listing Elastic IPs in ${region}"
                            sh """
                                aws ec2 describe-addresses --region ${region} \
                                --query "Addresses[*].[PublicIp,AllocationId,Domain,InstanceId,NetworkInterfaceId]" \
                                --output table || echo "No Elastic IPs found in ${region}"
                            """
                        }
                    }
                }
            }
        }
    }
    post {
        always {
            echo 'Cleaning up temporary files...'
        }
        success {
            echo 'Job executed successfully!'
        }
        failure {
            echo 'Job failed. Please check the logs for more details.'
        }
    }
}
