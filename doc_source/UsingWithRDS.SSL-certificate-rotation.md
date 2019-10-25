# Rotating Your SSL/TLS Certificate<a name="UsingWithRDS.SSL-certificate-rotation"></a>

**Note**  
If your application connects to an RDS DB instance using Secure Socket Layer \(SSL\) or Transport Layer Security \(TLS\), you must take the following steps *before March 5, 2020\.* Doing this means you can avoid interruption of connectivity between your applications and your RDS DB instances\.

As of September 19, 2019, Amazon RDS has published new Certificate Authority \(CA\) certificates for connecting to your RDS DB instances using SSL/TLS\. We provide these new CA certificates as an AWS security best practice\. For information about the new certificates and the supported AWS Regions, see [Using SSL/TLS to Encrypt a Connection to a DB Instance](UsingWithRDS.SSL.md)\.

The current CA certificates expire on March 5, 2020\. Therefore, we strongly recommend completing this change as soon as possible \(and no later than February 5, 2020\), to avoid disruption on the expiration date\. If the change is not completed, your applications will fail to connect to your RDS DB instances using SSL/TLS after March 5, 2020\. 

We encourage you to test the steps listed following in a development or staging environment before taking them for your production environments\. 

Before you update your DB instances to use the new CA certificate, make sure that you update your clients or applications connecting to your RDS databases\.

Any new RDS DB instances created after January 14, 2020 will use the new certificates by default\. If you want to temporarily modify new DB instances manually to use the old \(rds\-ca\-2015\) certificates, you can do so using the AWS Management Console or the AWS CLI\. Any DB instances created prior to January 14, 2020 use the rds\-ca\-2015 certificates until you update them to the rds\-ca\-2019 certificates\.

**Topics**
+ [Updating Your CA Certificate](#UsingWithRDS.SSL-certificate-rotation-updating)
+ [Reverting an Update of a CA Certificate](#UsingWithRDS.SSL-certificate-rotation-reverting)

## Updating Your CA Certificate<a name="UsingWithRDS.SSL-certificate-rotation-updating"></a>

Complete the following steps to update your CA certificate\.

**To update your CA certificate**

1. Download the new SSL/TLS certificate from [Using SSL/TLS to Encrypt a Connection to a DB Instance](UsingWithRDS.SSL.md)\.

1. Update your database applications to use the new SSL/TLS certificate\.

   The methods for updating applications for new SSL/TLS certificates depend on your specific applications\. Work with your application developers to update the SSL/TLS certificates for your applications\.
**Note**  
The certificate bundle contains certificates for both the old and new CA, so you can upgrade your application safely and maintain connectivity during the transition period\.

1. Modify the DB instance to change the CA from **rds\-ca\-2015** to **rds\-ca\-2019**\.
**Important**  
This operation reboots your DB instance\. By default, this operation is scheduled to run during your next maintenance window\. Alternatively, you can choose to run it immediately\.

You can use the AWS Management Console or the AWS CLI to change the CA certificate from **rds\-ca\-2015** to **rds\-ca\-2019** for a DB instance\.

### Console<a name="UsingWithRDS.SSL-certificate-rotation-updating.Console"></a>

**To change the CA from **rds\-ca\-2015** to **rds\-ca\-2019** for a DB instance**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Databases**, and then choose the DB instance that you want to modify\. 

1. Choose **Modify**\.  
![\[Modify DB instance\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/ssl-rotate-cert-modify.png)

   The **Modify DB Instance** page appears\.

1. In the **Network & Security** section, choose **rds\-ca\-2019**\.  
![\[Choose CA certificate\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/ssl-rotate-cert-ca-2019.png)

1. Choose **Continue** and check the summary of modifications\. 

1. To apply the changes immediately, choose **Apply immediately**\. Choosing this option causes an outage\.

1. On the confirmation page, review your changes\. If they are correct, choose **Modify DB Instance** to save your changes\. 
**Important**  
When you schedule this operation, make sure that you have updated your client\-side trust store beforehand\.

   Or choose **Back** to edit your changes or **Cancel** to cancel your changes\. 

### AWS CLI<a name="UsingWithRDS.SSL-certificate-rotation-updating.CLI"></a>

To use the AWS CLI to change the CA from **rds\-ca\-2015** to **rds\-ca\-2019** for a DB instance, call the [modify\-db\-instance](https://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-instance.html) command\. Specify the DB instance identifier and the `--ca-certificate-identifier` option\. 

**Important**  
When you schedule this operation, make sure that you have updated your client\-side trust store beforehand\.

**Example**  
The following code modifies `mydbinstance` by setting the CA certificate to `rds-ca-2019`\. The changes are applied during the next maintenance window by using `--no-apply-immediately`\. Use `--apply-immediately` to apply the changes immediately\. Choosing this option causes an outage\.   
For Linux, OS X, or Unix:  

```
aws rds modify-db-instance \
    --db-instance-identifier mydbinstance \
    --ca-certificate-identifier rds-ca-2019 \
    --no-apply-immediately
```
For Windows:  

```
aws rds modify-db-instance ^
    --db-instance-identifier mydbinstance ^
    --ca-certificate-identifier rds-ca-2019 ^
    --no-apply-immediately
```

## Reverting an Update of a CA Certificate<a name="UsingWithRDS.SSL-certificate-rotation-reverting"></a>

You can use the AWS Management Console or the AWS CLI to revert to a previous CA certificate for a DB instance\.

### Console<a name="UsingWithRDS.SSL-certificate-rotation-reverting.Console"></a>

**To revert to a previous CA certificate for a DB instance**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Databases**, and then choose the DB instance that you want to modify\. 

1. Choose **Modify**\.  
![\[Modify DB instance\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/ssl-rotate-cert-modify.png)

   The **Modify DB Instance** page appears\.

1. In the **Network & Security** section, choose **rds\-ca\-2015**\.  
![\[Choose CA certificate\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/ssl-rotate-cert-ca-choice.png)

1. Choose **Continue** and check the summary of modifications\. 

1. To apply the changes immediately, choose **Apply immediately**\. Choosing this option causes an outage\.

1. On the confirmation page, review your changes\. If they are correct, choose **Modify DB Instance** to save your changes\. 
**Important**  
When you schedule this operation, make sure you have updated your client\-side trust store beforehand\.

   Or choose **Back** to edit your changes or **Cancel** to cancel your changes\. 

### AWS CLI<a name="UsingWithRDS.SSL-certificate-rotation-reverting.CLI"></a>

To revert to a previous CA certificate for a DB instance, call the [modify\-db\-instance](https://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-instance.html) command\. Specify the DB instance identifier and the `--ca-certificate-identifier` option\. 

**Important**  
When you schedule this operation, make sure that you have updated your client\-side trust store beforehand\.

**Example**  
The following code modifies `mydbinstance` by setting the CA certificate to `rds-ca-2015`\. The changes are applied during the next maintenance window by using `--no-apply-immediately`\. Use `--apply-immediately` to apply the changes immediately\. Choosing this option causes an outage\.   
For Linux, OS X, or Unix:  

```
aws rds modify-db-instance \
    --db-instance-identifier mydbinstance \
    --ca-certificate-identifier rds-ca-2015 \
    --no-apply-immediately
```
For Windows:  

```
aws rds modify-db-instance ^
    --db-instance-identifier mydbinstance ^
    --ca-certificate-identifier rds-ca-2015 ^
    --no-apply-immediately
```