pipeline {
    agent any
    parameters {
        string(name: 'AWS_REGIONS', defaultValue: 'us-east-1,us-west-1', description: 'Enter a comma-separated list of AWS regions (e.g., us-east-1,us-west-2)')
    }
    stages {
        stage('List AWS Resources') {
            steps {
                script {
                    // Split the input regions into a list
                    def regions = params.AWS_REGIONS.split(',')
                    
                    // Iterate over each region and fetch details
                    for (region in regions) {
                        echo "Fetching details for region: ${region}"
                        
                        echo "Listing EC2 Instances in region: ${region}"
                        sh """
                        aws ec2 describe-instances --region ${region.trim()} --query "Reservations[*].Instances[*].[InstanceId,InstanceType,State.Name,PublicIpAddress,PrivateIpAddress,Placement.AvailabilityZone]" --output table
                        """
                        
                        echo "Listing EBS Volumes in region: ${region}"
                        sh """
                        aws ec2 describe-volumes --region ${region.trim()} --query "Volumes[*].[VolumeId,Size,State,AvailabilityZone,VolumeType,Attachments[0].InstanceId]" --output table
                        """
                        
                        echo "Listing Security Groups in region: ${region}"
                        sh """
                        aws ec2 describe-security-groups --region ${region.trim()} --query "SecurityGroups[*].[GroupId,GroupName,Description,VpcId]" --output table
                        """
                        
                        echo "Listing Elastic IPs in region: ${region}"
                        sh """
                        aws ec2 describe-addresses --region ${region.trim()} --query "Addresses[*].[PublicIp,AllocationId,Domain,InstanceId,NetworkInterfaceId]" --output table
                        """
                    }
                }
            }
        }
    }
    post {
        always {
            echo 'Cleanup completed.'
        }
        success {
            echo 'Job executed successfully!'
        }
        failure {
            echo 'Job failed. Please check the logs for details.'
        }
    }
}
