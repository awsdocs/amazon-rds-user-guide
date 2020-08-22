# Using the Query API<a name="Using_the_Query_API"></a>

The following sections briefly discuss the parameters and request authentication used with the Query API\.

For general information about how the Query API works, see [Query Requests](https://docs.aws.amazon.com/AWSEC2/latest/APIReference/Query-Requests.html) in the *Amazon EC2 API Reference\.*

## Query Parameters<a name="query-parameters"></a>

HTTP Query\-based requests are HTTP requests that use the HTTP verb GET or POST and a Query parameter named `Action`\.

Each Query request must include some common parameters to handle authentication and selection of an action\. 

Some operations take lists of parameters\. These lists are specified using the `param.n` notation\. Values of *n* are integers starting from 1\. 

For information about Amazon RDS regions and endpoints, go to [Amazon Relational Database Service \(RDS\)](https://docs.aws.amazon.com/general/latest/gr/rande.html#rds_region) in the Regions and Endpoints section of the *Amazon Web Services General Reference*\.

## Query Request Authentication<a name="query-authentication"></a>

You can only send Query requests over HTTPS, and you must include a signature in every Query request\. You must use either AWS signature version 4 or signature version 2\. For more information, see [ Signature Version 4 Signing Process](https://docs.aws.amazon.com/general/latest/gr/signature-version-4.html) and [ Signature Version 2 Signing Process](https://docs.aws.amazon.com/general/latest/gr/signature-version-2.html)\.