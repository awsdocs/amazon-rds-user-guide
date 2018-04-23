# Oracle Management Agent for Enterprise Manager Cloud Control<a name="Oracle.Options.OEMAgent"></a>

Amazon RDS supports Oracle Enterprise Manager \(OEM\) Management Agent through the use of the OEM\_AGENT option\. Amazon RDS supports Management Agent for the following versions of OEM: 
+ Oracle Enterprise Manager Cloud Control for 13c
+ Oracle Enterprise Manager Cloud Control for 12c

Management Agent is a software component that monitors targets running on hosts and communicates that information to the middle\-tier Oracle Management Service \(OMS\)\. For more information, see [Overview of Oracle Enterprise Manager Cloud Control 12c](http://docs.oracle.com/cd/E24628_01/doc.121/e25353/overview.htm) and [Overview of Oracle Enterprise Manager Cloud Control 13c](http://docs.oracle.com/cd/E63000_01/EMCON/overview.htm#EMCON109) in the Oracle documentation\. 

The following are some limitations to using Management Agent: 
+ Administrative tasks such as job execution and database patching, that require host credentials, are not supported\. 
+ Host metrics and the process list are not guaranteed to reflect the actual system state\. 
+ Autodiscovery is not supported\. You must manually add database targets\. 
+ OMS module availability depends your database edition\. For example, the database performance diagnosis and tuning module is only available for Oracle Database Enterprise Edition\. 
+ Management Agent consumes additional memory and computing resources\. If you experience performance problems after enabling the OEM\_AGENT option, we recommend that you scale up to a larger DB instance class\. For more information, see [DB Instance Class](Concepts.DBInstanceClass.md) and [Modifying a DB Instance Running the Oracle Database Engine](USER_ModifyInstance.Oracle.md)\. 

## Prerequisites for Management Agent<a name="Oracle.Options.OEMAgent.PreReqs"></a>

The following are prerequisites for using Management Agent: 
+ An Amazon RDS DB instance running Oracle version 12\.1\.0\.2 or 11\.2\.0\.4\. 
+ At least 3\.3 GiB of storage space for OEM 13c2\. 
+ At least 3 GiB of storage space for OEM 13c1\. 
+ At least 2 GiB of storage space for OEM 12c\. 
+ An Oracle Management Service \(OMS\), configured to connect to your Amazon RDS DB instance\. 
  + For OMS 13c2 with Oracle patch 25163555 applied, use OEM Agent 13\.2\.0\.0\.v2 or later\.

    Use OMSPatcher to apply the patch\.
  + For unpatched OMS 13c2, use OEM Agent 13\.2\.0\.0\.v1\.
+ In most cases, you need to configure your VPC to allow connections from OMS to your DB instance\. If you are not familiar with Amazon Virtual Private Cloud \(Amazon VPC\), we recommend that you complete the steps in [Tutorial: Create an Amazon VPC for Use with an Amazon RDS DB Instance](CHAP_Tutorials.WebServerDB.CreateVPC.md) before continuing\. 

Additional configuration is required to allow your OMS host and your Amazon RDS DB instance to communicate\. You must also do the following: 
+ To connect from the Management Agent to your OMS, if your OMS is behind a firewall, you must add the IP addresses of your DB instances to your OMS\. 
+ To connect from your OMS to the Management Agent, if your OMS has a publicly resolvable host name, you must add the OMS address to a security group\. Your security group must have inbound rules that allow access to the DB instance port and the Management Agent port\. For an example of creating a security and adding inbound rules, see [Tutorial: Create an Amazon VPC for Use with an Amazon RDS DB Instance](CHAP_Tutorials.WebServerDB.CreateVPC.md)\. 
+ To connect from your OMS to the Management Agent, if your OMS doesn't have a publicly resolvable host name, use one of the following: 
  + If your OMS is hosted on an Amazon Elastic Compute Cloud \(Amazon EC2\) instance in a private VPC, you can set up VPC peering to connect from OMS to Management Agent\. For more information, see [A DB Instance in a VPC Accessed by an EC2 Instance in a Different VPC](USER_VPC.Scenarios.md#USER_VPC.Scenario3)\. 
  + If your OMS is hosted on\-premises, you can set up a VPN connection to allow access from OMS to Management Agent\. For more information, see [A DB Instance in a VPC Accessed by a Client Application Through the Internet](USER_VPC.Scenarios.md#USER_VPC.Scenario4) or [VPN Connections](http://docs.aws.amazon.com/AmazonVPC/latest/UserGuide/vpn-connections.html)\. 

## Management Agent Option Settings<a name="Oracle.Options.OEMAgent.Options"></a>

Amazon RDS supports the following settings for the Management Agent option\. 


****  

| Option Setting | Valid Values | Description | 
| --- | --- | --- | 
| **Version** \(`AGENT_VERSION`\) |  13\.2\.0\.0 13\.1\.0\.0 12\.1\.0\.4 12\.1\.0\.5  |  The version of the Management Agent software\.   | 
| **Port** \(`AGENT_PORT`\) | An integer value |  The port on the DB instance that listens for the OMS host\. The default is 3872\. Your OMS host must belong to a security group that has access to this port\.   | 
| **Security Groups** | — |  A security group that has access to **Port**\. Your OMS host must belong to this security group\.   | 
| **OMS\_HOST** |  A string value, for example *my\.example\.oms*   |  The publicly accessible host name or IP address of the OMS\.   | 
| **OMS\_PORT** | An integer value |  The HTTPS upload port on the OMS Host that listens for the Management Agent\.  To determine the HTTPS upload port, connect to the OMS host, and run the following command \(which requires the `SYSMAN` password\): emctl status oms \-details   | 
| **AGENT\_REGISTRATION\_PASSWORD** | A string value |  The password that the Management Agent uses to authenticate itself with the OMS\. We recommend that you create a persistent password in your OMS before enabling the OEM\_AGENT option\. With a persistent password you can share a single Management Agent option group among multiple Amazon RDS databases\.   | 

## Adding the Management Agent Option<a name="Oracle.Options.OEMAgent.Add"></a>

The general process for adding the Management Agent option to a DB instance is the following: 

1. Create a new option group, or copy or modify an existing option group\.

1. Add the option to the option group\.

1. Associate the option group with the DB instance\.

After you add the Management Agent option, you don't need to restart your DB instance\. As soon as the option group is active, the OEM Agent is active\. 

**To add the Management Agent option to a DB instance**

1. Determine the option group you want to use\. You can create a new option group or use an existing option group\. If you want to use an existing option group, skip to the next step\. Otherwise, create a custom DB option group with the following settings: 

   1. For **Engine** choose the oracle edition for your DB instance\. 

   1. For **Major engine version** choose **11\.2** or **12\.1** for your DB instance\. 

   For more information, see [Creating an Option Group](USER_WorkingWithOptionGroups.md#USER_WorkingWithOptionGroups.Create)\. 

1. Add the **OEM\_AGENT** option to the option group, and configure the option settings\. For more information about adding options, see [Adding an Option to an Option Group](USER_WorkingWithOptionGroups.md#USER_WorkingWithOptionGroups.AddOption)\. For more information about each setting, see [Management Agent Option Settings](#Oracle.Options.OEMAgent.Options)\. 

1. Apply the option group to a new or existing DB instance: 
   + For a new DB instance, you apply the option group when you launch the instance\. For more information, see [Creating a DB Instance Running the Oracle Database Engine](USER_CreateOracleInstance.md)\. 
   + For an existing DB instance, you apply the option group by modifying the instance and attaching the new option group\. For more information, see [Modifying a DB Instance Running the Oracle Database Engine](USER_ModifyInstance.Oracle.md)\. 

## Using the Management Agent<a name="Oracle.Options.OEMAgent.Using"></a>

After you enable the Management Agent option, use the following procedure to begin using it\. 

**To use the Management Agent**

1. Unlock and reset the DBSNMP account credential, by running the following code on your target database on your DB instance, and using your master user account\. 

   ```
   1. ALTER USER dbsnmp IDENTIFIED BY new_password ACCOUNT UNLOCK;
   ```

1. Add your targets to the OMS console manually: 

   1. In your OMS console, choose **Setup**, **Add Target**, **Add Targets Manually**\. 

   1. Choose **Add Targets Declaratively by Specifying Target Monitoring Properties**\.

   1. For **Target Type**, choose **Database Instance**\.

   1. For **Monitoring Agent**, choose the agent with the same identifier as your Amazon RDS DB instance identifier\. 

   1. Choose **Add Manually**\.

   1. Enter the host name for the Amazon RDS DB instance, or select the host name from the list\. Ensure that the specified host name matches the endpoint of the Amazon RDS DB instance\.

   1. Specify the following database properties: 
      + For **Target name**, type a name\. 
      + For **Database system name**, type a name\. 
      + For **Monitor username**, type `dbsnmp`\. 
      + For **Monitor password**, type the password from Step 1\. 
      + For **Role**, type **normal**\. 
      + For **Oracle home path**, type **/oracle**\. 
      + For **Listener Machine name**, the agent identifier already appears\. 
      + For **Port**, type the database port\. The RDS default port is 1521\. 
      + For **Database name**, type the name of your database\. 

   1. Choose **Test Connection**\. 

   1. Choose **Next**\. The target database appears in your list of monitored resources\. 

## Modifying Management Agent Settings<a name="Oracle.Options.OEMAgent.ModifySettings"></a>

After you enable the Management Agent, you can modify settings for the option\. For more information about how to modify option settings, see [Modifying an Option Setting](USER_WorkingWithOptionGroups.md#USER_WorkingWithOptionGroups.ModifyOption)\. For more information about each setting, see [Management Agent Option Settings](#Oracle.Options.OEMAgent.Options)\. 

## Removing the Management Agent Option<a name="Oracle.Options.OEMAgent.Remove"></a>

You can remove the OEM Agent from a DB instance\. After you remove the OEM Agent, you don't need to restart your DB instance\. 

To remove the OEM Agent from a DB instance, do one of the following: 
+ Remove the OEM Agent option from the option group it belongs to\. This change affects all DB instances that use the option group\. For more information, see [Removing an Option from an Option Group](USER_WorkingWithOptionGroups.md#USER_WorkingWithOptionGroups.RemoveOption) 
+ Modify the DB instance and specify a different option group that doesn't include the OEM Agent option\. This change affects a single DB instance\. You can specify the default \(empty\) option group, or a different custom option group\. For more information, see [Modifying a DB Instance Running the Oracle Database Engine](USER_ModifyInstance.Oracle.md)\. 

## Related Topics<a name="Oracle.Options.OEMAgent.Related"></a>
+ [Working with Option Groups](USER_WorkingWithOptionGroups.md)
+ [Options for Oracle DB Instances](Appendix.Oracle.Options.md)