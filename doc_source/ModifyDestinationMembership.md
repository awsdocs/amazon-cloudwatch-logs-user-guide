# Modifying Destination Membership at Runtime<a name="ModifyDestinationMembership"></a>

You might encounter situations where you have to add or remove membership of some users from a destination that you own\. You can use the **PutDestinationPolicy** action on your destination with new access policy\. In the following example, a previously added account **111111111111** is stopped from sending any more log data, and account **222222222222** is enabled\.

1. Fetch the policy that is currently associated with the destination **testDestination** and make a note of the **AccessPolicy**:

   ```
   aws logs describe-destinations \
       --destination-name-prefix "testDestination"
   
   {
    "Destinations": [
      {
        "DestinationName": "testDestination",
        "RoleArn": "arn:aws:iam::222222222222:role/CWLtoKinesisRole",
        "DestinationArn": "arn:aws:logs:region:222222222222:destination:testDestination",
        "TargetArn": "arn:aws:kinesis:region:222222222222:stream/RecipientStream",
        "AccessPolicy": "{\"Version\": \"2012-10-17\", \"Statement\": [{\"Sid\": \"\", \"Effect\": \"Allow\", \"Principal\": {\"AWS\": \"111111111111\"}, \"Action\": \"logs:PutSubscriptionFilter\", \"Resource\": \"arn:aws:logs:region:123456789012:destination:testDestination\"}] }"
      }
    ]
   }
   ```

1. Update the policy to reflect that account **111111111111** is stopped, and that account **222222222222** is enabled\. Put this policy in the **\~/NewAccessPolicy\.json** file:

   ```
   {
     "Version" : "2012-10-17",
     "Statement" : [
       {
         "Sid" : "",
         "Effect" : "Allow",
         "Principal" : {
           "AWS" : "222222222222"
         },
         "Action" : "logs:PutSubscriptionFilter",
         "Resource" : "arn:aws:logs:region:222222222222:destination:testDestination"
       }
     ]
   }
   ```

1. Call **PutDestinationPolicy** to associate the policy defined in the **NewAccessPolicy\.json** file with the destination:

   ```
   aws logs put-destination-policy \
   --destination-name "testDestination" \
   --access-policy file://~/NewAccessPolicy.json
   ```

   This will eventually disable the log events from account ID **111111111111**\. Log events from account ID **222222222222** start flowing to the destination as soon as the owner of account **222222222222** creates a subscription filter using **PutSubscriptionFilter**\.