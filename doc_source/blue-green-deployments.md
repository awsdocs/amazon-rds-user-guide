# Using Amazon RDS Blue/Green Deployments for database updates<a name="blue-green-deployments"></a>

A blue/green deployment copies a production database environment to a separate, synchronized staging environment\. By using Amazon RDS Blue/Green Deployments, you can make changes to the database in the staging environment without affecting the production environment\. For example, you can upgrade the major or minor DB engine version, change database parameters, or make schema changes in the staging environment\. When you are ready, you can promote the staging environment to be the new production database environment, with downtime typically under one minute\.

**Topics**
+ [Overview of Amazon RDS Blue/Green Deployments](blue-green-deployments-overview.md)
+ [Creating a blue/green deployment](blue-green-deployments-creating.md)
+ [Viewing a blue/green deployment](blue-green-deployments-viewing.md)
+ [Switching a blue/green deployment](blue-green-deployments-switching.md)
+ [Deleting a blue/green deployment](blue-green-deployments-deleting.md)