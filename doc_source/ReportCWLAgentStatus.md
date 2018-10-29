# Report the CloudWatch Logs Agent Status<a name="ReportCWLAgentStatus"></a>

Use the following procedure to report the status of the CloudWatch Logs agent on your EC2 instance\.

**To report the agent status**

1. Connect to your EC2 instance\. For more information, see [Connect to Your Instance](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2-connect-to-instance-linux.html) in the *Amazon EC2 User Guide for Linux Instances*\.

   For more information about connection issues, see [Troubleshooting Connecting to Your Instance](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/TroubleshootingInstancesConnecting.html) in the *Amazon EC2 User Guide for Linux Instances*

1. At a command prompt, type the following command:

   ```
   sudo service awslogs status
   ```

   If you are running Amazon Linux 2, type the following command:

   ```
   sudo service awslogsd status
   ```

1. Check the **/var/log/awslogs\.log** file for any errors, warnings, or issues with the CloudWatch Logs agent\.