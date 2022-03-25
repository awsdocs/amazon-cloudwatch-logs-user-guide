# Modifying destination membership at runtime<a name="ModifyDestinationMembershipFirehose"></a>

You might encounter situations where you have to add or remove log senders from a destination that you own\. You can use the **PutDestinationPolicy** action on your destination with new access policy\. In the following example, a previously added account **111111111111** is stopped from sending any more log data, and account **333333333333** is enabled\.

1. Fetch the policy that is currently associated with the destination **testDestination** and make a note of the **AccessPolicy**:

   ```
   aws logs describe-destinations \
       --destination-name-prefix "testFirehoseDestination"
   
   {
       "destinations": [
           {
               "destinationName": "testFirehoseDestination",
               "targetArn": "arn:aws:firehose:us-east-1:222222222222:deliverystream/my-delivery-stream",
               "roleArn": "arn:aws:iam:: 222222222222:role/CWLtoKinesisFirehoseRole",
               "accessPolicy": "{\n  \"Version\" : \"2012-10-17\",\n  \"Statement\" : [\n    {\n      \"Sid\" : \"\",\n      \"Effect\" : \"Allow\",\n      \"Principal\" : {\n        \"AWS\" : \"111111111111 \"\n      },\n      \"Action\" : \"logs:PutSubscriptionFilter\",\n      \"Resource\" : \"arn:aws:logs:us-east-1:222222222222:destination:testFirehoseDestination\"\n    }\n  ]\n}\n\n",
               "arn": "arn:aws:logs:us-east-1: 222222222222:destination:testFirehoseDestination",
               "creationTime": 1612256124430
           }
       ]
   }
   ```

1. Update the policy to reflect that account **111111111111** is stopped, and that account **333333333333** is enabled\. Put this policy in the **\~/NewAccessPolicy\.json** file:

   ```
   {
     "Version" : "2012-10-17",
     "Statement" : [
       {
         "Sid" : "",
         "Effect" : "Allow",
         "Principal" : {
           "AWS" : "333333333333 "
         },
         "Action" : "logs:PutSubscriptionFilter",
         "Resource" : "arn:aws:logs:us-east-1:222222222222:destination:testFirehoseDestination"
       }
     ]
   }
   ```

1. Use the following command to associate the policy defined in the **NewAccessPolicy\.json** file with the destination:

   ```
   aws logs put-destination-policy \
       --destination-name "testFirehoseDestination" \                                                                              
       --access-policy file://~/NewAccessPolicy.json
   ```

   This eventually disables the log events from account ID **111111111111**\. Log events from account ID **333333333333** start flowing to the destination as soon as the owner of account **333333333333** creates a subscription filter\.