# Setting Up Amazon RDS to Work with AWS CloudHSM Classic<a name="Appendix.OracleCloudHSM.SetupRDS"></a>

To use AWS CloudHSM Classic with an Oracle DB instance using Oracle TDE, you must do the following tasks:
+ Ensure that the security group associated with the Oracle DB instance allows access to the HSM port 1792\.
+ Create a DB subnet group that uses the same subnets as those in the VPC used by your HSMs, and then assign that DB subnet group to your Oracle DB instance\.
+ Set up the Amazon RDS CLI\.
+ Add IAM permissions for Amazon RDS to use when accessing AWS CloudHSM Classic\.
+ Add the **TDE\_HSM** option to the option group associated with your Oracle DB instance using the Amazon RDS CLI\.
+ Add two new DB instance parameters to the Oracle DB instance that will use AWS CloudHSM Classic\. The `tde-credential-arn` parameter is the Amazon Resource Number \(ARN\) of the high\-availability \(HA\) partition group returned from the `create-hapg` command\. The `tde-credential-password` is the partition password you used when you initialized the HA partition group\.

The Amazon RDS CLI documentation can be found at [What Is the AWS Command Line Interface?](https://docs.aws.amazon.com//cli/latest/userguide/cli-chap-welcome.html) and the section [Getting Set Up with the AWS Command Line Interface](https://docs.aws.amazon.com//cli/latest/userguide/cli-chap-getting-set-up.html)\. General instructions on using the AWS CLI can be found at [Using the AWS Command Line Interface](https://docs.aws.amazon.com//cli/latest/userguide/cli-chap-using.html)\. 

The following sections show you how to set up the Amazon RDS CLI, add the required permissions for RDS to access your HSMs, create an option group with the **TDE\_HSM** option, and how to create or modify a DB instance that will use the **TDE\_HSM** option\. 

## Security Group<a name="w4aac30c87c11c27c11"></a>

To allow the RDS instance to communicate with the HSM, the security group ENI assigned to the HSM appliance must authorize ingress connectivity on TCP port 1792 from the DB instance\. Additionally, the Network ACL associated with the HSM's ENI must permit ingress TCP port 1792 from the RDS instance, and egress connections from the HSM to the Dynamic Port range on the RDS instance\. For more information about the Dynamic TCP Port range, please see the [Amazon VPC documentation](https://docs.aws.amazon.com//AmazonVPC/latest/UserGuide/VPC_ACLs.html#VPC_ACLs_Ephemeral_Ports)\. 

If you used the AWS CloudFormation template to create your AWS CloudHSM Classic environment, modify the security group that allows SSH and NTLS from the public subnet\. If you didn't use the AWS CloudFormation template, modify the security group associated with the ENI assigned to the HSM appliance\. 

## DB Subnet Group<a name="w4aac30c87c11c27c13"></a>

The DB subnet group that you assign to your Oracle DB instance must have the same subnets as those in the VPC used by the AWS CloudHSM Classic\. For information about how to create a DB subnet group, see [Creating a DB Subnet Group](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_VPC.html#USER_VPC.CreateDBSubnetGroup), or you can use the AWS CLI [create\-db\-subnet\-group](https://docs.aws.amazon.com/cli/latest/reference/rds/delete-db-subnet-group.html) command to create the DB subnet group\.

## Setting Up the Amazon RDS CLI<a name="w4aac30c87c11c27c15"></a>

The Amazon RDS CLI can be installed on a computer running the Linux or Windows operating system and that has Java version 1\.6 or higher installed\. 

The following steps install and configure the Amazon RDS CLI:

1. Download the Amazon RDS CLI from [here](https://s3.amazonaws.com/rds-downloads/RDSCli-1.17.001.zip)\. Unzip the file\.

1. Set the following environment variables:

   ```
   AWS_RDS_HOME - <The directory where the deployment files were copied to>
   JAVA_HOME - <Java Installation home directory>
   ```

   You can check that the environment variables are set correctly by running the following command for Linux or Windows should list `describe-db-instances` and other AWS CLI commands\.

   For Linux, OS X, or Unix:

   ```
   ls ${AWS_RDS_HOME}/bin
   ```

   For Windows:

   ```
   dir %AWS_RDS_HOME%\bin
   ```

1. Add `${AWS_RDS_HOME}/bin` \(Linux\) or `%AWS_RDS_HOME%\bin` \(Windows\) to your path

1. Add the RDS service URL information for your AWS region to your shell configuration\. For example:

   ```
   export RDS_URL=https://rds.us-east-1.amazonaws.com
   export SERVICE_SIG_NAME=rds
   ```

1. If you are on a Linux system, set execute permissions on all files in the bin directory using the following command: 

   ```
   chmod +x ${AWS_RDS_HOME}/bin/*
   ```

1. Provide the Amazon RDS CLI with your AWS user credentials\. There are two ways you can provide credentials: AWS keys, or using X\.509 certificates\.

   If you are using AWS keys, do the following:

   1. Edit the credential file included in the zip file, $\{AWS\_RDS\_HOME\}/credential\-file\-path\.template, to add your AWS credentials\. If you are on a Linux system, limit permissions to the owner of the credential file:

      ```
      $ chmod 600 <credential file>
      ```

   1.  Alternatively, you can provide the following option with every command:

      ```
      aws rds <AWSCLIcommand>  --aws-credential-file <credential file>
      ```

   1. Or you can explicitly specify credentials on the command line: \-\-I ACCESS\_KEY \-\-S SECRET\_KEY

   If you are using X\.509 certifications, do the following:

   1. Save your certificate and private keys to files: e\.g\. my\-cert\.pem and my\-pk\.pem\.

   1. Set the following environment variables:

      ```
      EC2_CERT=<path_to_my_cert>
      EC2_PRIVATE_KEY=<path_to_my_private_key>
      ```

   1. Or you can specify the files directly on command\-line for every command:

      For Linux, OS X, or Unix:

      ```
      aws rds <AWSCLIcommand> \
          --ec2-cert-file-path <path_to_my_cert> \
          --ec2-private-key-file-path <path_to_my_private_key>
      ```

      For Windows:

      ```
      aws rds <AWSCLIcommand> ^
          --ec2-cert-file-path <path_to_my_cert> ^
          --ec2-private-key-file-path <path_to_my_private_key>
      ```

You can test that you have set up the AWS CLI correctly by running the following commands\. The first command should output the usage page for all Amazon RDS commands\. The second command should output information on all DB instances for the account you are using\.

```
aws rds --help
aws rds describe-db-instances --headers
```

## Adding IAM Permissions for Amazon RDS to Access the AWS CloudHSM Classic<a name="Appendix.OracleCloudHSM.SetupRDS.Permissions"></a>

You can use a single AWS account to work with Amazon RDS and AWS CloudHSM Classic or you can use two separate accounts, one for Amazon RDS and one for AWS CloudHSM Classic\. This section provides information on both processes\. 

**Topics**
+ [Adding IAM Permissions for a Single Account for Amazon RDS to Access the AWS CloudHSM Classic API](#Appendix.OracleCloudHSM.SetupRDS.Permissions.OneAccount)
+ [Using Separate AWS CloudHSM Classic and Amazon RDS Accounts for Amazon RDS to Access AWS CloudHSM Classic](#Appendix.OracleCloudHSM.SetupRDS.Permissions.TwoAccounts)

### Adding IAM Permissions for a Single Account for Amazon RDS to Access the AWS CloudHSM Classic API<a name="Appendix.OracleCloudHSM.SetupRDS.Permissions.OneAccount"></a>

To create an IAM role that Amazon RDS uses to access the AWS CloudHSM Classic API, use the following procedure\. Amazon RDS checks for the presence of this IAM role when you create or modify a DB instance that uses AWS CloudHSM Classic\.

**To create an IAM role for Amazon RDS to access the AWS CloudHSM Classic API**

1. Open the [IAM console](https://console.aws.amazon.com/iam/home?#home)\.

1. In the left navigation pane, click **Roles**\.

1. Click **Create role**\.

1. Click **AWS Service** and choose **RDS**\.

1. Under **Select your use case**, choose **RDS**\.

1. Choose **Next: Permissions**\.

1. On the **Attached permissions policy** page, choose **Next: Review**\.

1. In **Role name** on the **Review** page, type **RDSCloudHsmAuthorization**\. Currently, you must use this name\. 

   In **Role description**, you can also type a description for the role\.

1. Review the information and then click **Create role**\.

### Using Separate AWS CloudHSM Classic and Amazon RDS Accounts for Amazon RDS to Access AWS CloudHSM Classic<a name="Appendix.OracleCloudHSM.SetupRDS.Permissions.TwoAccounts"></a>

If you want to separately manage your AWS CloudHSM Classic and Amazon RDS resources, you can use the two services with separate accounts\. To use two different accounts, you must set up each account as described in the following section\.

To use two accounts, you must have the following:
+ An account that is enabled for the AWS CloudHSM Classic service and that is the owner of your hardware security module \(HSM\) devices\. Generally, this account is your AWS CloudHSM Classic account, with a customer ID of HSM\_ACCOUNT\_ID\.
+ An account for Amazon RDS that you can use to create and manage a DB instance that uses Oracle TDE\. Generally, this account is your DB account, with a customer ID DB\_ACCOUNT\_ID\.

**To add DB account permission to access AWS CloudHSM Classic resources under the AWS CloudHSM Classic account**

1. Create the IAM policy\.

   1. Open the [IAM console](https://console.aws.amazon.com/iam/home?#home)\.

   1. Log in using your DB account\.

   1. In the navigation pane, choose **Policies**\.

   1. Choose **Create policy**\.

   1. Choose **JSON** tab\.

   1. Copy the following policy information and paste it into the policy text field: 

      ```
      {
          "Version": "2012-10-17",
          "Statement": [
              {
                  "Effect": "Allow",
                  "Action": [
                      "sts:AssumeRole"
                  ],
                  "Resource": "*"
              }
          ]
      }
      ```

   1. Choose **Review policy**\.

   1. In **Name**, type **AssumeRole**\. You can also add an optional **Description** value\.

   1. Choose **Create policy**\.

1. Create the role\.

   1. In the navigation pane of the [IAM console](https://console.aws.amazon.com/iam/home?#home), choose **Roles**\.

   1. Choose **Create role**\.

   1. Under **AWS service**, choose **RDS**\.

   1. Under **Select your use case**, choose **RDS**\.

   1. Choose **Next: Permissions**\.

   1. Choose **Next: Review**\.

   1. In **Role name** on the **Review** page, type **RDSCloudHsmAssumeAuthorization**\. Currently, you must use this name\. 

      In **Role description**, you can also type a description for the role\.

   1. Choose **Create Role**\.

1. Attach the policy to the role\.

   1. In the navigation pane of the [IAM console](https://console.aws.amazon.com/iam/home?#home), choose **Roles**\.

   1. In the **Search** field, enter **RDSCloudHsmAssumeAuthorization**, and click the role when it appears in the list\.

   1. On the **Permissions** tab, detach the following default roles from the policy:
      + `AmazonRDSDirectoryServiceAccess`
      + `RDSCloudHsmAuthorizationRole`

      To detach a role, click the **X** associated with the role on the right, and then click **Detach**\.

   1. On the **Permissions** tab, choose **Attach policy**\.

   1. On the **Attach policy** page, enter **AssumeRole** in the **Search** field\.

   1. When it appears in the list, select the **AssumeRole** policy\.

   1. Choose **Attach policy**\.

**To revise the AWS CloudHSM Classic account to trust permission to access AWS CloudHSM Classic resources under the AWS CloudHSM Classic account**

1. Open the [IAM console](https://console.aws.amazon.com/iam/home?#home)\.

1.  Log in using your AWS CloudHSM Classic account\. 

1.  In the left navigation pane, choose **Roles**\.

1. In the **Search** field, enter **RDSCloudHsmAuthorization**, and click the role when it appears in the list\. This role is the one created for a single account CloudHSM\-RDS\.

1. Choose the **Trust relationships** tab\.

1. Choose **Edit trust relationship**\. 

1. Add your DB account as a trusted account\. The policy document should look like the following, with your DB account replacing the *<DB\_ACCOUNT\_ID>* placeholder: 

   ```
   {
     "Version": "2012-10-17",
     "Statement": [
       {
         "Sid": "",
         "Effect": "Allow",
         "Principal": {
           "Service": "rds.amazonaws.com",
           "AWS":[   "arn:aws:iam::<DB_ACCOUNT_ID>:role/RDSCloudHsmAssumeAuthorization"
           ]
         },
         "Action": "sts:AssumeRole"
       }
     ]
   }
   ```

1. Choose **Update Trust Policy**\.

#### Creating an Amazon VPC Using the DB Account That Can Connect to Your HSM<a name="w4aac30c87c11c27c17b9c13"></a>

HSM appliances are provisioned into an HSM\-specific Amazon VPC\. By default, only hosts inside the HSM VPC can see the HSM devices\. Thus, all DB instances need to be created inside the HSM VPC or in a VPC that can be linked to the HSM VPC using VPC peering\.

To use AWS CloudHSM Classic with an Amazon RDS DB instance in a different VPC \(which you create under your DB account, as described in [Creating a DB Instance in a VPC](USER_VPC.WorkingWithRDSInstanceinaVPC.md#USER_VPC.InstanceInVPC)\), you set up VPC peering from the VPC containing the DB instance to the HSM\-specific VPC that contains your HSM appliances\.

**To set up VPC peering between the two VPCs**

1.  Use an existing VPC created under your DB account, or create a new VPC using your DB account\. The VPC should not have any CIDR ranges that overlap with the CIDR ranges of the HSM\-specific VPC\. 

1.  Perform VPC peering between the DB VPC and the HSM VPC\. For instructions, go to [VPC Peering](https://docs.aws.amazon.com/AmazonVPC/latest/UserGuide/vpc-peering.html) in the *Amazon Virtual Private Cloud User Guide\.* 

1.  Ensure that the VPC routing table is correctly associated with the VPC subnet and the VPC security group on the HSM network interface\. 

Note that you must configure both VPCs' routing tables so that network traffic goes to the correct VPC \(from the DB VPC to the HSM VPC, and from the HSM VPC to the DB VPC\)\. The two VPCs don’t need to share the same security group, though the security groups must not prevent network traffic between the two VPCs\.

## Creating an Option Group with the TDE\_HSM Option<a name="w4aac30c87c11c27c19"></a>

The **TDE\_HSM** option can be added to an existing option group just like other Oracle options, or you can create a new option group and add the **TDE\_HSM** option\. The following Amazon RDS CLI example creates an option group for Oracle Enterprise Edition 11\.2 named *tdehsm\-option\-group*\. 

For Linux, OS X, or Unix:

```
aws rds create-option-group \
    --option-group-name tdehsm-option-group \
    --option-group-description "Option Group with TDE_HSM" \
    --engine-name oracle-ee \
    --major-engine-version 11.2
```

For Windows:

```
aws rds create-option-group ^
    --option-group-name tdehsm-option-group ^
    --option-group-description "Option Group with TDE_HSM" ^
    --engine-name oracle-ee ^
    --major-engine-version 11.2
```

The output of the command should appear similar to the following example:

```
OPTIONGROUP  tdehsm-option-group  oracle-ee  11.2  Option Group with TDE_HSM  n
```

Once the option group has been created, you can use the following command to add the **TDE\_HSM** option to the option group\.

For Linux, OS X, or Unix:

```
aws rds add-option-to-option-group \
    --option-group-name tdehsm-option-group \
    --option-name OptionName=TDE_HSM
```

For Windows:

```
aws rds add-option-to-option-group ^
    --option-group-name tdehsm-option-group ^
    --option-name OptionName=TDE_HSM
```

The output of the command should appear similar to the following example:

```
OPTION  TDE_HSM  y  n  Oracle Advanced Security - TDE with HSM
```

## Adding the AWS CloudHSM Classic Parameters to an Oracle DB Instance<a name="w4aac30c87c11c27c21"></a>

An Oracle Enterprise Edition DB instance that uses AWS CloudHSM Classic must have two new parameters added to the DB instance\. The `tde-credential-arn` and `tde-credential-password` parameters are new parameters you must include when creating a new DB instance or when modifying an existing DB instance to use AWS CloudHSM Classic\. 

### Creating a New Oracle DB Instance with Additional Parameters for AWS CloudHSM Classic<a name="w4aac30c87c11c27c21b5"></a>

When creating a new DB instance to use with AWS CloudHSM Classic, there are several requirements:
+ You must include the option group that contains the **TDE\_HSM** option
+ You must provide values for the `tde-credential-arn` and `tde-credential-password` parameters\. The `tde-credential-arn` parameter value is the Amazon Resource Number \(ARN\) of the HA partition group returned from the `create-hapg` command\. You can also retrieve the ARNs of all of your high\-availability partition groups with the `list-hapgs` command\.

  The `tde-credential-password` is the partition password you used when you initialized the HA partition group\. 
+ The IAM Role that provides cross\-service access must be created\.
+ You must create an Oracle Enterprise Edition DB instance\.

The following command creates a new Oracle Enterprise Edition DB instance called *HsmInstance\-test01* that includes the two parameters that provide AWS CloudHSM Classic access and uses an option group called *tdehsm\-option\-group*\.

For Linux, OS X, or Unix:

```
aws rds create-db-instance \
    --db-instance-identifier HsmInstance-test01 \
    --db-instance-class <instance class> \
    --engine oracle-ee \
    --tde-credential-arn <ha partition group ARN> \
    --tde-credential-password <partition password> \
    --db-name <Oracle DB instance name> \
    --db-subnet-group-name <subnet group name> \
    --connection-timeout <connection timeout value> \
    --master-user-password <master user password> \
    --master-username <master user name> \
    --allocated-storage <storage value> \
    --option-group-name <TDE option group>
```

For Windows:

```
aws rds create-db-instance ^
    --db-instance-identifier HsmInstance-test01 ^
    --db-instance-class <instance class> ^
    --engine oracle-ee ^
    --tde-credential-arn <ha partition group ARN> ^
    --tde-credential-password <partition password> ^
    --db-name <Oracle DB instance name> ^
    --db-subnet-group-name <subnet group name> ^
    --connection-timeout <connection timeout value> ^ 
    --master-user-password <master user password> ^
    --master-username <master user name> ^
    --allocated-storage <storage value> ^
    --option-group-name <TDE option group>
```

The output of the command should appear similar to the following example:

```
DBINSTANCE  hsminstance-test01  db.m1.medium  oracle-ee  40  fooooo  creating  
1  ****  n  11.2.0.4.v7  bring-your-own-license  AL52UTF8  n
      VPCSECGROUP  sg-922xvc2fd  active
SUBNETGROUP  dev-test  test group  Complete  vpc-3facfe54
      SUBNET  subnet-1fd6a337  us-east-1e  Active
      SUBNET  subnet-28aeff43  us-east-1c  Active
      SUBNET  subnet-5daeff36  us-east-1b  Active
      SUBNET  subnet-2caeff47  us-east-1d  Active
      PARAMGRP  default.oracle-ee-11.2  in-sync
      OPTIONGROUP  tdehsm-option-group  pending-apply
```

### Modifying an Existing DB Instance to Add Parameters for AWS CloudHSM Classic<a name="w4aac30c87c11c27c21b7"></a>

The following command modifies an existing Oracle Enterprise Edition DB instance and adds the `tde-credential-arn` and `tde-credential-password` parameters\. Note that you must also include in the command the option group that contains the **TDE\_HSM** option\.

For Linux, OS X, or Unix:

```
aws rds modify-db-instance \
    --db-instance-identifier hsm03 \
    --tde-credential-arn <ha partition group ARN> \
    --tde-credential-password <partition password> \
    --option-group <tde hsm option group> \
    --apply-immediately
```

For Windows:

```
aws rds modify-db-instance ^
    --db-instance-identifier hsm03 ^
    --tde-credential-arn <ha partition group ARN> ^
    --tde-credential-password <partition password> ^
    --option-group <tde hsm option group> ^
    --apply-immediately
```

The output of the command should appear similar to the following example:

```
DBINSTANCE  hsm03  2014-04-03T18:48:53.106Z  db.m1.medium  oracle-ee  40  fooooo  available  
hsm03.c1iibpgwvdfo.us-east-1.rds.amazonaws.com  1521  us-east-1e  1  
n  11.2.0.4.v7  bring-your-own-license  AL32UTF8  n
      VPCSECGROUP  sg-922dc2fd  active
SUBNETGROUP  dev-test  test group  Complete  vpc-3faffe54
      SUBNET  subnet-1fd6a337  us-east-1e  Active
      SUBNET  subnet-28aeff43  us-east-1c  Active
      SUBNET  subnet-5daeff36  us-east-1b  Active
      SUBNET  subnet-2caeff47  us-east-1d  Active
      PARAMGRP  default.oracle-ee-11.2  in-sync
      OPTIONGROUP  tdehsm-option-group               pending-apply
      OPTIONGROUP  default:oracle-ee-11-2  pending-removal
```