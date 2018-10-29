# Start the CloudWatch Logs Agent<a name="StartTheCWLAgent"></a>

If the CloudWatch Logs agent on your EC2 instance did not start automatically after installation, or if you stopped the agent, you can use the following procedure to start the agent\.

**To start the agent**

1. Connect to your EC2 instance\. For more information, see [Connect to Your Instance](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2-connect-to-instance-linux.html) in the *Amazon EC2 User Guide for Linux Instances*\.

   For more information about connection issues, see [Troubleshooting Connecting to Your Instance](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/TroubleshootingInstancesConnecting.html) in the *Amazon EC2 User Guide for Linux Instances*\.

1. At a command prompt, type the following command:

   ```
   sudo service awslogs start
   ```

   If you are running Amazon Linux 2, type the following command:

   ```
   sudo service awslogsd start
   ```