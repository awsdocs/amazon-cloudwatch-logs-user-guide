# Stop the CloudWatch Logs Agent<a name="StopTheCWLAgent"></a>

Use the following procedure to stop the CloudWatch Logs agent on your EC2 instance\.

**To stop the agent**

1. Connect to your EC2 instance\. For more information, see [Connect to Your Instance](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2-connect-to-instance-linux.html) in the *Amazon EC2 User Guide for Linux Instances*\.

   For more information about connection issues, see [Troubleshooting Connecting to Your Instance](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/TroubleshootingInstancesConnecting.html) in the *Amazon EC2 User Guide for Linux Instances*\.

1. At a command prompt, type the following command:

   ```
   sudo service awslogs stop
   ```

   If you are running Amazon Linux 2, type the following command:

   ```
   sudo service awslogsd stop
   ```