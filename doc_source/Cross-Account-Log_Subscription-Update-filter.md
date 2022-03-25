# Step 1: Update the subscription filters<a name="Cross-Account-Log_Subscription-Update-filter"></a>

**Note**  
This step is needed only for cross\-account subscriptions for logs that are created by the services listed in [Enabling logging from certain AWS services](AWS-logs-and-resource-policy.md)\. If you are not working with logs created by one of these log groups, you can skip to [ Step 2: Update the existing destination access policy](Cross-Account-Log_Subscription-Update-policy.md)\.

In certain cases, you must update the subscription filters in all the sender accounts that are sending logs to the destination account\. The update adds an IAM role, which CloudWatch can assume and validate that the sender account has permission to send logs to the recipient account\.

Follow the steps in this section for every sender account that you want to update to use organization ID for the cross\-account subscription permissions\.

In the examples in this section, two accounts, `111111111111` and `222222222222` already have subscription filters created to send logs to account `999999999999`\. The existing subscription filter values are as follows:

```
## Existing Subscription Filter parameter values
    \ --log-group-name "my-log-group-name" 
    \ --filter-name "RecipientStream" 
    \ --filter-pattern "{$.userIdentity.type = Root}" 
    \ --destination-arn "arn:aws:logs:region:999999999999:destination:testDestination"
```

If you need to find the current subscription filter parameter values, enter the following command\.

```
aws logs describe-subscription-filters 
    \ --log-group-name "my-log-group-name"
```

**To update a subscription filter to start using organization IDs for cross\-account log permissions**

1. Create the following trust policy in a file `/TrustPolicyForCWL.json`\. Use a text editor to create this policy file; do not use the IAM console\.

   ```
   {
     "Statement": {
       "Effect": "Allow",
       "Principal": { "Service": "logs.region.amazonaws.com" },
       "Action": "sts:AssumeRole"
     }
   }
   ```

1. Create the IAM role that uses this policy\. Take note of the `Arn` value of the `Arn` value that is returned by the command, you will need it later in this procedure\. In this example, we use `CWLtoSubscriptionFilterRole` for the name of the role we're creating\.

   ```
   aws iam create-role 
       \ --role-name CWLtoSubscriptionFilterRole 
       \ --assume-role-policy-document file://~/TrustPolicyForCWL.json
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

1. Enter the following command to update the subscription filter\.

   ```
   aws logs put-subscription-filter 
       \ --log-group-name "my-log-group-name" 
       \ --filter-name "RecipientStream" 
       \ --filter-pattern "{$.userIdentity.type = Root}" 
       \ --destination-arn "arn:aws:logs:region:999999999999:destination:testDestination"
       \ --role-arn "arn:aws:iam::111111111111:role/CWLtoSubscriptionFilterRole"
   ```