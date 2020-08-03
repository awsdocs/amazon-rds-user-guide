# Rotating Your SSL/TLS Certificate<a name="UsingWithRDS.SSL-certificate-rotation"></a>

As of March 5, 2020, Amazon RDS CA\-2015 certificates have expired\. If you use or plan to use Secure Sockets Layer \(SSL\) or Transport Layer Security \(TLS\) with certificate verification to connect to your RDS DB instances, you require Amazon RDS CA\-2019 certificates, which are enabled by default for new DB instances\. If you currently do not use SSL/TLS with certificate verification, you might still have expired CA\-2015 certificates and must update them to CA\-2019 certificates if you plan to use SSL/TLS with certificate verification to connect to your RDS databases\. 

Follow these instructions to complete your updates\. Before you update your DB instances to use the new CA certificate, make sure that you update your clients or applications connecting to your RDS databases\.

Amazon RDS provides new CA certificates as an AWS security best practice\. For information about the new certificates and the supported AWS Regions, see [Using SSL/TLS to Encrypt a Connection to a DB Instance](UsingWithRDS.SSL.md)\.

**Note**  
Amazon RDS Proxy uses certificates from the AWS Certificate Manager \(ACM\)\. If you are using RDS Proxy, when you rotate your SSL/TLS certificate, you don't need to update applications that use RDS Proxy connections\. For more information about using TLS/SSL with RDS Proxy, see [Using TLS/SSL with RDS Proxy](rds-proxy.md#rds-proxy-security.tls)\.

**Note**  
If you are using a Go version 1\.15 application with a DB instance that was created or updated to the `rds-ca-2019` certificate prior to July 28, 2020, you must update the certificate again\. Run the `modify-db-instance` command shown in the AWS CLI section using `rds-ca-2019` as the CA certificate identifier\. In this case, it isn't possible to update the certificate using the AWS Management Console\. If you created your DB instance or updated its certificate after July 28, 2020, no action is required\. For more information, see [Go GitHub issue \#39568](https://github.com/golang/go/issues/39568)\.

**Topics**
+ [Updating Your CA Certificate by Modifying Your DB Instance](#UsingWithRDS.SSL-certificate-rotation-updating)
+ [Updating Your CA Certificate by Applying DB Instance Maintenance](#UsingWithRDS.SSL-certificate-rotation-maintenance)
+ [Sample Script for Importing Certificates Into Your Trust Store](#UsingWithRDS.SSL-certificate-rotation-sample-script)

## Updating Your CA Certificate by Modifying Your DB Instance<a name="UsingWithRDS.SSL-certificate-rotation-updating"></a>

Complete the following steps to update your CA certificate\.

**To update your CA certificate by modifying your DB instance**

1. Download the new SSL/TLS certificate as described in [Using SSL/TLS to Encrypt a Connection to a DB Instance](UsingWithRDS.SSL.md)\.

1. Update your applications to use the new SSL/TLS certificate\.

   The methods for updating applications for new SSL/TLS certificates depend on your specific applications\. Work with your application developers to update the SSL/TLS certificates for your applications\.

   For information about checking for SSL/TLS connections and updating applications for each DB engine, see the following topics:
   + [Updating Applications to Connect to MariaDB DB Instances Using New SSL/TLS Certificates](ssl-certificate-rotation-mariadb.md)
   + [Updating Applications to Connect to Microsoft SQL Server DB Instances Using New SSL/TLS Certificates](ssl-certificate-rotation-sqlserver.md)
   + [Updating Applications to Connect to MySQL DB Instances Using New SSL/TLS Certificates](ssl-certificate-rotation-mysql.md)
   + [Updating Applications to Connect to Oracle DB Instances Using New SSL/TLS Certificates](ssl-certificate-rotation-oracle.md)
   + [Updating Applications to Connect to PostgreSQL DB Instances Using New SSL/TLS Certificates](ssl-certificate-rotation-postgresql.md)

   For a sample script that updates a trust store for a Linux operating system, see [Sample Script for Importing Certificates Into Your Trust Store](#UsingWithRDS.SSL-certificate-rotation-sample-script)\.
**Note**  
The certificate bundle contains certificates for both the old and new CA, so you can upgrade your application safely and maintain connectivity during the transition period\. If you are using the AWS Database Migration Service to migrate a database to a DB instance, we recommend using the certificate bundle to ensure connectivity during the migration\.

1. Modify the DB instance to change the CA from **rds\-ca\-2015** to **rds\-ca\-2019**\.
**Important**  
By default, this operation restarts your DB instance\. If you don't want to restart your DB instance during this operation, you can use the `modify-db-instance` CLI command and specify the `--no-certificate-rotation-restart` option\.  
This option will not rotate the certificate until the next time the database restarts, either for planned or unplanned maintenance\. This option is only recommended if you don't use SSL/TLS\.   
If you are experiencing connectivity issues after certificate expiry, use the apply immediately option by specifying **Apply immediately** in the console or by specifying the `--apply-immediately` option using the AWS CLI\. By default, this operation is scheduled to run during your next maintenance window\.

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

1. To apply the changes immediately, choose **Apply immediately**\.
**Important**  
Choosing this option restarts your database immediately\.

1. On the confirmation page, review your changes\. If they are correct, choose **Modify DB Instance** to save your changes\. 
**Important**  
When you schedule this operation, make sure that you have updated your client\-side trust store beforehand\.

   Or choose **Back** to edit your changes or **Cancel** to cancel your changes\. 

### AWS CLI<a name="UsingWithRDS.SSL-certificate-rotation-updating.CLI"></a>

To use the AWS CLI to change the CA from **rds\-ca\-2015** to **rds\-ca\-2019** for a DB instance, call the [modify\-db\-instance](https://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-instance.html) command\. Specify the DB instance identifier and the `--ca-certificate-identifier` option\. 

**Important**  
When you schedule this operation, make sure that you have updated your client\-side trust store beforehand\.

**Example**  
The following code modifies `mydbinstance` by setting the CA certificate to `rds-ca-2019`\. The changes are applied during the next maintenance window by using `--no-apply-immediately`\. Use `--apply-immediately` to apply the changes immediately\.   
By default, this operation reboots your DB instance\. If you don't want to reboot your DB instance during this operation, you can use the `modify-db-instance` CLI command and specify the `--no-certificate-rotation-restart` option\.  
This option will not rotate the certificate until the next time the database restarts, either for planned or unplanned maintenance\. This option is only recommended if you do not use SSL/TLS\.  
Use `--apply-immediately` to apply the update immediately\. By default, this operation is scheduled to run during your next maintenance window\.
For Linux, macOS, or Unix:  

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

## Updating Your CA Certificate by Applying DB Instance Maintenance<a name="UsingWithRDS.SSL-certificate-rotation-maintenance"></a>

Complete the following steps to update your CA certificate by applying DB instance maintenance\.

**To update your CA certificate by applying DB instance maintenance**

1. Download the new SSL/TLS certificate as described in [Using SSL/TLS to Encrypt a Connection to a DB Instance](UsingWithRDS.SSL.md)\.

1. <a name="UpdateAppsForSSL"></a>Update your database applications to use the new SSL/TLS certificate\.

   The methods for updating applications for new SSL/TLS certificates depend on your specific applications\. Work with your application developers to update the SSL/TLS certificates for your applications\.

   For information about checking for SSL/TLS connections and updating applications for each DB engine, see the following topics:
   + [Updating Applications to Connect to MariaDB DB Instances Using New SSL/TLS Certificates](ssl-certificate-rotation-mariadb.md)
   + [Updating Applications to Connect to Microsoft SQL Server DB Instances Using New SSL/TLS Certificates](ssl-certificate-rotation-sqlserver.md)
   + [Updating Applications to Connect to MySQL DB Instances Using New SSL/TLS Certificates](ssl-certificate-rotation-mysql.md)
   + [Updating Applications to Connect to Oracle DB Instances Using New SSL/TLS Certificates](ssl-certificate-rotation-oracle.md)
   + [Updating Applications to Connect to PostgreSQL DB Instances Using New SSL/TLS Certificates](ssl-certificate-rotation-postgresql.md)

   For a sample script that updates a trust store for a Linux operating system, see [Sample Script for Importing Certificates Into Your Trust Store](#UsingWithRDS.SSL-certificate-rotation-sample-script)\.
**Note**  
The certificate bundle contains certificates for both the old and new CA, so you can upgrade your application safely and maintain connectivity during the transition period\.

1. Apply DB instance maintenance to change the CA from **rds\-ca\-2015** to **rds\-ca\-2019**\.
**Important**  
You can choose to apply the change immediately\. By default, this operation is scheduled to run during your next maintenance window\.

You can use the AWS Management Console to apply DB instance maintenance to change the CA certificate from **rds\-ca\-2015** to **rds\-ca\-2019** for multiple DB instances\.

### Updating Your CA Certificate by Applying Maintenance to Multiple DB Instances<a name="UsingWithRDS.SSL-certificate-rotation-maintenance.multiple"></a>

Use the AWS Management Console to change the CA certificate for multiple DB instances\.

**To change the CA from **rds\-ca\-2015** to **rds\-ca\-2019** for multiple DB instances**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Databases**\.

   In the navigation pane, there is a **Certificate update** option that shows the total number of affected DB instances\.  
![\[Certificate rotation navigation pane option\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/ssl-rotate-cert-certupdate.png)

   Choose **Certificate update** in the navigation pane\.

   The **Update your Amazon RDS SSL/TLS certificates** page appears\.  
![\[Update CA certificate for multiple DB instances\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/ssl-rotate-cert-update-multiple.png)
**Note**  
This page only shows the DB instances for the current AWS Region\. If you have DB instances in more than one AWS Region, check this page in each AWS Region to see all DB instances with old SSL/TLS certificates\.

1. Choose the DB instance you want to update\.

   You can schedule the certificate rotation for your next maintenance window by choosing **Update at the next maintenance window**\. Apply the rotation immediately by choosing **Update now**\.
**Important**  
When your CA certificate is rotated, the operation restarts your DB instance\.  
If you experience connectivity issues after certificate expiry, use the **Update now** option\.

1. If you choose **Update at the next maintenance window** or **Update now**, you are prompted to confirm the CA certificate rotation\.
**Important**  
Before scheduling the CA certificate rotation on your database, update any client applications that use SSL/TLS and the server certificate to connect\. These updates are specific to your DB engine\. To determine whether your applications use SSL/TLS and the server certificate to connect, see [Step 2: Update Your Database Applications to Use the New SSL/TLS Certificate](#UpdateAppsForSSL)\. After you have updated these client applications, you can confirm the CA certificate rotation\.  
![\[Confirm certificate rotation\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/ssl-rotate-cert-confirm.png)

   To continue, choose the check box, and then choose **Confirm**\.

1. Repeat steps 3 and 4 for each DB instance that you want to update\.

## Sample Script for Importing Certificates Into Your Trust Store<a name="UsingWithRDS.SSL-certificate-rotation-sample-script"></a>

The following are sample shell scripts that import the certificate bundle into a trust store\.

**Topics**
+ [Sample Script for Importing Certificates on Linux](#UsingWithRDS.SSL-certificate-rotation-sample-script.linux)
+ [Sample Script for Importing Certificates on macOS](#UsingWithRDS.SSL-certificate-rotation-sample-script.macos)

### Sample Script for Importing Certificates on Linux<a name="UsingWithRDS.SSL-certificate-rotation-sample-script.linux"></a>

The following is a sample shell script that imports the certificate bundle into a trust store on a Linux operating system\.

```
mydir=tmp/certs
if [ ! -e "${mydir}" ]
then
mkdir -p "${mydir}"
fi

truststore=${mydir}/rds-truststore.jks
storepassword=changeit

curl -sS "https://s3.amazonaws.com/rds-downloads/rds-combined-ca-bundle.pem" > ${mydir}/rds-combined-ca-bundle.pem
awk 'split_after == 1 {n++;split_after=0} /-----END CERTIFICATE-----/ {split_after=1}{print > "rds-ca-" n ".pem"}' < ${mydir}/rds-combined-ca-bundle.pem

for CERT in rds-ca-*; do
  alias=$(openssl x509 -noout -text -in $CERT | perl -ne 'next unless /Subject:/; s/.*(CN=|CN = )//; print')
  echo "Importing $alias"
  keytool -import -file ${CERT} -alias "${alias}" -storepass ${storepassword} -keystore ${truststore} -noprompt
  rm $CERT
done

rm ${mydir}/rds-combined-ca-bundle.pem

echo "Trust store content is: "

keytool -list -v -keystore "$truststore" -storepass ${storepassword} | grep Alias | cut -d " " -f3- | while read alias 
do
   expiry=`keytool -list -v -keystore "$truststore" -storepass ${storepassword} -alias "${alias}" | grep Valid | perl -ne 'if(/until: (.*?)\n/) { print "$1\n"; }'`
   echo " Certificate ${alias} expires in '$expiry'" 
done
```

### Sample Script for Importing Certificates on macOS<a name="UsingWithRDS.SSL-certificate-rotation-sample-script.macos"></a>

The following is a sample shell script that imports the certificate bundle into a trust store on macOS\.

```
mydir=tmp/certs
if [ ! -e "${mydir}" ]
then
mkdir -p "${mydir}"
fi

truststore=${mydir}/rds-truststore.jks
storepassword=changeit

curl -sS "https://s3.amazonaws.com/rds-downloads/rds-combined-ca-bundle.pem" > ${mydir}/rds-combined-ca-bundle.pem
split -p "-----BEGIN CERTIFICATE-----" ${mydir}/rds-combined-ca-bundle.pem rds-ca-

for CERT in rds-ca-*; do
  alias=$(openssl x509 -noout -text -in $CERT | perl -ne 'next unless /Subject:/; s/.*(CN=|CN = )//; print')
  echo "Importing $alias"
  keytool -import -file ${CERT} -alias "${alias}" -storepass ${storepassword} -keystore ${truststore} -noprompt
  rm $CERT
done

rm ${mydir}/rds-combined-ca-bundle.pem

echo "Trust store content is: "

keytool -list -v -keystore "$truststore" -storepass ${storepassword} | grep Alias | cut -d " " -f3- | while read alias 
do
   expiry=`keytool -list -v -keystore "$truststore" -storepass ${storepassword} -alias "${alias}" | grep Valid | perl -ne 'if(/until: (.*?)\n/) { print "$1\n"; }'`
   echo " Certificate ${alias} expires in '$expiry'" 
done
```