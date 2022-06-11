# Managing access to database activity streams<a name="DBActivityStreams.ManagingAccess"></a>

Any user with appropriate AWS Identity and Access Management \(IAM\) role privileges for database activity streams can create, start, stop, and modify the activity stream settings for a DB instance\. These actions are included in the audit log of the stream\. For best compliance practices, we recommend that you don't provide these privileges to DBAs\.

You set access to database activity streams using IAM policies\. For more information about RDS for Oracle authentication, see [Identity and access management for Amazon RDS](UsingWithRDS.IAM.md)\. For more information about creating IAM policies, see [Creating and using an IAM policy for IAM database access](UsingWithRDS.IAMDBAuth.IAMPolicy.md)\. 

**Example Policy to allow configuring database activity streams**  
To give users fine\-grained access to modify activity streams, use the service\-specific operation context keys `rds:StartActivityStream` and `rds:StopActivityStream`  in an IAM policy\. The following IAM policy example allows a user or role to configure activity streams\.  

```
{
   "Version":"2012-10-17",
   "Statement":[
        {
            "Sid":"ConfigureActivityStreams",
            "Effect":"Allow",
            "Action": [
                "rds:StartActivityStream",
                "rds:StopActivityStream"
            ],
            "Resource":"*",
          }
     ]
}
```

**Example Policy to allow starting database activity streams**  
The following IAM policy example allows a user or role to start activity streams\.  

```
{
    "Version":"2012-10-17",
    "Statement":[
        {
            "Sid":"AllowStartActivityStreams",
            "Effect":"Allow",
            "Action":"rds:StartActivityStream",
            "Resource":"*"
        }
    ]
}
```

**Example Policy to allow stopping database activity streams**  
The following IAM policy example allows a user or role to stop activity streams\.  

```
{
    "Version":"2012-10-17",
    "Statement":[
        {
            "Sid":"AllowStopActivityStreams",
            "Effect":"Allow",
            "Action":"rds:StopActivityStream",
            "Resource":"*"
        }
     ]
}
```

**Example Policy to deny starting database activity streams**  
The following IAM policy example prevents a user or role from starting activity streams\.  

```
{
    "Version":"2012-10-17",
    "Statement":[
        {
            "Sid":"DenyStartActivityStreams",
            "Effect":"Deny",
            "Action":"rds:StartActivityStream",
            "Resource":"*"
        }
     ]
}
```

**Example Policy to deny stopping database activity streams**  
The following IAM policy example prevents a user or role from stopping activity streams\.  

```
{
    "Version":"2012-10-17",
    "Statement":[
        {
            "Sid":"DenyStopActivityStreams",
            "Effect":"Deny",
            "Action":"rds:StopActivityStream",
            "Resource":"*"
        }
    ]
}
```