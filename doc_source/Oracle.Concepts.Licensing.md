# RDS for Oracle licensing options<a name="Oracle.Concepts.Licensing"></a>

Amazon RDS for Oracle has two licensing options: License Included \(LI\) and Bring Your Own License \(BYOL\)\. After you create an Oracle DB instance on Amazon RDS, you can change the licensing model by modifying the DB instance\. For more information, see [Modifying an Amazon RDS DB instance](Overview.DBInstance.Modifying.md)\.

## License Included<a name="Oracle.Concepts.Licensing.LicenseIncluded"></a>

In the License Included model, you don't need to purchase Oracle Database licenses separately\. AWS holds the license for the Oracle database software\. In this model, if you have an AWS Support account with case support, contact AWS Support for both Amazon RDS and Oracle Database service requests\. The License Included model is only supported on Amazon RDS for Oracle Database Standard Edition Two \(SE2\)\.

## Bring Your Own License \(BYOL\)<a name="Oracle.Concepts.Licensing.BYOL"></a>

In the BYOL model, you can use your existing Oracle Database licenses to deploy databases on Amazon RDS\. Make sure that you have the appropriate Oracle Database license \(with Software Update License and Support\) for the DB instance class and Oracle Database edition you wish to run\. You must also follow Oracle's policies for licensing Oracle Database software in the cloud computing environment\. For more information on Oracle's licensing policy for Amazon EC2, see [ Licensing Oracle software in the cloud computing environment](http://www.oracle.com/us/corporate/pricing/cloud-licensing-070579.pdf)\.

In this model, you continue to use your active Oracle support account, and you contact Oracle directly for Oracle Database service requests\. If you have an AWS Support account with case support, you can contact AWS Support for Amazon RDS issues\. Amazon Web Services and Oracle have a multi\-vendor support process for cases that require assistance from both organizations\. 

Amazon RDS supports the BYOL model only for Oracle Database Enterprise Edition \(EE\) and Oracle Database Standard Edition Two \(SE2\)\.

### Integrating with AWS License Manager<a name="oracle-lms-integration"></a>

To make it easier to monitor Oracle license usage in the BYOL model, [AWS License Manager](http://aws.amazon.com/license-manager/) integrates with Amazon RDS for Oracle\. License Manager supports tracking of RDS for Oracle engine editions and licensing packs based on virtual cores \(vCPUs\)\. You can also use License Manager with AWS Organizations to manage all of your organizational accounts centrally\.

The following table shows the product information filters for RDS for Oracle\.

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/Oracle.Concepts.Licensing.html)

To track license usage of your Oracle DB instances, you can create a license configuration\. In this case, RDS for Oracle resources that match the product information filter are automatically associated with the license configuration\. Discovery of Oracle DB instances can take up to 24 hours\.

#### Console<a name="oracle-lms-integration.console"></a>

**To create a license configuration to track the license usage of your Oracle DB instances**

1. Go to [https://console\.aws\.amazon\.com/license\-manager/](https://console.aws.amazon.com/license-manager/)\.

1. Create a license configuration\.

   For instructions, see [Create a license configuration](https://docs.aws.amazon.com/license-manager/latest/userguide/create-license-configuration.html) in the *AWS License Manager User Guide*\.

   Add a rule for an **RDS Product Information Filter** in the **Product Information** panel\.

   For more information, see [ProductInformation](https://docs.aws.amazon.com/license-manager/latest/APIReference/API_ProductInformation.html) in the *AWS License Manager API Reference*\.

#### AWS CLI<a name="oracle-lms-integration.cli"></a>

To create a license configuration by using the AWS CLI, call the [create\-license\-configuration](https://docs.aws.amazon.com/cli/latest/reference/license-manager/create-license-configuration.html) command\. Use the `--cli-input-json` or `--cli-input-yaml` parameters to pass the parameters to the command\.

**Example**  
The following code creates a license configuration for Oracle Enterprise Edition\.   

```
aws license-manager create-license-configuration -cli-input-json file://rds-oracle-ee.json
```
The following is the sample `rds-oracle-ee.json` file used in the example\.  

```
{
    "Name": "rds-oracle-ee",
    "Description": "RDS Oracle Enterprise Edition",
    "LicenseCountingType": "vCPU",
    "LicenseCountHardLimit": false,
    "ProductInformationList": [
        {
            "ResourceType": "RDS",
            "ProductInformationFilterList": [
                {
                    "ProductInformationFilterName": "Engine Edition",
                    "ProductInformationFilterValue": ["oracle-ee"],
                    "ProductInformationFilterComparator": "EQUALS"
                }
            ]
        }
    ]
}
```

For more information about product information, see [Automated discovery of resource inventory](https://docs.aws.amazon.com/license-manager/latest/userguide/automated-discovery.html) in the *AWS License Manager User Guide*\.

For more information about the `--cli-input` parameter, see [Generating AWS CLI skeleton and input parameters from a JSON or YAML input file](https://docs.aws.amazon.com/cli/latest/userguide/cli-usage-skeleton.html) in the *AWS CLI User Guide*\.

### Migrating between Oracle editions<a name="Oracle.Concepts.EditionsMigrating"></a>

If you have an unused BYOL Oracle license appropriate for the edition and class of DB instance that you plan to run, you can migrate from Standard Edition 2 \(SE2\) to Enterprise Edition \(EE\)\. You can't migrate from Enterprise Edition to other editions\.

**To change the edition and retain your data**

1. Create a snapshot of the DB instance\.

   For more information, see [Creating a DB snapshot](USER_CreateSnapshot.md)\.

1. Restore the snapshot to a new DB instance, and select the Oracle database edition you want to use\.

   For more information, see [Restoring from a DB snapshot](USER_RestoreFromSnapshot.md)\.

1. \(Optional\) Delete the old DB instance, unless you want to keep it running and have the appropriate Oracle Database licenses for it\.

   For more information, see [Deleting a DB instance](USER_DeleteInstance.md)\.

## Licensing Oracle Multi\-AZ deployments<a name="Oracle.Concepts.Licensing.MAZ"></a>

Amazon RDS supports Multi\-AZ deployments for Oracle as a high\-availability, failover solution\. We recommend Multi\-AZ for production workloads\. For more information, see [Multi\-AZ deployments for high availability](Concepts.MultiAZ.md)\. 

If you use the Bring Your Own License model, you must have a license for both the primary DB instance and the standby DB instance in a Multi\-AZ deployment\. 