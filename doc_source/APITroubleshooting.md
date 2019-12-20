# Troubleshooting Applications on Amazon RDS<a name="APITroubleshooting"></a>

Amazon RDS provides specific and descriptive errors to help you troubleshoot problems while interacting with the Amazon RDS API\.

**Topics**
+ [Retrieving Errors](#RetrievingErrors)
+ [Troubleshooting Tips](#TroubleshootingTipss)

 For information about troubleshooting for Amazon RDS DB instances, see [Troubleshooting for Amazon RDS](CHAP_Troubleshooting.md)\. 

## Retrieving Errors<a name="RetrievingErrors"></a>

Typically, you want your application to check whether a request generated an error before you spend any time processing results\. The easiest way to find out if an error occurred is to look for an `Error` node in the response from the Amazon RDS API\.

XPath syntax provides a simple way to search for the presence of an `Error` node, as well as an easy way to retrieve the error code and message\. The following code snippet uses Perl and the XML::XPath module to determine if an error occurred during a request\. If an error occurred, the code prints the first error code and message in the response\. 

```
use XML::XPath; 
    my $xp = XML::XPath->new(xml =>$response); 
    if ( $xp->find("//Error") ) 
    {print "There was an error processing your request:\n", " Error code: ",
    $xp->findvalue("//Error[1]/Code"), "\n", " ",
    $xp->findvalue("//Error[1]/Message"), "\n\n"; }
```

## Troubleshooting Tips<a name="TroubleshootingTipss"></a>

 We recommend the following processes to diagnose and resolve problems with the Amazon RDS API\. 
+ Verify that Amazon RDS is operating normally in the AWS Region you are targeting by visiting [http://status\.aws\.amazon\.com](http://status.aws.amazon.com/)\.
+ Check the structure of your request

  Each Amazon RDS operation has a reference page in the *Amazon RDS API Reference*\. Double\-check that you are using parameters correctly\. In order to give you ideas regarding what might be wrong, look at the sample requests or user scenarios to see if those examples are doing similar operations\.
+ Check the forum

  Amazon RDS has a development community forum where you can search for solutions to problems others have experienced along the way\. To view the forum, go to 

   [https://forums\.aws\.amazon\.com/](https://forums.aws.amazon.com/) 