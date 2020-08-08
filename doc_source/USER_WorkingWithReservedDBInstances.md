# Reserved DB Instances for Amazon RDS<a name="USER_WorkingWithReservedDBInstances"></a>

Using reserved DB instances, you can reserve a DB instance for a one\- or three\-year term\. Reserved DB instances provide you with a significant discount compared to on\-demand DB instance pricing\. Reserved DB instances are not physical instances, but rather a billing discount applied to the use of certain on\-demand DB instances in your account\. Discounts for reserved DB instances are tied to instance type and AWS Region\. 

The general process for working with reserved DB instances is: First get information about available reserved DB instance offerings, then purchase a reserved DB instance offering, and finally get information about your existing reserved DB instances\. 

## Overview of Reserved DB Instances<a name="USER_WorkingWithReservedDBInstances.Overview"></a>

When you purchase a reserved DB instance in Amazon RDS, you purchase a commitment to getting a discounted rate, on a specific DB instance type, for the duration of the reserved DB instance\. To use an Amazon RDS reserved DB instance, you create a new DB instance just like you do for an on\-demand instance\. The new DB instance that you create must match the specifications of the reserved DB instance\. If the specifications of the new DB instance match an existing reserved DB instance for your account, you are billed at the discounted rate offered for the reserved DB instance\. Otherwise, the DB instance is billed at an on\-demand rate\. 

For more information about reserved DB instances, including pricing, see [Amazon RDS Reserved Instances](http://aws.amazon.com/rds/reserved-instances/#2)\. 

### Offering Types<a name="USER_WorkingWithReservedDBInstances.OfferingTypes"></a>

Reserved DB instances are available in three varieties—No Upfront, Partial Upfront, and All Upfront—that let you optimize your Amazon RDS costs based on your expected usage\. 

**No Upfront**  
This option provides access to a reserved DB instance without requiring an upfront payment\. Your No Upfront reserved DB instance bills a discounted hourly rate for every hour within the term, regardless of usage, and no upfront payment is required\. This option is only available as a one\-year reservation\. 

**Partial Upfront**  
This option requires a part of the reserved DB instance to be paid upfront\. The remaining hours in the term are billed at a discounted hourly rate, regardless of usage\. This option is the replacement for the previous Heavy Utilization option\. 

**All Upfront**  
Full payment is made at the start of the term, with no other costs incurred for the remainder of the term regardless of the number of hours used\. 

If you are using consolidated billing, all the accounts in the organization are treated as one account\. This means that all accounts in the organization can receive the hourly cost benefit of reserved DB instances that are purchased by any other account\. For more information about consolidated billing, see [Amazon RDS Reserved DB Instances](https://docs.aws.amazon.com/awsaccountbilling/latest/aboutv2/consolidatedbilling-other.html#consolidatedbilling-rds) in the *AWS Billing and Cost Management User Guide*\.

### Size\-Flexible Reserved DB Instances<a name="USER_WorkingWithReservedDBInstances.SizeFlexible"></a>

When you purchase a reserved DB instance, one thing that you specify is the instance class, for example db\.m4\.large\. For more information about instance classes, see [DB Instance Classes](Concepts.DBInstanceClass.md)\. 

If you have a DB instance, and you need to scale it to larger capacity, your reserved DB instance is automatically applied to your scaled DB instance\. That is, your reserved DB instances are automatically applied across all DB instance class sizes\. Size\-flexible reserved DB instances are available for DB instances with the same AWS Region and database engine\. Size\-flexible reserved DB instances can only scale in their instance class type\. For example, a reserved DB instance for a db\.m4\.large can apply to a db\.m4\.xlarge, but not to a db\.m5\.large, because db\.m4 and db\.m5 are different instance class types\. Reserved DB instance benefits also apply for both Multi\-AZ and Single\-AZ configurations\. Flexibility means that you can move freely between configurations within the same DB instance class type\. For example, you can move from a Single\-AZ deployment running on one large DB instance class to a Multi\-AZ deployment running on two small DB instance classes\. 

Size\-flexible reserved DB instances are available for the following Amazon RDS database engines:
+ MariaDB
+ MySQL
+ Oracle, Bring Your Own License
+ PostgreSQL

For details about using size\-flexible reserved instances with Aurora, see [Reserved DB Instances for Aurora](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/USER_WorkingWithReservedDBInstances.html)\. 

You can compare usage for different reserved DB instance sizes by using normalized units\. For example, one unit of usage on two db\.m3\.large DB instances is equivalent to eight normalized units of usage on one db\.m3\.small\. The following table shows the number of normalized units for each DB instance size\. 


****  

| Instance Size | Single\-AZ Normalized Units | Multi\-AZ Normalized Units | 
| --- | --- | --- | 
| micro | 0\.5 | 1 | 
| small | 1 | 2 | 
| medium | 2 | 4 | 
| large | 4 | 8 | 
| xlarge | 8 | 16 | 
| 2xlarge | 16 | 32 | 
| 4xlarge | 32 | 64 | 
| 6xlarge | 48 | 96 | 
| 8xlarge | 64 | 128 | 
| 10xlarge | 80 | 160 | 
| 12xlarge | 96 | 192 | 
| 16xlarge | 128 | 256 | 
| 24xlarge | 192 | 384 | 
| 32xlarge | 256 | 512 | 

For example, suppose that you purchase a `db.t2.medium` reserved DB instance, and you have two running `db.t2.small` DB instances in your account in the same AWS Region\. In this case, the billing benefit is applied in full to both instances\. 

![\[Applying a reserved DB instance in full to smaller DB instances\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/ri-db-instance-flex-full.png)

Alternatively, if you have one `db.t2.large` instance running in your account in the same AWS Region, the billing benefit is applied to 50 percent of the usage of the DB instance\. 

![\[Applying a reserved DB instance in part to a larger DB instance\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/ri-db-instance-flex-partial.png)

### Reserved DB Instance Billing Example<a name="USER_WorkingWithReservedDBInstances.BillingExample"></a>

The price for a reserved DB instance doesn't include regular costs associated with storage, backups, and I/O\. The following example illustrates the total cost per month for a reserved DB instance:
+ An Amazon RDS MySQL reserved Single\-AZ db\.r4\.large DB instance class in US East \(N\. Virginia\) with the No Upfront option at a cost of $0\.12 for the instance, or $90 per month
+ 400 GiB of General Purpose SSD \(gp2\) storage at a cost of 0\.115 per GiB per month, or $45\.60 per month
+ 600 GiB of backup storage at $0\.095, or $19 per month \(400 GiB free\)

Add all of these options \($90 \+ $45\.60 \+ $19\) with the reserved DB instance, and the total cost per month is $154\.60\.

If you chose to use an on\-demand DB instance instead of a reserved DB instance, an Amazon RDS MySQL Single\-AZ db\.r4\.large DB instance class in US East \(N\. Virginia\) costs $0\.1386 per hour, or $101\.18 per month\. So, for an on\-demand DB instance, add all of these options \($101\.18 \+ $45\.60 \+ $19\), and the total cost per month is $165\.78\.

**Note**  
The prices in this example are sample prices and might not match actual prices\.  
For Amazon RDS pricing information, see the [Amazon RDS product page](https://aws.amazon.com/rds/pricing)\.

### Deleting a Reserved DB Instance<a name="USER_WorkingWithReservedDBInstances.Cancelling"></a>

The terms for a reserved DB instance involve a one\-year or three\-year commitment\. You can't cancel a reserved DB instance\. However, you can delete a DB instance that is covered by a reserved DB instance discount\. The process for deleting a DB instance that is covered by a reserved DB instance discount is the same as for any other DB instance\. 

Your upfront payment for a reserved DB instance reserves the resources for your use\. Because these resources are reserved for you, you are billed for the resources regardless of whether you use them\. 

If you delete a DB instance that is covered by a reserved DB instance discount, you can launch another DB instance with compatible specifications\. In this case, you continue to get the discounted rate during the reservation term \(one or three years\)\. 

## Console<a name="USER_WorkingWithReservedDBInstances.CON"></a>

You can use the AWS Management Console to work with reserved DB instances as shown in the following procedures\. 

**To get pricing and information about available reserved DB instance offerings**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Reserved instances**\. 

1. Choose **Purchase Reserved DB Instance**\.

1. For **Product description**, choose the DB engine and licensing type\.

1. For **DB instance class**, choose the DB instance class\.

1. For **Multi\-AZ deployment**, choose whether you want a Multi\-AZ deployment\.

1. For **Term**, choose the length of time you want the DB instance reserved\.

1. For **Offering type**, choose the offering type\. 

   After you select the offering type, you can see the pricing information\. 
**Important**  
Choose **Cancel** to avoid purchasing the reserved DB instance and incurring any charges\. 

After you have information about the available reserved DB instance offerings, you can use the information to purchase an offering as shown in the following procedure\. 

**To purchase a reserved DB instance**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Reserved instances**\. 

1. Choose **Purchase Reserved DB Instance**\.

1. For **Product description**, choose the DB engine and licensing type\.

1. For **DB instance class**, choose the DB instance class\.

1. For **Multi\-AZ deployment**, choose whether you want a Multi\-AZ deployment\.

1. For **Term**, choose the length of time you want the DB instance reserved\.

1. For **Offering type**, choose the offering type\.

   After you choose the offering type, you can see the pricing information, as shown following\.   
![\[Purchase reserved DB instance console step 1\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/reservedinstance.png)

1. \(Optional\) You can assign your own identifier to the reserved DB instances that you purchase to help you track them\. For **Reserved Id**, type an identifier for your reserved DB instance\. 

1. Choose **Continue**\. 

   The **Purchase Reserved DB Instance** dialog box appears, with a summary of the reserved DB instance attributes that you've selected and the payment due, as shown following\.   
![\[Purchase reserved DB instance console step 2\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/reservedinstance2.png)

1. On the confirmation page, review your reserved DB instance\. If the information is correct, choose **Purchase** to purchase the reserved DB instance\. 

   Alternatively, choose **Back** to edit your reserved DB instance\.

After you have purchased reserved DB instances, you can get information about your reserved DB instances as shown in the following procedure\. 

**To get information about reserved DB instances for your AWS account**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the **Navigation** pane, choose **Reserved instances**\. 

   The reserved DB instances for your account appear\. To see detailed information about a particular reserved DB instance, choose that instance in the list\. You can then see detailed information about that instance in the detail pane at the bottom of the console\. 

## AWS CLI<a name="USER_WorkingWithReservedDBInstances.CLI"></a>

You can use the AWS CLI to work with reserved DB instances as shown in the following examples\. 

**Example Get Available Reserved DB Instance Offerings**  
To get information about available reserved DB instance offerings, call the AWS CLI command [https://docs.aws.amazon.com/cli/latest/reference/rds/describe-reserved-db-instances-offerings.html](https://docs.aws.amazon.com/cli/latest/reference/rds/describe-reserved-db-instances-offerings.html)\.   

```
aws rds describe-reserved-db-instances-offerings
```
This call returns output similar to the following:   

```
 1. OFFERING  OfferingId                            Class         Multi-AZ  Duration  Fixed Price  Usage Price  Description  Offering Type
 2. OFFERING  438012d3-4052-4cc7-b2e3-8d3372e0e706  db.m1.large   y         1y        1820.00 USD  0.368 USD    mysql        Partial  Upfront
 3. OFFERING  649fd0c8-cf6d-47a0-bfa6-060f8e75e95f  db.m1.small   n         1y         227.50 USD  0.046 USD    mysql        Partial  Upfront
 4. OFFERING  123456cd-ab1c-47a0-bfa6-12345667232f  db.m1.small   n         1y         162.00 USD   0.00 USD    mysql        All      Upfront
 5.     Recurring Charges:   Amount  Currency  Frequency        
 6.     Recurring Charges:   0.123   USD       Hourly
 7. OFFERING  123456cd-ab1c-37a0-bfa6-12345667232d  db.m1.large   y         1y         700.00 USD   0.00 USD    mysql        All      Upfront
 8.     Recurring Charges:   Amount  Currency  Frequency
 9.     Recurring Charges:   1.25    USD       Hourly
10. OFFERING  123456cd-ab1c-17d0-bfa6-12345667234e  db.m1.xlarge  n         1y        4242.00 USD   2.42 USD    mysql        No       Upfront
```

After you have information about the available reserved DB instance offerings, you can use the information to purchase an offering as shown in the following example\. 

**Example Purchase a Reserved DB Instance**  
To purchase a reserved DB instance, use the AWS CLI command [https://docs.aws.amazon.com/cli/latest/reference/rds/purchase-reserved-db-instances-offering.html](https://docs.aws.amazon.com/cli/latest/reference/rds/purchase-reserved-db-instances-offering.html) with the following parameters:   
+ `--reserved-db-instances-offering-id` – the id of the offering that you want to purchase\. See the preceding example to get the offering ID\. 
+ `--reserved-db-instance-id` – you can assign your own identifier to the reserved DB instances that you purchase to help you track them\.  
The following example purchases the reserved DB instance offering with ID *649fd0c8\-cf6d\-47a0\-bfa6\-060f8e75e95f*, and assigns the identifier of *MyReservation*\.   
For Linux, macOS, or Unix:  

```
aws rds purchase-reserved-db-instances-offering \
    --reserved-db-instances-offering-id 649fd0c8-cf6d-47a0-bfa6-060f8e75e95f \
    --reserved-db-instance-id MyReservation
```
For Windows:  

```
aws rds purchase-reserved-db-instances-offering ^
    --reserved-db-instances-offering-id 649fd0c8-cf6d-47a0-bfa6-060f8e75e95f ^
    --reserved-db-instance-id MyReservation
```
The command returns output similar to the following:   

```
1. RESERVATION  ReservationId      Class        Multi-AZ  Start Time                Duration  Fixed Price  Usage Price  Count  State            Description  Offering Type
2. RESERVATION  MyReservation      db.m1.small  y         2011-12-19T00:30:23.247Z  1y        455.00 USD   0.092 USD    1      payment-pending  mysql        Partial  Upfront
```

After you have purchased reserved DB instances, you can get information about your reserved DB instances as shown in the following example\. 

**Example Get Your Reserved DB Instances**  
To get information about reserved DB instances for your AWS account, call the AWS CLI command [https://docs.aws.amazon.com/cli/latest/reference/rds/describe-reserved-db-instances.html](https://docs.aws.amazon.com/cli/latest/reference/rds/describe-reserved-db-instances.html)\.   

```
aws rds describe-reserved-db-instances
```
The command returns output similar to the following:   

```
1. RESERVATION  ReservationId     Class        Multi-AZ  Start Time                Duration  Fixed Price  Usage Price  Count  State    Description  Offering Type
2. RESERVATION  MyReservation     db.m1.small  y         2011-12-09T23:37:44.720Z  1y        455.00 USD   0.092 USD    1      retired  mysql        Partial  Upfront
```

## RDS API<a name="USER_WorkingWithReservedDBInstances.API"></a>

You can use the RDS API to work with reserved DB instances as shown in the following examples\. 

**Example Get Available Reserved DB Instance Offerings**  
To get information about available reserved DB instance offerings, call the Amazon RDS API function [https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_DescribeReservedDBInstancesOfferings.html](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_DescribeReservedDBInstancesOfferings.html)\.   

```
https://rds.us-east-1.amazonaws.com/
   ?Action=DescribeReservedDBInstancesOfferings
   &SignatureMethod=HmacSHA256
   &SignatureVersion=4
   &Version=2014-09-01
   &X-Amz-Algorithm=AWS4-HMAC-SHA256
   &X-Amz-Credential=AKIADQKE4SARGYLE/20140411/us-east-1/rds/aws4_request
   &X-Amz-Date=20140411T203327Z
   &X-Amz-SignedHeaders=content-type;host;user-agent;x-amz-content-sha256;x-amz-date
   &X-Amz-Signature=545f04acffeb4b80d2e778526b1c9da79d0b3097151c24f28e83e851d65422e2
```
This call returns output similar to the following:   

```
 1. <DescribeReservedDBInstancesOfferingsResponse xmlns="http://rds.amazonaws.com/doc/2014-10-31/">
 2.   <DescribeReservedDBInstancesOfferingsResult>
 3.     <ReservedDBInstancesOfferings>
 4.       <ReservedDBInstancesOffering>
 5.         <Duration>31536000</Duration>
 6.         <OfferingType>Partial Upfront</OfferingType>
 7.         <CurrencyCode>USD</CurrencyCode>
 8.         <RecurringCharges/>
 9.         <FixedPrice>1820.0</FixedPrice>
10.         <ProductDescription>mysql</ProductDescription>
11.         <UsagePrice>0.368</UsagePrice>
12.         <MultiAZ>true</MultiAZ>
13.         <ReservedDBInstancesOfferingId>438012d3-4052-4cc7-b2e3-8d3372e0e706</ReservedDBInstancesOfferingId>
14.         <DBInstanceClass>db.m1.large</DBInstanceClass>
15.       </ReservedDBInstancesOffering>
16.       <ReservedDBInstancesOffering>
17.         <Duration>31536000</Duration>
18.         <OfferingType>Partial Upfront</OfferingType>
19.         <CurrencyCode>USD</CurrencyCode>
20.         <RecurringCharges/>
21.         <FixedPrice>227.5</FixedPrice>
22.         <ProductDescription>mysql</ProductDescription>
23.         <UsagePrice>0.046</UsagePrice>
24.         <MultiAZ>false</MultiAZ>
25.         <ReservedDBInstancesOfferingId>649fd0c8-cf6d-47a0-bfa6-060f8e75e95f</ReservedDBInstancesOfferingId>
26.         <DBInstanceClass>db.m1.small</DBInstanceClass>
27.       </ReservedDBInstancesOffering>
28.     </ReservedDBInstancesOfferings>
29.   </DescribeReservedDBInstancesOfferingsResult>
30.   <ResponseMetadata>
31.     <RequestId>5e4ec40b-2978-11e1-9e6d-771388d6ed6b</RequestId>
32.   </ResponseMetadata>
33. </DescribeReservedDBInstancesOfferingsResponse>
```

After you have information about the available reserved DB instance offerings, you can use the information to purchase an offering as shown in the following example\. 

**Example Purchase a Reserved DB Instance**  
To purchase a reserved DB instance, call the Amazon RDS API operation [https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_PurchaseReservedDBInstancesOffering.html](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_PurchaseReservedDBInstancesOffering.html) with the following parameters:   
+ `--reserved-db-instances-offering-id` – the id of the offering that you want to purchase\. See the preceding example to get the offering ID\. 
+ `--reserved-db-instance-id` – you can assign your own identifier to the reserved DB instances that you purchase to help you track them\.  
The following example purchases the reserved DB instance offering with ID *649fd0c8\-cf6d\-47a0\-bfa6\-060f8e75e95f*, and assigns the identifier of *MyReservation*\.   

```
https://rds.us-east-1.amazonaws.com/
   ?Action=PurchaseReservedDBInstancesOffering
   &ReservedDBInstanceId=MyReservation
   &ReservedDBInstancesOfferingId=438012d3-4052-4cc7-b2e3-8d3372e0e706
   &DBInstanceCount=10
   &SignatureMethod=HmacSHA256
   &SignatureVersion=4
   &Version=2014-09-01
   &X-Amz-Algorithm=AWS4-HMAC-SHA256
   &X-Amz-Credential=AKIADQKE4SARGYLE/20140415/us-east-1/rds/aws4_request
   &X-Amz-Date=20140415T232655Z
   &X-Amz-SignedHeaders=content-type;host;user-agent;x-amz-content-sha256;x-amz-date
   &X-Amz-Signature=c2ac761e8c8f54a8c0727f5a87ad0a766fbb0024510b9aa34ea6d1f7df52fb11
```
This call returns output similar to the following:   

```
 1. <PurchaseReservedDBInstancesOfferingResponse xmlns="http://rds.amazonaws.com/doc/2014-10-31/">
 2.   <PurchaseReservedDBInstancesOfferingResult>
 3.     <ReservedDBInstance>
 4.       <OfferingType>Partial Upfront</OfferingType>
 5.       <CurrencyCode>USD</CurrencyCode>
 6.       <RecurringCharges/>
 7.       <ProductDescription>mysql</ProductDescription>
 8.       <ReservedDBInstancesOfferingId>649fd0c8-cf6d-47a0-bfa6-060f8e75e95f</ReservedDBInstancesOfferingId>
 9.       <MultiAZ>true</MultiAZ>
10.       <State>payment-pending</State>
11.       <ReservedDBInstanceId>MyReservation</ReservedDBInstanceId>
12.       <DBInstanceCount>10</DBInstanceCount>
13.       <StartTime>2011-12-18T23:24:56.577Z</StartTime>
14.       <Duration>31536000</Duration>
15.       <FixedPrice>123.0</FixedPrice>
16.       <UsagePrice>0.123</UsagePrice>
17.       <DBInstanceClass>db.m1.small</DBInstanceClass>
18.     </ReservedDBInstance>
19.   </PurchaseReservedDBInstancesOfferingResult>
20.   <ResponseMetadata>
21.     <RequestId>7f099901-29cf-11e1-bd06-6fe008f046c3</RequestId>
22.   </ResponseMetadata>
23. </PurchaseReservedDBInstancesOfferingResponse>
```

After you have purchased reserved DB instances, you can get information about your reserved DB instances as shown in the following example\. 

**Example Get Your Reserved DB Instances**  
To get information about reserved DB instances for your AWS account, call the Amazon RDS API operation [https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_DescribeReservedDBInstances.html](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_DescribeReservedDBInstances.html)\.   

```
https://rds.us-west-2.amazonaws.com/
   ?Action=DescribeReservedDBInstances
   &SignatureMethod=HmacSHA256 
   &SignatureVersion=4
   &Version=2014-09-01
   &X-Amz-Algorithm=AWS4-HMAC-SHA256
   &X-Amz-Credential=AKIADQKE4SARGYLE/20140420/us-west-2/rds/aws4_request
   &X-Amz-Date=20140420T162211Z
   &X-Amz-SignedHeaders=content-type;host;user-agent;x-amz-content-sha256;x-amz-date
   &X-Amz-Signature=3312d17a4c43bcd209bc22a0778dd23e73f8434254abbd7ac53b89ade3dae88e
```
The API returns output similar to the following:   

```
 1. <DescribeReservedDBInstancesResponse xmlns="http://rds.amazonaws.com/doc/2014-10-31/">
 2.   <DescribeReservedDBInstancesResult>
 3.     <ReservedDBInstances>
 4.       <ReservedDBInstance>
 5.         <OfferingType>Partial Upfront</OfferingType>
 6.         <CurrencyCode>USD</CurrencyCode>
 7.         <RecurringCharges/>
 8.         <ProductDescription>mysql</ProductDescription>
 9.         <ReservedDBInstancesOfferingId>649fd0c8-cf6d-47a0-bfa6-060f8e75e95f</ReservedDBInstancesOfferingId>
10.         <MultiAZ>false</MultiAZ>
11.         <State>payment-failed</State>
12.         <ReservedDBInstanceId>MyReservation</ReservedDBInstanceId>
13.         <DBInstanceCount>1</DBInstanceCount>
14.         <StartTime>2010-12-15T00:25:14.131Z</StartTime>
15.         <Duration>31536000</Duration>
16.         <FixedPrice>227.5</FixedPrice>
17.         <UsagePrice>0.046</UsagePrice>
18.         <DBInstanceClass>db.m1.small</DBInstanceClass>
19.       </ReservedDBInstance>
20.       <ReservedDBInstance>
21.         <OfferingType>Partial Upfront</OfferingType>
22.         <CurrencyCode>USD</CurrencyCode>
23.         <RecurringCharges/>
24.         <ProductDescription>mysql</ProductDescription>
25.         <ReservedDBInstancesOfferingId>649fd0c8-cf6d-47a0-bfa6-060f8e75e95f</ReservedDBInstancesOfferingId>
26.         <MultiAZ>false</MultiAZ>
27.         <State>payment-failed</State>
28.         <ReservedDBInstanceId>MyReservation</ReservedDBInstanceId>
29.         <DBInstanceCount>1</DBInstanceCount>
30.         <StartTime>2010-12-15T01:07:22.275Z</StartTime>
31.         <Duration>31536000</Duration>
32.         <FixedPrice>227.5</FixedPrice>
33.         <UsagePrice>0.046</UsagePrice>
34.         <DBInstanceClass>db.m1.small</DBInstanceClass>
35.       </ReservedDBInstance>
36.     </ReservedDBInstances>
37.   </DescribeReservedDBInstancesResult>
38.   <ResponseMetadata>
39.     <RequestId>23400d50-2978-11e1-9e6d-771388d6ed6b</RequestId>
40.   </ResponseMetadata>
41. </DescribeReservedDBInstancesResponse>
```