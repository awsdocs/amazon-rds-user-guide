# Setting Up AWS CloudHSM Classic to Work with Amazon RDS<a name="Appendix.OracleCloudHSM.SetupCloudHSM"></a>

To use AWS CloudHSM Classic with an Oracle DB instance using TDE, you must first complete the tasks required to setup AWS CloudHSM Classic\. The tasks are explained in detail in the following sections\. 

Amazon RDS supports AWS CloudHSM Classic for Oracle DB instances in the following regions: US East \(N\. Virginia\), US West \(Oregon\), Asia Pacific \(Seoul\), Asia Pacific \(Singapore\), Asia Pacific \(Sydney\), Asia Pacific \(Tokyo\), EU \(Frankfurt\), EU \(Ireland\)\. 

## Completing the AWS CloudHSM Classic Prerequisites<a name="prereq"></a>

Follow the procedure in the [Setting Up AWS CloudHSM](http://docs.aws.amazon.com/cloudhsm/classic/userguide/cloud-hsm-prereq.html) section in the *AWS CloudHSM Classic User Guide* to setup an AWS CloudHSM Classic environment\. 

## Installing the AWS CloudHSM Classic Command Line Interface Tools<a name="control_instance"></a>

Follow the instructions in the [Setting Up the AWS CloudHSM CLI Tools](http://docs.aws.amazon.com/cloudhsm/classic/userguide/cli-setup.html) section in the *AWS CloudHSM Classic User Guide* to install the AWS CloudHSM Classic command line interface tools on your AWS CloudHSM Classic control instance\. 

## Configuring Your HSMs<a name="configure_hsms"></a>

The recommended configuration for using AWS CloudHSM Classic with Amazon RDS is to use three AWS CloudHSM Classic appliances configured into a high\-availability \(HA\) partition group\. A minimum of three HSMs are suggested for HA purposes\. Even if two of your HSMs are unavailable, your keys will still be available to Amazon RDS\. 

![\[AWS CloudHSM Classic high-availability configuration\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/hsm-ha.png)

**Important**  
Initializing an HSM sets the password for the HSM security officer account \(also known as the HSM administrator\)\. Record the security officer password on your [Password Worksheet](password_worksheet.md) and do not lose it\. We recommend that you print out a copy of the [Password Worksheet](password_worksheet.md), use it to record your AWS CloudHSM Classic passwords, and store it in a secure place\. We also recommended that you store at least one copy of this worksheet in secure off\-site storage\. AWS does not have the ability to recover your key material from an HSM for which you do not have the proper HSM security officer credentials\. 

To provision and initialize your HSMs using the AWS CloudHSM Classic CLI tools, perform the following steps from your control instance: 

1. Following the instructions in [Creating Your HSMs with the CLI](http://docs.aws.amazon.com/cloudhsm/classic/userguide/cli-create-hsm.html), provision the number of HSMs you need for your configuration\. When you provision your HSMs, make note of the ARN of each HSM because you will need these to initialize your HSMs and create your high\-availability partition group\. 

1. Following the instructions in [Initializing Your HSMs](http://docs.aws.amazon.com/cloudhsm/classic/userguide/cli-initialize-hsm.html), initialize each of your HSMs\. 

## Creating Your High\-Availability Partition Group<a name="configure_hapg"></a>

After your HSMs are initialized, create an HA partition group with the initialized HSMs\. Creating an HA partition group is a three\-step process\. You create the HA partition group, add your HSMs to the HA partition group, and register the clients for use with the HA partition group\. 

**To create and initialize an HA partition group**

1. Following the instructions in the [Create the HA Partition Group](http://docs.aws.amazon.com/cloudhsm/classic/userguide/cli-guide.html) section in the *AWS CloudHSM Classic User Guide*, create your HA partition group\. Save the HA partition group ARN returned from the `create-hapg` command for later use\. 

   Save the partition password on your [Password Worksheet](password_worksheet.md)\. 

1. Following the instructions in [Registering a Client with a High\-Availability Partition Group](http://docs.aws.amazon.com/cloudhsm/classic/userguide/cli-client.html), create, register, and assign the clients to use with your HA partition group\. 

Repeat this process to add additional partitions if necessary\. One partition can support multiple Oracle databases\. 