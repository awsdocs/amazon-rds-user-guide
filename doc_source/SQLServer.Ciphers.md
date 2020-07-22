# Configuring Security Protocols and Ciphers<a name="SQLServer.Ciphers"></a>

You can turn certain security protocols and ciphers on and off using DB parameters\. The security parameters that you can configure \(except for TLS version 1\.2\) are shown in the following table\. 

A value of `default` means that the operating system default value is used, whether it is `enabled` or `disabled`\.

**Note**  
You can't disable TLS 1\.2, because Amazon RDS uses it internally\.


****  

| DB Parameter | Allowed Values \(Default in Bold\) | Description | 
| --- | --- | --- | 
| rds\.tls10 | default, enabled, disabled | TLS 1\.0\. | 
| rds\.tls11 | default, enabled, disabled | TLS 1\.1\. | 
| rds\.tls12 | default | TLS 1\.2\. You can't modify this value\. | 
| rds\.rc4 | default, enabled, disabled | RC4 stream cipher\. | 
| rds\.diffie\-hellman | default, enabled, disabled | Diffie\-Hellman key\-exchange encryption\. | 
| rds\.diffie\-hellman\-min\-key\-bit\-length | default, 1024, 2048, 4096 | Minimum bit length for Diffie\-Hellman keys\. | 
| rds\.curve25519 | default, enabled, disabled | Curve25519 elliptic\-curve encryption cipher\. This parameter isn't supported for all engine versions\. | 
| rds\.3des168 | default, enabled, disabled | Triple Data Encryption Standard \(DES\) encryption cipher with a 168\-bit key length\. | 

Use the following process to configure the security protocols and ciphers:

1. Create a custom DB parameter group\.

1. Modify the parameters in the parameter group\.

1. Associate the DB parameter group with your DB instance\.

For more information on DB parameter groups, see [Working with DB Parameter Groups](USER_WorkingWithParamGroups.md)\.

## Creating the Security\-Related Parameter Group<a name="CreateParamGroup.Ciphers"></a>

Create a parameter group for your security\-related parameters that corresponds to the SQL Server edition and version of your DB instance\.

### Console<a name="CreateParamGroup.Ciphers.Console"></a>

The following procedure creates a parameter group for SQL Server Standard Edition 2016\.

**To create the parameter group**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Parameter groups**\.

1. Choose **Create parameter group**\.

1. In the **Create parameter group** pane, do the following:

   1. For **Parameter group family**, choose **sqlserver\-se\-13\.0**\.

   1. For **Group name**, enter an identifier for the parameter group, such as **sqlserver\-ciphers\-se\-13**\.

   1. For **Description**, enter **Parameter group for security protocols and ciphers**\.

1. Choose **Create**\.

### CLI<a name="CreateParamGroup.Ciphers.CLI"></a>

The following procedure creates a parameter group for SQL Server Standard Edition 2016\.

**To create the parameter group**
+ Run one of the following commands\.  
**Example**  

  For Linux, macOS, or Unix:

  ```
  aws rds create-db-parameter-group \
      --db-parameter-group-name sqlserver-ciphers-se-13 \
      --db-parameter-group-family "sqlserver-se-13.0" \
      --description "Parameter group for security protocols and ciphers"
  ```

  For Windows:

  ```
  aws rds create-db-parameter-group ^
      --db-parameter-group-name sqlserver-ciphers-se-13 ^
      --db-parameter-group-family "sqlserver-se-13.0" ^
      --description "Parameter group for security protocols and ciphers"
  ```

## Modifying Security\-Related Parameters<a name="ModifyParams.Ciphers"></a>

Modify the security\-related parameters in the parameter group that corresponds to the SQL Server edition and version of your DB instance\.

### Console<a name="ModifyParams.Ciphers.Console"></a>

The following procedure modifies the parameter group that you created for SQL Server Standard Edition 2016\. This example turns off TLS version 1\.0\.

**To modify the parameter group**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Parameter groups**\.

1. Choose the parameter group, such as **sqlserver\-ciphers\-se\-13**\.

1. Under **Parameters**, filter the parameter list for **rds**\.

1. Choose **Edit parameters**\.

1. Choose **rds\.tls10**\.

1. For **Values**, choose **disabled**\.

1. Choose **Save changes**\.

### CLI<a name="ModifyParams.Ciphers.CLI"></a>

The following procedure modifies the parameter group that you created for SQL Server Standard Edition 2016\. This example turns off TLS version 1\.0\.

**To modify the parameter group**
+ Run one of the following commands\.  
**Example**  

  For Linux, macOS, or Unix:

  ```
  aws rds modify-db-parameter-group \
      --db-parameter-group-name sqlserver-ciphers-se-13 \
      --parameters "ParameterName='rds.tls10',ParameterValue='disabled',ApplyMethod=pending-reboot"
  ```

  For Windows:

  ```
  aws rds modify-db-parameter-group ^
      --db-parameter-group-name sqlserver-ciphers-se-13 ^
      --parameters "ParameterName='rds.tls10',ParameterValue='disabled',ApplyMethod=pending-reboot"
  ```

## Associating the Security\-Related Parameter Group with Your DB Instance<a name="AssocParamGroup.Ciphers"></a>

To associate the parameter group with your DB instance, use the AWS Management Console or the AWS CLI\.

### Console<a name="AssocParamGroup.Ciphers.Console"></a>

You can associate the parameter group with a new or existing DB instance:
+ For a new DB instance, associate it when you launch the instance\. For more information, see [Creating an Amazon RDS DB Instance](USER_CreateDBInstance.md)\.
+ For an existing DB instance, associate it by modifying the instance\. For more information, see [Modifying an Amazon RDS DB Instance](Overview.DBInstance.Modifying.md)\.

### CLI<a name="AssocParamGroup.Ciphers.CLI"></a>

You can associate the parameter group with a new or existing DB instance\.

**To create a DB instance with the parameter group**
+ Specify the same DB engine type and major version as you used when creating the parameter group\.  
**Example**  

  For Linux, macOS, or Unix:

  ```
  aws rds create-db-instance \
      --db-instance-identifier mydbinstance \
      --db-instance-class db.m5.2xlarge \
      --engine sqlserver-se \
      --engine-version 13.00.5426.0.v1 \
      --allocated-storage 100 \
      --master-user-password secret123 \
      --master-username admin \
      --storage-type gp2 \
      --license-model li \
      --db-parameter-group-name sqlserver-ciphers-se-13
  ```

  For Windows:

  ```
  aws rds create-db-instance ^
      --db-instance-identifier mydbinstance ^
      --db-instance-class db.m5.2xlarge ^
      --engine sqlserver-se ^
      --engine-version 13.00.5426.0.v1 ^
      --allocated-storage 100 ^
      --master-user-password secret123 ^
      --master-username admin ^
      --storage-type gp2 ^
      --license-model li ^
      --db-parameter-group-name sqlserver-ciphers-se-13
  ```

**To modify a DB instance and associate the parameter group**
+ Run one of the following commands\.  
**Example**  

  For Linux, macOS, or Unix:

  ```
  aws rds modify-db-instance \
      --db-instance-identifier mydbinstance \
      --db-parameter-group-name sqlserver-ciphers-se-13 \
      --apply-immediately
  ```

  For Windows:

  ```
  aws rds modify-db-instance ^
      --db-instance-identifier mydbinstance ^
      --db-parameter-group-name sqlserver-ciphers-se-13 ^
      --apply-immediately
  ```