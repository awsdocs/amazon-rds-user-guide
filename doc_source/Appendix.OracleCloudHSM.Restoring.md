# Restoring Encrypted DB Instances<a name="Appendix.OracleCloudHSM.Restoring"></a>

To restore an encrypted Oracle DB instance, you can use your existing AWS CloudHSM Classic HA partition group or create a new HA partition group and copy the contents from the original partition group to the new partition group\. Please update the SafeNet client on your HSM control instance if you would like to use your existing HA partition group\. Then use the `restore-db-instance-from-db-snapshot` command to restore the DB instance\.

To restore the instance, perform the following procedure:

1. On your AWS CloudHSM Classic control instance, create a new HA partition group as shown in [Creating Your High\-Availability Partition Group](Appendix.OracleCloudHSM.SetupCloudHSM.md#configure_hapg)\. When you create the new HA partition group, you must specify the same partition password as the original HA partition group\. Make a note of the ARN of the new HA partition group, which you will need in the next two steps\.

1. On your AWS CloudHSM Classic control instance, clone the contents of the existing HA partition group to the new HA partition group with the `clone-hapg` command\.

   For Linux, OS X, or Unix:

   ```
   cloudhsm clone-hapg --conf_file ~/cloudhsm.conf \
      --src-hapg-arn <src_arn> \
      --dest-hapg-arn <dest_arn> \ 
      --client-arn <client_arn> \
      --partition-password <partition_password>
   ```

   For Windows:

   ```
   cloudhsm clone-hapg --conf_file ~/cloudhsm.conf ^
      --src-hapg-arn <src_arn> ^
      --dest-hapg-arn <dest_arn> ^ 
      --client-arn <client_arn> ^
      --partition-password <partition_password>
   ```

   The parameters are as follows:  
*<src\_arn>*  
The identifier of the existing HA partition group\.  
*<dest\_arn>*  
The identifier of the new HA partition group created in the previous step\.  
*<client\_arn>*  
The identifier of the HSM client\.  
*<partition\_password>*  
The password for the member partitions\. Both HA partition groups must have the same partition password\.

1. To restore the DB instance, use the AWS CLI [ restore\-db\-instance\-from\-db\-snapshot](http://docs.aws.amazon.com/cli/latest/reference/rds/restore-db-instance-from-db-snapshot.html) command\. For the parameter `tde-credential-arn`, specify the ARN of the new HA partition group in\. For the parameter `tde-credential-password`, specify the partition password for the HA partition group\. 