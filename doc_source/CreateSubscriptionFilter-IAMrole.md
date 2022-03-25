# Step 2: \(Only if using an organization\) Create an IAM role<a name="CreateSubscriptionFilter-IAMrole"></a>

In the previous section, if you created the destination by using an access policy that grants permissions to the organization that account `111111111111` is in, instead of granting permissions directly to account `111111111111`, then follow the steps in this section\. Otherwise, you can skip to [Create a subscription filter](CreateSubscriptionFilter.md)\.

The steps in this section create an IAM role, which CloudWatch can assume and validate whether the sender account has permission to create a subscription filter against the recipient destination\. 

**To create the IAM role necessary for cross\-account log subscriptions using AWS Organizations**

1. Create the following trust policy in a file `/TrustPolicyForCWLSubscriptionFilter.json`\. Use a text editor to create this policy file; do not use the IAM console\.

   ```
   {
     "Statement": {
       "Effect": "Allow",
       "Principal": { "Service": "logs.region.amazonaws.com" },
       "Action": "sts:AssumeRole"
     }
   }
   ```

1. Create the IAM role that uses this policy\. Take note of the `Arn` value that is returned by the command, you will need it later in this procedure\. In this example, we use `CWLtoSubscriptionFilterRole` for the name of the role we're creating\.

   ```
   aws iam create-role \ 
        --role-name CWLtoSubscriptionFilterRole \ 
        --assume-role-policy-document file://~/TrustPolicyForCWL.json
   ```

1. Create a permissions policy to define the actions that CloudWatch Logs can perform on your account\.

   1. First, use a text editor to create the following permissions policy in a file named `/PermissionsForCWLSubscriptionFilter.json`\.

      ```
      { 
          "Statement": [ 
              { 
                  "Effect": "Allow", 
                  "Action": "logs:PutLogEvents", 
                  "Resource": "arn:aws:logs:region:111111111111:log-group:LogGroupOnWhichSubscriptionFilterIsCreated:*" 
              } 
          ] 
      }
      ```

   1. Enter the following command to associate the permissions policy you just created with the role that you created in step 2\.

      ```
      aws iam put-role-policy 
          \ --role-name CWLtoSubscriptionFilterRole 
          \ --policy-name Permissions-Policy-For-CWL-Subscription-filter 
          \ --policy-document file://~/PermissionsForCWLSubscriptionFilter.json
      ```

When you have finished, you can proceed to [Create a subscription filter](CreateSubscriptionFilter.md)\.