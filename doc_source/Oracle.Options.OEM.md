# Oracle Enterprise Manager<a name="Oracle.Options.OEM"></a>

Amazon RDS supports Oracle Enterprise Manager \(OEM\)\. OEM is the Oracle product line for integrated management of enterprise information technology\. 

Amazon RDS supports OEM through the following options\.


****  

| Option | Option ID | Supported OEM releases | Supported Oracle Database releases | 
| --- | --- | --- | --- | 
|  [OEM Database Express](Appendix.Oracle.Options.OEM_DBControl.md)  |  `OEM`  |  OEM Database Express 12c  |  Oracle Database 19c \(non\-CDB only\) and Oracle Database 12c  | 
|  [OEM Management Agent](Oracle.Options.OEMAgent.md)  |  `OEM_AGENT`  |  OEM Cloud Control for 13c OEM Cloud Control for 12c   |  Oracle Database 19c \(non\-CDB only\) and Oracle Database 12c  | 

**Note**  
You can use OEM Database or OEM Management Agent, but not both\. 

**Note**  
These options aren't supported for the single\-tenant architecture\. 