# mysql\.rds\_import\_binlog\_ssl\_material<a name="mysql_rds_import_binlog_ssl_material"></a>

Imports the certificate authority certificate, client certificate, and client key into an Aurora MySQL DB cluster\. The information is required for SSL communication and encrypted replication\.

**Note**  
Currently, this procedure is only supported for Aurora MySQL version 5\.6\.

## Syntax<a name="mysql_rds_import_binlog_ssl_material-syntax"></a>

```
CALL mysql.rds_import_binlog_ssl_material (
  ssl_material
);
```

## Parameters<a name="mysql_rds_import_binlog_ssl_material-parameters"></a>

 *ssl\_material*   
JSON payload that contains the contents of the following \.pem format files for a MySQL client:  
+ "ssl\_ca":"*Certificate authority certificate*"
+ "ssl\_cert":"*Client certificate*"
+ "ssl\_key":"*Client key*"

## Usage Notes<a name="mysql_rds_import_binlog_ssl_material-usage-notes"></a>

Prepare for encrypted replication before you run this procedure:
+ If you don't have SSL enabled on the external MySQL source database instance and don't have a client key and client certificate prepared, enable SSL on the MySQL database server and generate the required client key and client certificate\.
+ If SSL is enabled on the external source database instance, supply a client key and certificate for the Aurora MySQL DB cluster\. If you don't have these, generate a new key and certificate for the Aurora MySQL DB cluster\. To sign the client certificate, you must have the certificate authority key you used to configure SSL on the external MySQL source database instance\.

For more information, see [ Creating SSL Certificates and Keys Using openssl](https://dev.mysql.com/doc/refman/8.0/en/creating-ssl-files-using-openssl.html) in the MySQL documentation\.

**Important**  
After you prepare for encrypted replication, use an SSL connection to run this procedure\. The client key must not be transferred across an insecure connection\. 

This procedure imports SSL information from an external MySQL database into an Aurora MySQL DB cluster\. The SSL information is in \.pem format files that contain the SSL information for the Aurora MySQL DB cluster\. During encrypted replication, the Aurora MySQL DB cluster acts a client to the MySQL database server\. The certificates and keys for the Aurora MySQL client are in files in \.pem format\.

You can copy the information from these files into the `ssl_material` parameter in the correct JSON payload\. To support encrypted replication, import this SSL information into the Aurora MySQL DB cluster\.

The JSON payload must be in the following format\.

```
'{"ssl_ca":"-----BEGIN CERTIFICATE-----
ssl_ca_pem_body_code
-----END CERTIFICATE-----\n","ssl_cert":"-----BEGIN CERTIFICATE-----
ssl_cert_pem_body_code
-----END CERTIFICATE-----\n","ssl_key":"-----BEGIN RSA PRIVATE KEY-----
ssl_key_pem_body_code
-----END RSA PRIVATE KEY-----\n"}'
```

## Examples<a name="mysql_rds_import_binlog_ssl_material-examples"></a>

The following example imports SSL information into an Aurora MySQL DB cluster\. In \.pem format files, the body code typically is longer than the body code shown in the example\.

```
call mysql.rds_import_binlog_ssl_material(
'{"ssl_ca":"-----BEGIN CERTIFICATE-----
AAAAB3NzaC1yc2EAAAADAQABAAABAQClKsfkNkuSevGj3eYhCe53pcjqP3maAhDFcvBS7O6V
hz2ItxCih+PnDSUaw+WNQn/mZphTk/a/gU8jEzoOWbkM4yxyb/wB96xbiFveSFJuOp/d6RJhJOI0iBXr
lsLnBItntckiJ7FbtxJMXLvvwJryDUilBMTjYtwB+QhYXUMOzce5Pjz5/i8SeJtjnV3iAoG/cQk+0FzZ
qaeJAAHco+CY/5WrUBkrHmFJr6HcXkvJdWPkYQS3xqC0+FmUZofz221CBt5IMucxXPkX4rWi+z7wB3Rb
BQoQzd8v7yeb7OzlPnWOyN0qFU0XA246RA8QFYiCNYwI3f05p6KLxEXAMPLE
-----END CERTIFICATE-----\n","ssl_cert":"-----BEGIN CERTIFICATE-----
AAAAB3NzaC1yc2EAAAADAQABAAABAQClKsfkNkuSevGj3eYhCe53pcjqP3maAhDFcvBS7O6V
hz2ItxCih+PnDSUaw+WNQn/mZphTk/a/gU8jEzoOWbkM4yxyb/wB96xbiFveSFJuOp/d6RJhJOI0iBXr
lsLnBItntckiJ7FbtxJMXLvvwJryDUilBMTjYtwB+QhYXUMOzce5Pjz5/i8SeJtjnV3iAoG/cQk+0FzZ
qaeJAAHco+CY/5WrUBkrHmFJr6HcXkvJdWPkYQS3xqC0+FmUZofz221CBt5IMucxXPkX4rWi+z7wB3Rb
BQoQzd8v7yeb7OzlPnWOyN0qFU0XA246RA8QFYiCNYwI3f05p6KLxEXAMPLE
-----END CERTIFICATE-----\n","ssl_key":"-----BEGIN RSA PRIVATE KEY-----
AAAAB3NzaC1yc2EAAAADAQABAAABAQClKsfkNkuSevGj3eYhCe53pcjqP3maAhDFcvBS7O6V
hz2ItxCih+PnDSUaw+WNQn/mZphTk/a/gU8jEzoOWbkM4yxyb/wB96xbiFveSFJuOp/d6RJhJOI0iBXr
lsLnBItntckiJ7FbtxJMXLvvwJryDUilBMTjYtwB+QhYXUMOzce5Pjz5/i8SeJtjnV3iAoG/cQk+0FzZ
qaeJAAHco+CY/5WrUBkrHmFJr6HcXkvJdWPkYQS3xqC0+FmUZofz221CBt5IMucxXPkX4rWi+z7wB3Rb
BQoQzd8v7yeb7OzlPnWOyN0qFU0XA246RA8QFYiCNYwI3f05p6KLxEXAMPLE
-----END RSA PRIVATE KEY-----\n"}');
```

**Note**  
For information about using Amazon Aurora, see the [https://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/CHAP_AuroraOverview.html](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/CHAP_AuroraOverview.html)\.