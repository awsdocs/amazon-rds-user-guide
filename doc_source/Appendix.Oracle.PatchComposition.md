# Oracle Database Engine Release Notes<a name="Appendix.Oracle.PatchComposition"></a>

Updates to your Amazon RDS for Oracle DB instances keep them current\. If you apply updates, you can be confident that your DB instance is running a version of the database software that has been tested by both Oracle and Amazon\. We don't support applying one\-off patches to individual DB instances\. 

You can specify any currently supported Oracle version when creating a new DB instance\. You can specify the major version \(such as Oracle 12\.1\), and any supported minor version for the specified major version\. If no version is specified, Amazon RDS defaults to a supported version, typically the most recent version\. If a major version is specified but a minor version is not, Amazon RDS defaults to a recent release of the major version that you have specified\. To see a list of supported versions and defaults for newly created DB instances, use the [ `describe-db-engine-versions`](https://docs.aws.amazon.com/cli/latest/reference/rds/describe-db-engine-versions.html) AWS CLI command\.

## Oracle Versions 19\.0\.0, 18\.0\.0, and 12\.2\.0\.1<a name="Appendix.Oracle.PatchComposition.180-122"></a>

For Amazon RDS for Oracle versions 19\.0\.0\.0, 18\.0\.0\.0, and 12\.2\.0\.1, Amazon RDS incorporates bug fixes from Oracle by using Release Updates \(RUs\) and Release Updates Revisions \(RURs\)\. We don't support applying one\-off patches to individual DB instances\.

To find what RUs and RURs are applied to Amazon RDS for Oracle versions 19\.0\.0, 18\.0\.0\.0, and 12\.2\.0\.1, see the following table\. 


****  

| RU and RUR | Version 19\.0\.0\.0 | Version 18\.0\.0\.0 | Version 12\.2\.0\.1 | 
| --- | --- | --- | --- | 
| 2020 July | [19\.0\.0\.0\.ru\-2020\-07\.rur\-2020\-07\.r1](Appendix.Oracle.RU-RUR.19.0.0.0.md#Appendix.Oracle.RU-RUR.19.0.0.0.ru-2020-07.rur-2020-07.r1) | [18\.0\.0\.0\.ru\-2020\-07\.rur\-2020\-07\.r1](Appendix.Oracle.RU-RUR.18.0.0.0.md#Appendix.Oracle.RU-RUR.18.0.0.0.ru-2020-07.rur-2020-07.r1) | [12\.2\.0\.1\.ru\-2020\-07\.rur\-2020\-07\.r1](Appendix.Oracle.RU-RUR.12.2.0.1.md#Appendix.Oracle.RU-RUR.12.2.0.1.ru-2020-07.rur-2020-07.r1) | 
| 2020 April | [19\.0\.0\.0\.ru\-2020\-04\.rur\-2020\-04\.r1](Appendix.Oracle.RU-RUR.19.0.0.0.md#Appendix.Oracle.RU-RUR.19.0.0.0.ru-2020-04.rur-2020-04.r1) | [18\.0\.0\.0\.ru\-2020\-04\.rur\-2020\-04\.r1](Appendix.Oracle.RU-RUR.18.0.0.0.md#Appendix.Oracle.RU-RUR.18.0.0.0.ru-2020-04.rur-2020-04.r1) | [12\.2\.0\.1\.ru\-2020\-04\.rur\-2020\-04\.r1](Appendix.Oracle.RU-RUR.12.2.0.1.md#Appendix.Oracle.RU-RUR.12.2.0.1.ru-2020-04.rur-2020-04.r1) | 
| 2020 January | [19\.0\.0\.0\.ru\-2020\-01\.rur\-2020\-01\.r1](Appendix.Oracle.RU-RUR.19.0.0.0.md#Appendix.Oracle.RU-RUR.19.0.0.0.ru-2020-01.rur-2020-01.r1) | [18\.0\.0\.0\.ru\-2020\-01\.rur\-2020\-01\.r1](Appendix.Oracle.RU-RUR.18.0.0.0.md#Appendix.Oracle.RU-RUR.18.0.0.0.ru-2020-01.rur-2020-01.r1) | [12\.2\.0\.1\.ru\-2020\-01\.rur\-2020\-01\.r1](Appendix.Oracle.RU-RUR.12.2.0.1.md#Appendix.Oracle.RU-RUR.12.2.0.1.ru-2020-01.rur-2020-01.r1) | 
| 2019 October | [19\.0\.0\.0\.ru\-2019\-10\.rur\-2019\-10\.r1](Appendix.Oracle.RU-RUR.19.0.0.0.md#Appendix.Oracle.RU-RUR.19.0.0.0.ru-2019-10.rur-2019-10.r1) | [18\.0\.0\.0\.ru\-2019\-10\.rur\-2019\-10\.r1](Appendix.Oracle.RU-RUR.18.0.0.0.md#Appendix.Oracle.RU-RUR.18.0.0.0.ru-2019-10.rur-2019-10.r1) | [12\.2\.0\.1\.ru\-2019\-10\.rur\-2019\-10\.r1](Appendix.Oracle.RU-RUR.12.2.0.1.md#Appendix.Oracle.RU-RUR.12.2.0.1.ru-2019-10.rur-2019-10.r1) | 
| 2019 July | [19\.0\.0\.0\.ru\-2019\-07\.rur\-2019\-07\.r1](Appendix.Oracle.RU-RUR.19.0.0.0.md#Appendix.Oracle.RU-RUR.19.0.0.0.ru-2019-07.rur-2019-07.r1) | [18\.0\.0\.0\.ru\-2019\-07\.rur\-2019\-07\.r1](Appendix.Oracle.RU-RUR.18.0.0.0.md#Appendix.Oracle.RU-RUR.18.0.0.0.ru-2019-07.rur-2019-07.r1) | [12\.2\.0\.1\.ru\-2019\-07\.rur\-2019\-07\.r1](Appendix.Oracle.RU-RUR.12.2.0.1.md#Appendix.Oracle.RU-RUR.12.2.0.1.ru-2019-07.rur-2019-07.r1) | 
| 2019 April | — | — | [12\.2\.0\.1\.ru\-2019\-04\.rur\-2019\-04\.r1](Appendix.Oracle.RU-RUR.12.2.0.1.md#Appendix.Oracle.RU-RUR.12.2.0.1.ru-2019-04.rur-2019-04.r1) | 
| 2019 January | — | — | [12\.2\.0\.1\.ru\-2019\-01\.rur\-2019\-01\.r1](Appendix.Oracle.RU-RUR.12.2.0.1.md#Appendix.Oracle.RU-RUR.12.2.0.1.ru-2019-01.rur-2019-01.r1) | 
| 2018 October | — | — | [12\.2\.0\.1\.ru\-2018\-10\.rur\-2018\-10\.r1](Appendix.Oracle.RU-RUR.12.2.0.1.md#Appendix.Oracle.RU-RUR.12.2.0.1.ru-2018-10.rur-2018-10.r1) | 

## Oracle Versions 12\.1\.0\.2 and 11\.2\.0\.4<a name="Appendix.Oracle.PatchComposition.121-112"></a>

For Amazon RDS for Oracle versions 12\.1\.0\.2 and 11\.2\.0\.4, Amazon RDS incorporates bug fixes from Oracle by using their quarterly Database Patch Set Updates \(PSUs\)\. If you apply updates, you can be confident that your DB instance is running a version of the database software that has been tested by both Oracle and Amazon\. We don't support applying one\-off patches to individual DB instances\. 

To find what Oracle Patch Set Updates \(PSUs\) are applied to Amazon RDS for Oracle versions 12\.1\.0\.2 and 11\.2\.0\.4, see the following table\. 


****  

| PSU | Version 12\.1\.0\.2 | Version 11\.2\.0\.4 | 
| --- | --- | --- | 
| 2020 July | [12\.1\.0\.2\.v21](Appendix.Oracle.PatchComposition.12.1.0.2.md#Appendix.Oracle.PatchComposition.12.1.0.2.v21) | [11\.2\.0\.4\.v25](Appendix.Oracle.PatchComposition.11.2.0.4.md#Appendix.Oracle.PatchComposition.11.2.0.4.v25) | 
| 2020 April | [12\.1\.0\.2\.v20](Appendix.Oracle.PatchComposition.12.1.0.2.md#Appendix.Oracle.PatchComposition.12.1.0.2.v20) | [11\.2\.0\.4\.v24](Appendix.Oracle.PatchComposition.11.2.0.4.md#Appendix.Oracle.PatchComposition.11.2.0.4.v24) | 
| 2020 January | [12\.1\.0\.2\.v19](Appendix.Oracle.PatchComposition.12.1.0.2.md#Appendix.Oracle.PatchComposition.12.1.0.2.v19) | [11\.2\.0\.4\.v23](Appendix.Oracle.PatchComposition.11.2.0.4.md#Appendix.Oracle.PatchComposition.11.2.0.4.v23) | 
| 2019 October | [12\.1\.0\.2\.v18](Appendix.Oracle.PatchComposition.12.1.0.2.md#Appendix.Oracle.PatchComposition.12.1.0.2.v18) | [11\.2\.0\.4\.v22](Appendix.Oracle.PatchComposition.11.2.0.4.md#Appendix.Oracle.PatchComposition.11.2.0.4.v22) | 
| 2019 July | [12\.1\.0\.2\.v17](Appendix.Oracle.PatchComposition.12.1.0.2.md#Appendix.Oracle.PatchComposition.12.1.0.2.v17) | [11\.2\.0\.4\.v21](Appendix.Oracle.PatchComposition.11.2.0.4.md#Appendix.Oracle.PatchComposition.11.2.0.4.v21) | 
| 2019 April | [12\.1\.0\.2\.v16](Appendix.Oracle.PatchComposition.12.1.0.2.md#Appendix.Oracle.PatchComposition.12.1.0.2.v16) | [11\.2\.0\.4\.v20](Appendix.Oracle.PatchComposition.11.2.0.4.md#Appendix.Oracle.PatchComposition.11.2.0.4.v20) | 
| 2019 January | [12\.1\.0\.2\.v15](Appendix.Oracle.PatchComposition.12.1.0.2.md#Appendix.Oracle.PatchComposition.12.1.0.2.v15) | [11\.2\.0\.4\.v19](Appendix.Oracle.PatchComposition.11.2.0.4.md#Appendix.Oracle.PatchComposition.11.2.0.4.v19) | 
| 2018 October | [12\.1\.0\.2\.v14](Appendix.Oracle.PatchComposition.12.1.0.2.md#Appendix.Oracle.PatchComposition.12.1.0.2.v14) | [11\.2\.0\.4\.v18](Appendix.Oracle.PatchComposition.11.2.0.4.md#Appendix.Oracle.PatchComposition.11.2.0.4.v18) | 
| 2018 July | [12\.1\.0\.2\.v13](Appendix.Oracle.PatchComposition.12.1.0.2.md#Appendix.Oracle.PatchComposition.12.1.0.2.v13) | [11\.2\.0\.4\.v17](Appendix.Oracle.PatchComposition.11.2.0.4.md#Appendix.Oracle.PatchComposition.11.2.0.4.v17) | 
| 2018 April | [12\.1\.0\.2\.v12](Appendix.Oracle.PatchComposition.12.1.0.2.md#Appendix.Oracle.PatchComposition.12.1.0.2.v12) | [11\.2\.0\.4\.v16](Appendix.Oracle.PatchComposition.11.2.0.4.md#Appendix.Oracle.PatchComposition.11.2.0.4.v16) | 
| 2018 January | [12\.1\.0\.2\.v11](Appendix.Oracle.PatchComposition.12.1.0.2.md#Appendix.Oracle.PatchComposition.12.1.0.2.v11) | [11\.2\.0\.4\.v15](Appendix.Oracle.PatchComposition.11.2.0.4.md#Appendix.Oracle.PatchComposition.11.2.0.4.v15) | 
| 2017 October | [12\.1\.0\.2\.v10](Appendix.Oracle.PatchComposition.12.1.0.2.md#Appendix.Oracle.PatchComposition.12.1.0.2.v10) | [11\.2\.0\.4\.v14](Appendix.Oracle.PatchComposition.11.2.0.4.md#Appendix.Oracle.PatchComposition.11.2.0.4.v14) | 
| 2017 July | [12\.1\.0\.2\.v9](Appendix.Oracle.PatchComposition.12.1.0.2.md#Appendix.Oracle.PatchComposition.12.1.0.2.v9) | [11\.2\.0\.4\.v13](Appendix.Oracle.PatchComposition.11.2.0.4.md#Appendix.Oracle.PatchComposition.11.2.0.4.v13) | 
| 2017 April | [12\.1\.0\.2\.v8](Appendix.Oracle.PatchComposition.12.1.0.2.md#Appendix.Oracle.PatchComposition.12.1.0.2.v8) | [11\.2\.0\.4\.v12](Appendix.Oracle.PatchComposition.11.2.0.4.md#Appendix.Oracle.PatchComposition.11.2.0.4.v12) | 
| 2017 January | [12\.1\.0\.2\.v7](Appendix.Oracle.PatchComposition.12.1.0.2.md#Appendix.Oracle.PatchComposition.12.1.0.2.v7) | [11\.2\.0\.4\.v11](Appendix.Oracle.PatchComposition.11.2.0.4.md#Appendix.Oracle.PatchComposition.11.2.0.4.v11) | 
| 2016 October | [12\.1\.0\.2\.v6](Appendix.Oracle.PatchComposition.12.1.0.2.md#Appendix.Oracle.PatchComposition.12.1.0.2.v6) | [11\.2\.0\.4\.v10](Appendix.Oracle.PatchComposition.11.2.0.4.md#Appendix.Oracle.PatchComposition.11.2.0.4.v10) | 
| 2016 July | [12\.1\.0\.2\.v5](Appendix.Oracle.PatchComposition.12.1.0.2.md#Appendix.Oracle.PatchComposition.12.1.0.2.v5) | [11\.2\.0\.4\.v9](Appendix.Oracle.PatchComposition.11.2.0.4.md#Appendix.Oracle.PatchComposition.11.2.0.4.v9) | 
| 2016 April | [12\.1\.0\.2\.v4](Appendix.Oracle.PatchComposition.12.1.0.2.md#Appendix.Oracle.PatchComposition.12.1.0.2.v4) | [11\.2\.0\.4\.v8](Appendix.Oracle.PatchComposition.11.2.0.4.md#Appendix.Oracle.PatchComposition.11.2.0.4.v8) | 
| 2016 January | [12\.1\.0\.2\.v3](Appendix.Oracle.PatchComposition.12.1.0.2.md#Appendix.Oracle.PatchComposition.12.1.0.2.v3) | [11\.2\.0\.4\.v7](Appendix.Oracle.PatchComposition.11.2.0.4.md#Appendix.Oracle.PatchComposition.11.2.0.4.v7) | 
| 2015 October | [12\.1\.0\.2\.v2](Appendix.Oracle.PatchComposition.12.1.0.2.md#Appendix.Oracle.PatchComposition.12.1.0.2.v2) |  [11\.2\.0\.4\.v6](Appendix.Oracle.PatchComposition.11.2.0.4.md#Appendix.Oracle.PatchComposition.11.2.0.4.v6) [11\.2\.0\.4\.v5](Appendix.Oracle.PatchComposition.11.2.0.4.md#Appendix.Oracle.PatchComposition.11.2.0.4.v5)  | 
| 2015 April | [12\.1\.0\.2\.v1](Appendix.Oracle.PatchComposition.12.1.0.2.md#Appendix.Oracle.PatchComposition.12.1.0.2.v1) | [11\.2\.0\.4\.v4](Appendix.Oracle.PatchComposition.11.2.0.4.md#Appendix.Oracle.PatchComposition.11.2.0.4.v4) | 
| 2014 October | — | [11\.2\.0\.4\.v3](Appendix.Oracle.PatchComposition.11.2.0.4.md#Appendix.Oracle.PatchComposition.11.2.0.4.v3) | 
| 2014 July | — | [11\.2\.0\.4\.v2](Appendix.Oracle.PatchComposition.11.2.0.4.md#Appendix.Oracle.PatchComposition.11.2.0.4.v2)\(Deprecated\) | 
| 2014 January | — | [11\.2\.0\.4\.v1](Appendix.Oracle.PatchComposition.11.2.0.4.md#Appendix.Oracle.PatchComposition.11.2.0.4.v1) | 