# Create an authentication token for IAM authentication to an Amazon RDS database using an AWS SDK<a name="example_rds_GenerateRDSAuthToken_section"></a>

The following code example shows how to create an authentication token for IAM authentication\.

**Note**  
The source code for these examples is in the [AWS Code Examples GitHub repository](https://github.com/awsdocs/aws-doc-sdk-examples)\. Have feedback on a code example? [Create an Issue](https://github.com/awsdocs/aws-doc-sdk-examples/issues/new/choose) in the code examples repo\. 

------
#### [ Java ]

**SDK for Java 2\.x**  
 There's more on GitHub\. Find the complete example and learn how to set up and run in the [AWS Code Examples Repository](https://github.com/awsdocs/aws-doc-sdk-examples/tree/main/javav2/example_code/rds#readme)\. 
Use the [RdsUtilities](https://sdk.amazonaws.com/java/api/latest/software/amazon/awssdk/services/rds/RdsUtilities.html) class to generate an authentication token\.  

```
public class GenerateRDSAuthToken {

    public static void main(String[] args) {

        final String usage = "\n" +
            "Usage:\n" +
            "    <dbInstanceIdentifier> <masterUsername>\n\n" +
            "Where:\n" +
            "    dbInstanceIdentifier - The database instance identifier. \n" +
            "    masterUsername - The master user name. \n";

            if (args.length != 2) {
                System.out.println(usage);
                System.exit(1);
            }

            String dbInstanceIdentifier = args[0];
            String masterUsername = args[1];
            Region region = Region.US_WEST_2;
            RdsClient rdsClient = RdsClient.builder()
                .region(region)
                .credentialsProvider(ProfileCredentialsProvider.create())
                .build();

            String token = getAuthToken(rdsClient, dbInstanceIdentifier, masterUsername);
            System.out.println("The token response is "+token);
    }

    public static String getAuthToken(RdsClient rdsClient, String dbInstanceIdentifier, String masterUsername ) {

        RdsUtilities utilities = rdsClient.utilities();
        try {
            GenerateAuthenticationTokenRequest tokenRequest = GenerateAuthenticationTokenRequest.builder()
                .credentialsProvider(ProfileCredentialsProvider.create())
                .username(masterUsername)
                .port(3306)
                .hostname(dbInstanceIdentifier)
                .build();

                return utilities.generateAuthenticationToken(tokenRequest);

        } catch (RdsException e) {
            System.out.println(e.getLocalizedMessage());
            System.exit(1);
        }
        return "";
   }
}
```
+  For API details, see [GenerateRDSAuthToken](https://docs.aws.amazon.com/goto/SdkForJavaV2/rds-2014-10-31/GenerateRDSAuthToken) in *AWS SDK for Java 2\.x API Reference*\. 

------

For a complete list of AWS SDK developer guides and code examples, see [Using this service with an AWS SDK](CHAP_Tutorials.md#sdk-general-information-section)\. This topic also includes information about getting started and details about previous SDK versions\.