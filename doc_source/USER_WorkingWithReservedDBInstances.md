# Reserved DB instances for Amazon RDS<a name="USER_WorkingWithReservedDBInstances"></a>

Using reserved DB instances, you can reserve a DB instance for a one\- or three\-year term\. Reserved DB instances provide you with a significant discount compared to on\-demand DB instance pricing\. Reserved DB instances are not physical instances, but rather a billing discount applied to the use of certain on\-demand DB instances in your account\. Discounts for reserved DB instances are tied to instance type and AWS Region\.

The general process for working with reserved DB instances is: First get information about available reserved DB instance offerings, then purchase a reserved DB instance offering, and finally get information about your existing reserved DB instances\.

## Overview of reserved DB instances<a name="USER_WorkingWithReservedDBInstances.Overview"></a>

When you purchase a reserved DB instance in Amazon RDS, you purchase a commitment to getting a discounted rate, on a specific DB instance type, for the duration of the reserved DB instance\. To use an Amazon RDS reserved DB instance, you create a new DB instance just like you do for an on\-demand instance\.

The new DB instance that you create must match the specifications of the reserved DB instance—same DB engine, DB instance type, license type \(license\-included or bring\-your\-own\-license\), and AWS Region\.

If the specifications of the new DB instance match an existing reserved DB instance for your account, you are billed at the discounted rate offered for the reserved DB instance\. Otherwise, the DB instance is billed at an on\-demand rate\. 

You can modify a reserved DB instance\. If the modification is within the specifications of the reserved DB instance, part or all of the discount still applies to the modified DB instance\. If the modification is outside the specifications, such as changing the instance class, the discount no longer applies\. For more information, see [Size\-flexible reserved DB instances](#USER_WorkingWithReservedDBInstances.SizeFlexible)\.

For more information about reserved DB instances, including pricing, see [Amazon RDS reserved instances](http://aws.amazon.com/rds/reserved-instances/#2)\.

### Offering types<a name="USER_WorkingWithReservedDBInstances.OfferingTypes"></a>

Reserved DB instances are available in three varieties—No Upfront, Partial Upfront, and All Upfront—that let you optimize your Amazon RDS costs based on your expected usage\.

**No Upfront**  
This option provides access to a reserved DB instance without requiring an upfront payment\. Your No Upfront reserved DB instance bills a discounted hourly rate for every hour within the term, regardless of usage, and no upfront payment is required\. This option is only available as a one\-year reservation\.

**Partial Upfront**  
This option requires a part of the reserved DB instance to be paid upfront\. The remaining hours in the term are billed at a discounted hourly rate, regardless of usage\. This option is the replacement for the previous Heavy Utilization option\.

**All Upfront**  
Full payment is made at the start of the term, with no other costs incurred for the remainder of the term regardless of the number of hours used\.

If you are using consolidated billing, all the accounts in the organization are treated as one account\. This means that all accounts in the organization can receive the hourly cost benefit of reserved DB instances that are purchased by any other account\. For more information about consolidated billing, see [Amazon RDS reserved DB instances](https://docs.aws.amazon.com/awsaccountbilling/latest/aboutv2/consolidatedbilling-other.html#consolidatedbilling-rds) in the *AWS Billing and Cost Management User Guide*\.

### Size\-flexible reserved DB instances<a name="USER_WorkingWithReservedDBInstances.SizeFlexible"></a>

When you purchase a reserved DB instance, one thing that you specify is the instance class, for example db\.r5\.large\. For more information about instance classes, see [DB instance classes](Concepts.DBInstanceClass.md)\.

If you have a DB instance, and you need to scale it to larger capacity, your reserved DB instance is automatically applied to your scaled DB instance\. That is, your reserved DB instances are automatically applied across all DB instance class sizes\. Size\-flexible reserved DB instances are available for DB instances with the same AWS Region and database engine\. Size\-flexible reserved DB instances can only scale in their instance class type\. For example, a reserved DB instance for a db\.r5\.large can apply to a db\.r5\.xlarge, but not to a db\.r6g\.large, because db\.r5 and db\.r6g are different instance class types\.

Reserved DB instance benefits also apply for both Multi\-AZ and Single\-AZ configurations\. Flexibility means that you can move freely between configurations within the same DB instance class type\. For example, you can move from a Single\-AZ deployment running on one large DB instance \(four normalized units\) to a Multi\-AZ deployment running on two small DB instances \(2\*2 = 4 normalized units\)\.

Size\-flexible reserved DB instances are available for the following Amazon RDS database engines:
+ MariaDB
+ MySQL
+ Oracle, Bring Your Own License
+ PostgreSQL

For details about using size\-flexible reserved instances with Aurora, see [Reserved DB instances for Aurora](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/USER_WorkingWithReservedDBInstances.html)\. 

You can compare usage for different reserved DB instance sizes by using normalized units\. For example, one unit of usage on two db\.r3\.large DB instances is equivalent to eight normalized units of usage on one db\.r3\.small\. The following table shows the number of normalized units for each DB instance size\.


| Instance size | Single\-AZ normalized units \(deployment with one DB instance\) | Multi\-AZ normalized units \(deployment with two DB instances\) | 
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

### Reserved DB instance billing example<a name="USER_WorkingWithReservedDBInstances.BillingExample"></a>

The price for a reserved DB instance doesn't include regular costs associated with storage, backups, and I/O\. The following example illustrates the total cost per month for a reserved DB instance:
+ An RDS for MySQL reserved Single\-AZ db\.r5\.large DB instance class in US East \(N\. Virginia\) with the No Upfront option at a cost of $0\.12 for the instance, or $90 per month
+ 400 GiB of General Purpose SSD \(gp2\) storage at a cost of 0\.115 per GiB per month, or $45\.60 per month
+ 600 GiB of backup storage at $0\.095, or $19 per month \(400 GiB free\)

Add all of these options \($90 \+ $45\.60 \+ $19\) with the reserved DB instance, and the total cost per month is $154\.60\.

If you choose to use an on\-demand DB instance instead of a reserved DB instance, an RDS for MySQL Single\-AZ db\.r5\.large DB instance class in US East \(N\. Virginia\) costs $0\.1386 per hour, or $101\.18 per month\. So, for an on\-demand DB instance, add all of these options \($101\.18 \+ $45\.60 \+ $19\), and the total cost per month is $165\.78\. You save a little over $11 per month by using the reserved DB instance\.

**Note**  
The prices in this example are sample prices and might not match actual prices\. For Amazon RDS pricing information, see [Amazon RDS pricing](https://aws.amazon.com/rds/pricing)\.

### Deleting a reserved DB instance<a name="USER_WorkingWithReservedDBInstances.Cancelling"></a>

The terms for a reserved DB instance involve a one\-year or three\-year commitment\. You can't cancel a reserved DB instance\. However, you can delete a DB instance that is covered by a reserved DB instance discount\. The process for deleting a DB instance that is covered by a reserved DB instance discount is the same as for any other DB instance\.

You're billed for the upfront costs regardless of whether you use the resources\.

If you delete a DB instance that is covered by a reserved DB instance discount, you can launch another DB instance with compatible specifications\. In this case, you continue to get the discounted rate during the reservation term \(one or three years\)\.

## Working with reserved DB instances<a name="USER_WorkingWithReservedDBInstances.WorkingWith"></a>

You can use the AWS Management Console, the AWS CLI, and the RDS API to work with reserved DB instances\.

### Console<a name="USER_WorkingWithReservedDBInstances.CON"></a>

You can use the AWS Management Console to work with reserved DB instances as shown in the following procedures\. 

**To get pricing and information about available reserved DB instance offerings**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Reserved instances**\. 

1. Choose **Purchase Reserved DB Instance**\.

1. For **Product description**, choose the DB engine and licensing type\.

1. For **DB instance class**, choose the DB instance class\.

1. For **Deployment Option**, choose whether you want a Multi\-AZ deployment\.

1. For **Term**, choose the length of time you want the DB instance reserved\.

1. For **Offering type**, choose the offering type\. 

   After you select the offering type, you can see the pricing information\. 
**Important**  
Choose **Cancel** to avoid purchasing the reserved DB instance and incurring any charges\. 

After you have information about the available reserved DB instance offerings, you can use the information to purchase an offering as shown in the following procedure\. 

**To purchase a reserved DB instance**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Reserved instances**\. 

1. Choose **Purchase reserved DB instance**\.

1. For **Product description**, choose the DB engine and licensing type\.

1. For **DB instance class**, choose the DB instance class\.

1. For **Multi\-AZ deployment**, choose whether you want a Multi\-AZ deployment\.

1. For **Term**, choose the length of time you want the DB instance reserved\.

1. For **Offering type**, choose the offering type\.

   After you choose the offering type, you can see the pricing information\.  
![\[Purchase reserved DB instance console\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/reservedinstance.png)

1. \(Optional\) You can assign your own identifier to the reserved DB instances that you purchase to help you track them\. For **Reserved Id**, type an identifier for your reserved DB instance\.

1. Choose **Submit**\.

   Your reserved DB instance is purchased, then displayed in the **Reserved instances** list\.

After you have purchased reserved DB instances, you can get information about your reserved DB instances as shown in the following procedure\.

**To get information about reserved DB instances for your AWS account**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the **Navigation** pane, choose **Reserved instances**\.

   The reserved DB instances for your account appear\. To see detailed information about a particular reserved DB instance, choose that instance in the list\. You can then see detailed information about that instance in the detail pane at the bottom of the console\.

### AWS CLI<a name="USER_WorkingWithReservedDBInstances.CLI"></a>

You can use the AWS CLI to work with reserved DB instances as shown in the following examples\.

**Example of getting available reserved DB instance offerings**  
To get information about available reserved DB instance offerings, call the AWS CLI command [https://docs.aws.amazon.com/cli/latest/reference/rds/describe-reserved-db-instances-offerings.html](https://docs.aws.amazon.com/cli/latest/reference/rds/describe-reserved-db-instances-offerings.html)\.  

```
aws rds describe-reserved-db-instances-offerings
```
This call returns output similar to the following:   

```
 1. OFFERING  OfferingId                            Class         Multi-AZ  Duration  Fixed Price  Usage Price  Description  Offering Type
 2. OFFERING  438012d3-4052-4cc7-b2e3-8d3372e0e706  db.r3.large   y         1y        1820.00 USD  0.368 USD    mysql        Partial  Upfront
 3. OFFERING  649fd0c8-cf6d-47a0-bfa6-060f8e75e95f  db.r3.small   n         1y         227.50 USD  0.046 USD    mysql        Partial  Upfront
 4. OFFERING  123456cd-ab1c-47a0-bfa6-12345667232f  db.r3.small   n         1y         162.00 USD   0.00 USD    mysql        All      Upfront
 5.     Recurring Charges:   Amount  Currency  Frequency        
 6.     Recurring Charges:   0.123   USD       Hourly
 7. OFFERING  123456cd-ab1c-37a0-bfa6-12345667232d  db.r3.large   y         1y         700.00 USD   0.00 USD    mysql        All      Upfront
 8.     Recurring Charges:   Amount  Currency  Frequency
 9.     Recurring Charges:   1.25    USD       Hourly
10. OFFERING  123456cd-ab1c-17d0-bfa6-12345667234e  db.r3.xlarge  n         1y        4242.00 USD   2.42 USD    mysql        No       Upfront
```

After you have information about the available reserved DB instance offerings, you can use the information to purchase an offering\.

To purchase a reserved DB instance, use the AWS CLI command [https://docs.aws.amazon.com/cli/latest/reference/rds/purchase-reserved-db-instances-offering.html](https://docs.aws.amazon.com/cli/latest/reference/rds/purchase-reserved-db-instances-offering.html) with the following parameters:
+ `--reserved-db-instances-offering-id` – The ID of the offering that you want to purchase\. See the preceding example to get the offering ID\.
+ `--reserved-db-instance-id` – You can assign your own identifier to the reserved DB instances that you purchase to help track them\.

**Example of purchasing a reserved DB instance**  
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
2. RESERVATION  MyReservation      db.r3.small  y         2011-12-19T00:30:23.247Z  1y        455.00 USD   0.092 USD    1      payment-pending  mysql        Partial  Upfront
```

After you have purchased reserved DB instances, you can get information about your reserved DB instances\.

To get information about reserved DB instances for your AWS account, call the AWS CLI command [https://docs.aws.amazon.com/cli/latest/reference/rds/describe-reserved-db-instances.html](https://docs.aws.amazon.com/cli/latest/reference/rds/describe-reserved-db-instances.html), as shown in the following example\.

**Example of getting your reserved DB instances**  

```
aws rds describe-reserved-db-instances
```
The command returns output similar to the following:   

```
1. RESERVATION  ReservationId     Class        Multi-AZ  Start Time                Duration  Fixed Price  Usage Price  Count  State    Description  Offering Type
2. RESERVATION  MyReservation     db.r3.small  y         2011-12-09T23:37:44.720Z  1y        455.00 USD   0.092 USD    1      retired  mysql        Partial  Upfront
```

### RDS API<a name="USER_WorkingWithReservedDBInstances.API"></a>

You can use the RDS API to work with reserved DB instances:
+ To get information about available reserved DB instance offerings, call the Amazon RDS API operation [https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_DescribeReservedDBInstancesOfferings.html](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_DescribeReservedDBInstancesOfferings.html)\.
+ After you have information about the available reserved DB instance offerings, you can use the information to purchase an offering\. Call the [https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_PurchaseReservedDBInstancesOffering.html](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_PurchaseReservedDBInstancesOffering.html) RDS API operation with the following parameters:
  + `--reserved-db-instances-offering-id` – The ID of the offering that you want to purchase\.
  + `--reserved-db-instance-id` – You can assign your own identifier to the reserved DB instances that you purchase to help track them\.
+ After you have purchased reserved DB instances, you can get information about your reserved DB instances\. Call the [https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_DescribeReservedDBInstances.html](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_DescribeReservedDBInstances.html) RDS API operation\.

## Viewing the billing for your reserved DB instances<a name="reserved-instances-billing"></a>

You can view the billing for your reserved DB instances in the Billing Dashboard in the AWS Management Console\.

**To view reserved DB instance billing**

1. Sign in to the AWS Management Console\.

1. From the **account menu** at the upper right, choose **Billing Dashboard**\.

1. Choose **Bill Details** at the upper right of the dashboard\.

1. Under **AWS Service Charges**, expand **Relational Database Service**\.

1. Expand the AWS Region where your reserved DB instances are, for example **US West \(Oregon\)**\.

   Your reserved DB instances and their hourly charges for the current month are shown under **Amazon Relational Database Service for *Database Engine* Reserved Instances**\.  
![\[View monthly costs for a reserved DB instance\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/ri-db-billing1.png)

   The reserved DB instance in this example was purchased All Upfront, so there are no hourly charges\.

1. Choose the **Cost Explorer** \(bar graph\) icon next to the **Reserved Instances** heading\.

   The Cost Explorer displays the **Monthly EC2 running hours costs and usage** graph\.

1. Clear the **Usage Type Group** filter to the right of the graph\.

1. Choose the time period and time unit for which you want to examine usage costs\.

   The following example shows usage costs for on\-demand and reserved DB instances for the year to date by month\.  
![\[View usage costs for on-demand and reserved DB instances\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/ri-db-billing2.png)

   The reserved DB instance costs from January through June 2021 are monthly charges for a Partial Upfront instance, while the cost in August 2021 is a one\-time charge for an All Upfront instance\.

   The reserved instance discount for the Partial Upfront instance expired in June 2021, but the DB instance wasn't deleted\. After the expiration date, it was simply charged at the on\-demand rate\.