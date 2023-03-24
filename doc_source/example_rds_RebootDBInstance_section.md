# Reboot an Amazon RDS DB instance using an AWS SDK<a name="example_rds_RebootDBInstance_section"></a>

The following code example shows how to reboot an Amazon RDS DB instance\.

**Note**  
The source code for these examples is in the [AWS Code Examples GitHub repository](https://github.com/awsdocs/aws-doc-sdk-examples)\. Have feedback on a code example? [Create an Issue](https://github.com/awsdocs/aws-doc-sdk-examples/issues/new/choose) in the code examples repo\. 

------
#### [ Java ]

**SDK for Java 2\.x**  
 There's more on GitHub\. Find the complete example and learn how to set up and run in the [AWS Code Examples Repository](https://github.com/awsdocs/aws-doc-sdk-examples/tree/main/javav2/example_code/rds#readme)\. 
  

```
    public static void rebootInstance(RdsClient rdsClient, String dbInstanceIdentifier ) {

        try {
            RebootDbInstanceRequest rebootDbInstanceRequest = RebootDbInstanceRequest.builder()
               .dbInstanceIdentifier(dbInstanceIdentifier)
               .build();

            RebootDbInstanceResponse instanceResponse = rdsClient.rebootDBInstance(rebootDbInstanceRequest);
            System.out.print("The database "+ instanceResponse.dbInstance().dbInstanceArn() +" was rebooted");

        } catch (RdsException e) {
            System.out.println(e.getLocalizedMessage());
            System.exit(1);
        }
   }
```
+  For API details, see [RebootDBInstance](https://docs.aws.amazon.com/goto/SdkForJavaV2/rds-2014-10-31/RebootDBInstance) in *AWS SDK for Java 2\.x API Reference*\. 

------

For a complete list of AWS SDK developer guides and code examples, see [Using this service with an AWS SDK](CHAP_Tutorials.md#sdk-general-information-section)\. This topic also includes information about getting started and details about previous SDK versions\.