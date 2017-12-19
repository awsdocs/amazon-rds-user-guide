# Using the Query API<a name="Using_the_Query_API"></a>

The following sections discuss the parameters and request authentication used with the Query API\.

## Query Parameters<a name="query-parameters"></a>

HTTP Query\-based requests are HTTP requests that use the HTTP verb GET or POST and a Query parameter named `Action`\.

Each Query request must include some common parameters to handle authentication and selection of an action\. 

Some operations take lists of parameters\. These lists are specified using the `param.n` notation\. Values of *n* are integers starting from 1\. 

For information about Amazon RDS regions and endpoints, go to [Amazon Relational Database Service \(RDS\)](http://docs.aws.amazon.com//general/latest/gr/rande.html#rds_region) in the Regions and Endpoints section of the *Amazon Web Services General Reference*\.

## Query Request Authentication<a name="query-authentication"></a>

You can only send Query requests over HTTPS, and you must include a signature in every Query request\. You must use either a signature version 2 or signature version 4\. This section describes how to create a signature version 2\. For information about creating a signature version 4, see [ Signature Version 4 Signing Process](http://docs.aws.amazon.com/general/latest/gr/signature-version-4.html)\. 

The following are the basic steps used to authenticate requests to AWS\. This process assumes you are registered with AWS and have an access key ID and secret access key\. 

**Tip**  
You can find your access key ID and secret access key in the [Security Credentials](https://portal.aws.amazon.com//gp/aws/developer/account/index.html?action=access-key) section of the AWS [Your Account](http://aws.amazon.com/account/) page\. 

**To authenticate requests to AWS**

1. The sender constructs a request to AWS\.

1. The sender calculates the request signature, a Keyed\-Hashing for Message Authentication Code \(HMAC\) with a SHA\-1 hash function, as defined in the next section of this topic\.

1. The sender of the request sends the request data, the signature, and access key ID \(the key identifier of the secret access key used\) to AWS\.

1. AWS uses the access key ID to look up the secret access key\.

1. AWS generates a signature from the request data and the secret access key using the same algorithm used to calculate the signature in the request\.

1. If the signatures match, the request is considered to be authentic\. If the comparison fails, the request is discarded, and AWS returns an error response\.

**Note**  
If a request contains a `Timestamp` parameter, the signature calculated for the request expires 15 minutes after its value\. If a request contains an `Expires` parameter, the signature expires at the time specified by the `Expires` parameter\. 

**To calculate the request signature**

1. Create the canonicalized query string that you need later in this procedure:

   1. Sort the UTF\-8 query string components by parameter name with natural byte ordering\. The parameters can come from the GET URI or from the POST body \(when Content\-Type is application/x\-www\-form\-urlencoded\)\.

   1. URL encode the parameter name and values according to the following rules:

      1. Do not URL encode any of the unreserved characters that RFC 3986 defines\. These unreserved characters are A–Z, a–z, 0–9, hyphen \( \- \), underscore \( \_ \), period \( \. \), and tilde \( \~ \)\.

      1. Percent encode all other characters with %XY, where X and Y are hex characters 0–9 and uppercase A–F\.

      1. Percent encode extended UTF\-8 characters in the form %XY%ZA\.\.\.\.

      1. Percent encode the space character as %20 \(and not \+, as common encoding schemes do\)\.

   1. Separate the encoded parameter names from their encoded values with the equals sign \( = \) \(ASCII character 61\), even if the parameter value is empty\.

   1. Separate the name\-value pairs with an ampersand \( & \) \(ASCII code 38\)\.

1.  Create the string to sign according to the following pseudo\-grammar \(the "\\n" represents an ASCII newline\)\. 

   ```
   StringToSign = HTTPVerb + "\n" +
   ValueOfHostHeaderInLowercase + "\n" +
   HTTPRequestURI + "\n" +
   CanonicalizedQueryString <from the preceding step>
   ```

    The HTTPRequestURI component is the HTTP absolute path component of the URI up to, but not including, the query string\. If the HTTPRequestURI is empty, use a forward slash \( / \)\. 

1. Calculate an RFC 2104\-compliant HMAC with the string you just created, your secret access key as the key, and SHA256 or SHA1 as the hash algorithm\.

   For more information, go to [RFC 2104](http://www.rfc-editor.org/rfc/rfc2104.txt)\.

1. Convert the resulting value to base 64\.

1. Include the value as the value of the `Signature` parameter in the request\.

For example, the following is an example request \(line breaks added for clarity\)\. 

```
https://rds.amazonaws.com/
    ?Action=DescribeDBInstances
    &DBInstanceIdentifier=myinstance
    &Version=2010-01-01
    &Timestamp=2010-05-10T17%3A09%3A03.726Z
    &SignatureVersion=2
    &SignatureMethod=HmacSHA256
    &AWSAccessKeyId=<Your AWS Access Key ID>
```

For the preceding Query string, you calculate the HMAC signature over the following string\. 

```
GET\n
rds.amazonaws.com\n
AWSAccessKeyId=<Your AWS Access Key ID>
&Action=DescribeDBInstances
&DBInstanceIdentifier=myinstance
&Timestamp=2010-05-10T17%3A09%3A03.726Z
&SignatureMethod=HmacSHA256
&SignatureVersion=2
&Version=2009-10-16
```

The result is the following signed request\. 

```
https://rds.amazonaws.com/
?Action=DescribeDBInstances
 &DBInstanceIdentifier=myinstance
 &Version=2010-01-01
 &Timestamp=2010-05-10T17%3A09%3A03.726Z
 &Signature=<URLEncode(Base64Encode(Signature))>
 &SignatureVersion=2
 &SignatureMethod=HmacSHA256
 &AWSAccessKeyId=<Your AWS Access Key ID>
```