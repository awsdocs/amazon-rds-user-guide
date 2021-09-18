# Reading log file contents using REST<a name="DownloadCompleteDBLogFile"></a>

Amazon RDS provides a REST endpoint that allows access to DB instance log files\. This is useful if you need to write an application to stream Amazon RDS log file contents\.

The syntax is:

```
GET /v13/downloadCompleteLogFile/DBInstanceIdentifier/LogFileName HTTP/1.1
Content-type: application/json
host: rds.region.amazonaws.com
```

The following parameters are required:
+ `DBInstanceIdentifier`—the name of the DB instance that contains the log file you want to download\.
+ `LogFileName`—the name of the log file to be downloaded\.

The response contains the contents of the requested log file, as a stream\.

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

If you specify a nonexistent DB instance, the response consists of the following error:
+ `DBInstanceNotFound`—`DBInstanceIdentifier` does not refer to an existing DB instance\. \(HTTP status code: 404\)