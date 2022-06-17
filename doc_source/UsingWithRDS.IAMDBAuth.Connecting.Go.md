# Connecting to your DB instance using IAM authentication and the AWS SDK for Go<a name="UsingWithRDS.IAMDBAuth.Connecting.Go"></a>

You can connect to an RDS for MariaDB, MySQL, or PostgreSQL DB instance with the AWS SDK for Go as described following\.

**Prerequisites**  
The following are prerequisites for connecting to your DB instance using IAM authentication:
+ [Enabling and disabling IAM database authentication](UsingWithRDS.IAMDBAuth.Enabling.md)
+ [Creating and using an IAM policy for IAM database access](UsingWithRDS.IAMDBAuth.IAMPolicy.md)
+ [Creating a database account using IAM authentication](UsingWithRDS.IAMDBAuth.DBAccounts.md)

**Examples**  
To run these code examples, you need the [AWS SDK for Go](http://aws.amazon.com/sdk-for-go/), found on the AWS site\.

Modify the values of the following variables as needed:
+ `dbName` – The database that you want to access
+ `dbUser` – The database account that you want to access
+ `dbHost` – The endpoint of the DB instance that you want to access
+ `dbPort` – The port number used for connecting to your DB instance
+ `region` – The AWS Region where the DB instance is running

In addition, make sure the imported libraries in the sample code exist on your system\.

**Important**  
The examples in this section use the following code to provide credentials that access a database from a local environment:  
`creds := credentials.NewEnvCredentials()`  
If you are accessing a database from an AWS service, such as Amazon EC2 or Amazon ECS, you can replace the code with the following code:  
`sess := session.Must(session.NewSession())`  
`creds := sess.Config.Credentials`  
If you make this change, make sure you add the following import:  
`"github.com/aws/aws-sdk-go/aws/session"`

**Topics**
+ [Connecting using IAM authentication and the AWS SDK for Go V2](#UsingWithRDS.IAMDBAuth.Connecting.GoV2)
+ [Connecting using IAM authentication and the AWS SDK for Go V1\.](#UsingWithRDS.IAMDBAuth.Connecting.GoV1)

## Connecting using IAM authentication and the AWS SDK for Go V2<a name="UsingWithRDS.IAMDBAuth.Connecting.GoV2"></a>

You can connect to a DB instance using IAM authentication and the AWS SDK for Go V2\.

The following code examples show how to generate an authentication token, and then use it to connect to a DB instance\. 

This code connects to a MariaDB or MySQL DB instance\.

```
package main
                
import (
     "context"
     "database/sql"
     "fmt"

     "github.com/aws/aws-sdk-go-v2/config"
     "github.com/aws/aws-sdk-go-v2/feature/rds/auth"
     _ "github.com/go-sql-driver/mysql"
)

func main() {

     var dbName string = "DatabaseName"
     var dbUser string = "DatabaseUser"
     var dbHost string = "mysqldb.123456789012.us-east-1.rds.amazonaws.com"
     var dbPort int = 3306
     var dbEndpoint string = fmt.Sprintf("%s:%d", dbHost, dbPort)
     var region string = "us-east-1"

    cfg, err := config.LoadDefaultConfig(context.TODO())
    if err != nil {
    	panic("configuration error: " + err.Error())
    }

    authenticationToken, err := auth.BuildAuthToken(
    	context.TODO(), dbEndpoint, region, dbUser, cfg.Credentials)
    if err != nil {
	    panic("failed to create authentication token: " + err.Error())
    }

    dsn := fmt.Sprintf("%s:%s@tcp(%s)/%s?tls=true&allowCleartextPasswords=true",
        dbUser, authenticationToken, dbEndpoint, dbName,
    )

    db, err := sql.Open("mysql", dsn)
    if err != nil {
        panic(err)
    }

    err = db.Ping()
    if err != nil {
        panic(err)
    }
}
```

This code connects to a PostgreSQL DB instance\.

```
package main

import (
     "context"
     "database/sql"
     "fmt"

     "github.com/aws/aws-sdk-go-v2/config"
     "github.com/aws/aws-sdk-go-v2/feature/rds/auth"
     _ "github.com/lib/pq"
)

func main() {

     var dbName string = "DatabaseName"
     var dbUser string = "DatabaseUser"
     var dbHost string = "postgresmydb.123456789012.us-east-1.rds.amazonaws.com"
     var dbPort int = 5432
     var dbEndpoint string = fmt.Sprintf("%s:%d", dbHost, dbPort)
     var region string = "us-east-1"

    cfg, err := config.LoadDefaultConfig(context.TODO())
    if err != nil {
    	panic("configuration error: " + err.Error())
    }

    authenticationToken, err := auth.BuildAuthToken(
    	context.TODO(), dbEndpoint, region, dbUser, cfg.Credentials)
    if err != nil {
	    panic("failed to create authentication token: " + err.Error())
    }

    dsn := fmt.Sprintf("host=%s port=%d user=%s password=%s dbname=%s",
        dbHost, dbPort, dbUser, authenticationToken, dbName,
    )

    db, err := sql.Open("postgres", dsn)
    if err != nil {
        panic(err)
    }

    err = db.Ping()
    if err != nil {
        panic(err)
    }
}
```

## Connecting using IAM authentication and the AWS SDK for Go V1\.<a name="UsingWithRDS.IAMDBAuth.Connecting.GoV1"></a>

You can connect to a DB instance using IAM authentication and the AWS SDK for Go V1

The following code examples show how to generate an authentication token, and then use it to connect to a DB instance\. 

This code connects to a MariaDB or MySQL DB instance\.

```
package main
         
import (
    "database/sql"
    "fmt"
    "log"

    "github.com/aws/aws-sdk-go/aws/credentials"
    "github.com/aws/aws-sdk-go/service/rds/rdsutils"
    _ "github.com/go-sql-driver/mysql"
)

func main() {
    dbName := "app"
    dbUser := "jane_doe"
    dbHost := "mysqldb.123456789012.us-east-1.rds.amazonaws.com"
    dbPort := 3306
    dbEndpoint := fmt.Sprintf("%s:%d", dbHost, dbPort)
    region := "us-east-1"

    creds := credentials.NewEnvCredentials()
    authToken, err := rdsutils.BuildAuthToken(dbEndpoint, region, dbUser, creds)
    if err != nil {
        panic(err)
    }

    dsn := fmt.Sprintf("%s:%s@tcp(%s)/%s?tls=true&allowCleartextPasswords=true",
        dbUser, authToken, dbEndpoint, dbName,
    )

    db, err := sql.Open("mysql", dsn)
    if err != nil {
        panic(err)
    }

    err = db.Ping()
    if err != nil {
        panic(err)
    }
}
```

This code connects to a PostgreSQL DB instance\.

```
package main

import (
	"database/sql"
	"fmt"

	"github.com/aws/aws-sdk-go/aws/credentials"
	"github.com/aws/aws-sdk-go/service/rds/rdsutils"
	_ "github.com/lib/pq"
)

func main() {
    dbName := "app"
    dbUser := "jane_doe"
    dbHost := "postgresmydb.123456789012.us-east-1.rds.amazonaws.com"
    dbPort := 5432
    dbEndpoint := fmt.Sprintf("%s:%d", dbHost, dbPort)
    region := "us-east-1"

    creds := credentials.NewEnvCredentials()
    authToken, err := rdsutils.BuildAuthToken(dbEndpoint, region, dbUser, creds)
    if err != nil {
        panic(err)
    }

    dsn := fmt.Sprintf("host=%s port=%d user=%s password=%s dbname=%s",
        dbHost, dbPort, dbUser, authToken, dbName,
    )

    db, err := sql.Open("postgres", dsn)
    if err != nil {
        panic(err)
    }

    err = db.Ping()
    if err != nil {
        panic(err)
    }
}
```