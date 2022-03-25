# Step 2: Update the existing destination access policy<a name="Cross-Account-Log_Subscription-Update-policy"></a>

After you have updated the subscription filters in all of the sender accounts, you can update the destination access policy in the recipient account\.

In the following examples, the recipient account is `999999999999` and the destination is named `testDestination`\.

The update enables all accounts that are part of the organization with ID `o-1234567890` to send logs to the recipient account\. Only the accounts that have subscription filters created will actually send logs to the recipient account\.

**To update the destination access policy in the recipient account to start using an organization ID for permissions**

1. In the recipient account, use a text editor to create a `~/AccessPolicy.json` file with the following contents\.

   ```
   { 
       "Version" : "2012-10-17", 
       "Statement" : [ 
           { 
               "Sid" : "", 
               "Effect" : "Allow",
               "Principal" : "*",
               "Action" : "logs:PutSubscriptionFilter", 
               "Resource" : "arn:aws:logs:region:999999999999:destination:testDestination",
               "Condition": {
                  "StringEquals" : {
                      "aws:PrincipalOrgID" : ["o-1234567890"]
                   }
               }
           } 
       ] 
   }
   ```

1. Enter the following command to attach the policy that you just created to the existing destination\. To update a destination to use an access policy with an organization ID instead of an access policy that lists specific AWS account IDs, include the `force` parameter\.
**Warning**  
If you are working with logs sent by an AWS service listed in [Enabling logging from certain AWS services](AWS-logs-and-resource-policy.md), then before doing this step, you must have first updated the subscription filters in all the sender accounts as explained in [Step 1: Update the subscription filters](Cross-Account-Log_Subscription-Update-filter.md)\.

   ```
   aws logs put-destination-policy 
       \ --destination-name "testDestination" 
       \ --access-policy file://~/AccessPolicy.json
       \ --force
   ```