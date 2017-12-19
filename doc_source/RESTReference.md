# RDS REST API Reference<a name="RESTReference"></a>

Standard API syntax cannot be used in certain scenarios\. The `DownloadCompleteDBLogFile` action is a REST API action that you can use to retrieve log files\. Because a database log file can be arbitrarily large, the `DownloadCompleteDBLogFile` REST API is provided to enable streaming of the log file contents\. 

## DownloadCompleteDBLogFile<a name="RESTReference.DownloadCompleteDBLogFile"></a>

### Description<a name="w3ab1c44c17b5b5"></a>

Downloads the contents of the specified database log file\.

### Request Parameters<a name="w3ab1c44c17b5b7"></a>

**DBInstanceIdentifier**  
The customer\-assigned name of the DB instance that contains the log file you want to download\.

**LogFileName**  
The name of the log file to be downloaded\.

### Syntax<a name="w3ab1c44c17b5b9"></a>

```
GET /v13/downloadCompleteLogFile/DBInstanceIdentifier/LogFileName HTTP/1.1
Content-type: application/json
host: rds.region.amazonaws.com
```

### Response Elements<a name="w3ab1c44c17b5c11"></a>

The `DownloadCompleteDBLogFile` REST API returns the contents of the requested log file as a stream\.

### Errors<a name="w3ab1c44c17b5c13"></a>

**DBInstanceNotFound**  
`DBInstanceIdentifier` does not refer to an existing DB instance\.   
HTTP Status Code: 404

### Examples<a name="w3ab1c44c17b5c15"></a>

The following example downloads the log file named *log/ERROR\.6* for the DB instance named *sample\-sql* in the *us\-west\-2* region\.

```
GET /v13/downloadCompleteLogFile/sample-sql/log/ERROR.6 HTTP/1.1
host: rds.us-west-2.amazonaws.com
X-Amz-Security-Token: AQoDYXdzEIH//////////wEa0AIXLhngC5zp9CyB1R6abwKrXHVR5efnAVN3XvR7IwqKYalFSn6UyJuEFTft9nObglx4QJ+GXV9cpACkETq=
X-Amz-Date: 20140903T233749Z
X-Amz-Algorithm: AWS4-HMAC-SHA256
X-Amz-Credential: AKIADQKE4SARGYLE/20140903/us-west-2/rds/aws4_request
X-Amz-SignedHeaders: host
X-Amz-Content-SHA256: e3b0c44298fc1c229afbf4c8996fb92427ae41e4649b934de495991b7852b855
X-Amz-Expires: 86400
X-Amz-Signature: 353a4f14b3f250142d9afc34f9f9948154d46ce7d4ec091d0cdabbcf8b40c558
```

## The `rds-download-db-logfile` Command<a name="RESTReference.DownloadCompleteDBLogFile.CLIversion"></a>

You can also download complete log files using the `rds-download-db-logfile` command\. Because the AWS CLI does not currently support the `rds-download-db-logfile` command, you must use the deprecated RDS CLI to run the `rds-download-db-logfile` command\. You can get the last version of the RDS CLI in a ZIP file at [ http://s3\.amazonaws\.com/rds\-downloads/RDSCli\.zip](https://s3.amazonaws.com/rds-downloads/RDSCli.zip)\.

Use the following syntax when using the RDS CLI with the `rds-download-db-logfile` command:

```
rds-download-db-logfile db-instance-identifier
--log-file-name value
[General Options]
```

For example, the following command downloads a log named *log/ERROR\.4* for the *myexampledb* RDS DB instance and stored the log in a file called errorlog\.txt\.

```
PROMPT> rds-download-db-logfile myexampledb --region us-west-2 --log-file-name log/ERROR.4 > errorlog.txt
```

## Related Topics<a name="w3ab1c44c17b9"></a>

+ [Downloading a Database Log File](USER_LogAccess.md#USER_LogAccess.Procedural.Downloading)

+  [download\-db\-logfile\-portion](http://docs.aws.amazon.com/cli/latest/reference/rds/download-db-log-file-portion.html)

+ [DownloadDBLogFilePortion](http://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_DownloadDBLogFilePortion.html)

+ [RDS Query API Documentation](http://docs.aws.amazon.com/AmazonRDS/latest/APIReference/Welcome.html)

+ [ Signature Version 4 Signing Process](http://docs.aws.amazon.com/general/latest/gr/signature-version-4.html) 