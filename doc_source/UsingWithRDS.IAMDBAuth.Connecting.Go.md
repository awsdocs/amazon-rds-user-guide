# Connecting to your DB instance using IAM authentication and the AWS SDK for Go<a name="UsingWithRDS.IAMDBAuth.Connecting.Go"></a>

You can connect to an RDS for MySQL or RDS for PostgreSQL DB instance with the AWS SDK for Go as described following\.

The following are prerequisites for connecting to your DB instance using IAM authentication:
+ [Enabling and disabling IAM database authentication](UsingWithRDS.IAMDBAuth.Enabling.md)
+ [Creating and using an IAM policy for IAM database access](UsingWithRDS.IAMDBAuth.IAMPolicy.md)
+ [Creating a database account using IAM authentication](UsingWithRDS.IAMDBAuth.DBAccounts.md)

To run these code examples, you need the [AWS SDK for Go](http://aws.amazon.com/sdk-for-go/), found on the AWS site\.

Modify the values of the following variables as needed:
+ `dbName` – The database that you want to access
+ `dbUser` – The database account that you want to access
+ `dbHost` – The endpoint of the DB instance that you want to access
+ `dbPort` – The port number used for connecting to your DB instance
+ `region` – The AWS Region where the DB instance is running

In addition, make sure the imported libraries in the sample code exist on your system\.

**Topics**
+ [Generating an IAM authentication token](#UsingWithRDS.IAMDBAuth.Connecting.Go.AuthToken)
+ [Connecting to a DB instance](#UsingWithRDS.IAMDBAuth.Connecting.Python.AuthToken.Connect)

## Generating an IAM authentication token<a name="UsingWithRDS.IAMDBAuth.Connecting.Go.AuthToken"></a>

You can use the [ `rdsutils`](https://docs.aws.amazon.com/sdk-for-go/api/service/rds/rdsutils/) package to generate tokens used to connect to a DB instance\. Call the [https://docs.aws.amazon.com/sdk-for-go/api/service/rds/rdsutils/#BuildAuthToken](https://docs.aws.amazon.com/sdk-for-go/api/service/rds/rdsutils/#BuildAuthToken) function to generate a token\. Provide the DB instance endpoint, AWS region, username, and IAM credentials to generate the token for connecting to a DB instance with IAM credentials\.

The following example shows how to use `BuildAuthToken` to create an authentication token for connecting to a MySQL DB instance\.

```
package main

import (
    "database/sql"
    "fmt"
    "log"

    "github.com/aws/aws-sdk-go/aws/credentials"
    "github.com/aws/aws-sdk-go/service/rds/rdsutils"
)

func main() {
    dbName := "app"
    dbUser := "jane_doe"
    dbHost := "mydb.123456789012.us-east-1.rds.amazonaws.com"
    dbPort := 3306
    dbEndpoint := fmt.Sprintf("%s:%d", dbHost, dbPort)
    region := "us-east-1"

    creds := credentials.NewEnvCredentials()
    authToken, err := rdsutils.BuildAuthToken(dbEndpoint, region, dbUser, creds)
    if err != nil {
        log.Fatalf("failed to build auth token %v", err)
    }
}
```

The following example shows how to use `BuildAuthToken` to create an authentication token for connecting to a PostgreSQL DB instance\.

```
package main

import (
    "database/sql"
    "fmt"
    "log"

    "github.com/aws/aws-sdk-go/aws/credentials"
    "github.com/aws/aws-sdk-go/service/rds/rdsutils"
)

func main() {
    dbName := "app"
    dbUser := "jane_doe"
    dbHost := "mydb.123456789012.us-east-1.rds.amazonaws.com"
    dbPort := 5432
    dbEndpoint := fmt.Sprintf("%s:%d", dbHost, dbPort)
    region := "us-east-1"

    creds := credentials.NewEnvCredentials()
    authToken, err := rdsutils.BuildAuthToken(dbEndpoint, region, dbUser, creds)
    if err != nil {
        log.Fatalf("failed to build auth token %v", err)
    }
}
```

## Connecting to a DB instance<a name="UsingWithRDS.IAMDBAuth.Connecting.Python.AuthToken.Connect"></a>

The following code example shows how to generate an authentication token, and then use it to connect to a DB instance\. 

This code connects to a MySQL DB instance\.

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
    dbHost := "mydb.123456789012.us-east-1.rds.amazonaws.com"
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
    dbHost := "mydb.123456789012.us-east-1.rds.amazonaws.com"
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