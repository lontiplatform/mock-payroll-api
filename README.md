# Mock Payroll API
A simple payroll api with CRUD operations

### Prerequisites

  - Apache Maven 3+
  - [Martini Desktop](https://www.torocloud.com/martini/download)

### Building the Martini Package

```
$ mvn clean package
```
This will create a ZIP file named `mock-payroll-api-bin.zip` containing all the files (services, configurations, etc.) needed under the `target` folder. This ZIP file is what we call a [Martini Package](https://docs.torocloud.com/martini/latest/developing/package/) which then you can import in Martini Desktop to get started. You can learn more how to import a Martini Package by visiting our [documentation](https://docs.torocloud.com/martini/latest/developing/package/importing/)

### Getting the Martini Package from TORO Marketplace

You can also get this package via TORO Marketplace. See our documentation on [How to import a Martini Package from Marketplace](https://docs.torocloud.com/martini/latest/developing/package/importing/#from-the-marketplace).

### Usage
This package exposes operations for a simple payroll REST API that allows you to do CRUD operations. You can find the [Gloop REST API](https://docs.torocloud.com/martini/latest/developing/gloop/api/rest/) file at `io/toro/mock/payroll/api/Payroll` under the `code` folder after importing the package to your Martini Desktop application.

### Setup
This Martini Package automatically sets up the required resources for you. During the package startup, Martini will execute the configured _Startup Service_ that initializes the database to be used for storing the record that will be creating for using this mock api.

This package uses HSQL as the default datastore but you can change this to MySQL by updating the [package properties](https://docs.torocloud.com/martini/latest/developing/package/properties/) located at [conf](https://docs.torocloud.com/martini/latest/developing/package/directory/) folder. Below is what the contents of the properties file look like

```
# Flag used for testing
debug.enabled=true

# The database type to be used
database.type=hsql

# HSQL database configuration to be used for testing environment
hsql.driver=org.hsqldb.jdbc.JDBCDriver
hsql.connection.url=jdbc:hsqldb:file:${toroesb.home}data/hsql/mock_payroll_api.db;hsqldb.tx=MVCC;sql.syntax_mys=true
hsql.username=sa
hsql.password=

# MySQL database configuration to be used in production environment
mysql.driver=com.mysql.cj.jdbc.Driver
mysql.connection.url=jdbc:mysql://<host>/<database-to-use>?allowMultiQueries=true
mysql.username=root
mysql.password=
```
* `debug.enabled` when set to `true` initializes the database with dummy data when the package starts. The initialized data are also removed when the package stops or is unloaded. Set the value of this property to `false` if you want to keep your data when doing a restart. You can also set this property to `true` when the package starts and then set to `false` afterwards so that you'll have the data initialized on startup, and keep the data when the package or the Martini instance do a restart
* `database.type` sets the database provider the Martini package will use. If you will use the default hsql config, you don't need to change anything in the hsql configuration. **Note**: If you will use a different hsql database, make sure that you add `sql.syntax_mys=true` in the connection properties. This ensures that the SQL query from the SQL Services in this package will be compatible with hsql.

### Sending Authenticated Requests

You can use your ECC account to send authenticated request to the API. Your ECC credentials must be sent in the `Authorization` header in the HTTP request

#### To authenticate a request with basic authentication

1. Combine your email and password with a colon (`:`). e.g. `jdoe@mailinator.com:pa$$w0rd`
2. Encode the resulting string in Base64
3. Include an Authorization header in the HTTP request containing the base64-encoded string. Example: ```
Authorization: Basic amRvZUBtYWlsaW5hdG9yLmNvbTpwYSQkdzByZA==```

### Operations

The base url is `<host>/api/mock-payroll-api` where `host` is the location where the Martini instance is deployed. By default, it's `localhost:8080`.

#### Users

`GET /users`

Returns a list of created users

**Sample Request**

**curl**
```
curl -X GET \
  http://localhost:8080/api/mock-payroll-api/users \
  -H 'Authorization: Basic <base64-encoded-credentials-string>' \
  -H 'accept: application/json'
```

**Sample Response**

If the request is successful, it will return an HTTP status code `200` with the response payload below:
```
{
    "result": "SUCCESS",
    "message": "Successfully fetched all users.",
    "users": [
        {
            "name": "Kim Pinnere",
            "userId": "0bb1f92c-7f1c-4016-b001-2684ba6c4900",
            "hourlyRate": 23.0
        },
        {
            "name": "Garner Eales",
            "userId": "75f1c0ed-e3c5-4edb-bf64-c578319a3bd0",
            "hourlyRate": 21.0
        },
        {
            "name": "Brit Lorent",
            "userId": "bd38b61a-bc8e-436d-b062-4785e685da42",
            "hourlyRate": 28.0
        }
    ]
}
```

`POST /user`

Creates a new user

**Sample Request**

**curl**
```
curl -X POST \
  http://localhost:8080/api/mock-payroll-api/users \
  -H 'Accept: application/json' \
  -H 'Authorization: Basic <base64-encoded-credentials-string>' \
  -H 'Content-Type: application/json' \
  -d '{
    "name": "John Doe",
    "hourlyRate": "25.50"
}'
```

**Sample Response**

If the request is successful, it will return an HTTP status code `201` with the response payload similar below.
```
{
    "result": "SUCCESS",
    "message": "Successfully created user with id: 469514e0-6161-4293-8c9c-c6549385ad8c",
    "user": {
        "name": "John Doe",
        "userId": "469514e0-6161-4293-8c9c-c6549385ad8c",
        "hourlyRate": 25.5
    }
}
```

`GET /users/<userId>`

Returns a singler user record that matches the given `userId`

**Sample Request**

**curl**
```
curl -X GET \
  http://localhost:8080/api/mock-payroll-api/users/469514e0-6161-4293-8c9c-c6549385ad8c \
  -H 'Accept: application/json'
```

**Sample Response**

If the request is successful, it will return an HTTP status code `200` with the response payload below.
```
{
    "name": "John Doe",
    "userId": "469514e0-6161-4293-8c9c-c6549385ad8c",
    "hourlyRate": 25.5
}
```

`PATCH /user/<userId>`

Updates a user that matches the given `userId`

**curl**
```
curl -X PUT \
  http://localhost:8080/api/mock-payroll-api/users/cdd1ab2c-8e2a-4e73-bd12-aaedd590ef35 \
  -H 'Accept: application/json' \
  -H 'Authorization: Basic <base64-encoded-credentials-string>' \
  -H 'Content-Type: application/json' \
  -d '{
    "hourlyRate": 35.55
}'
```

**Sample Response** 

If the request is successful, it will return an HTTP status code of `200` with the response payload below.
```
{
    "result": "SUCCESS",
    "message": "Successfully updated user with id: 469514e0-6161-4293-8c9c-c6549385ad8c",
    "user": {
        "name": "John Doe",
        "userId": "469514e0-6161-4293-8c9c-c6549385ad8c",
        "hourlyRate": 35.5
    }
}
```

`DELETE /users/<userId>`

Deletes a user record that matches the `userId`

**Sample Request**

**curl**
```
curl -X DELETE \
  http://localhost:8080/api/mock-payroll-api/users/cdd1ab2c-8e2a-4e73-bd12-aaedd590ef35
```

**Sample Response**

If the request is successful, it will return an HTTP status code of `200` with the response payload below:
```
{
    "result": "SUCCESS",
    "message": "Successfully deleted user with id: cdd1ab2c-8e2a-4e73-bd12-aaedd590ef35"
}
```

#### Attendance

`GET /attendance`

Returns a list of all the attendance in record. You can also get all the list of attendance record by supplying query parameters `startDate` and `endDate`

**Sample Request**

**curl**
```
curl -X GET \
  http://localhost:8080/api/mock-payroll-api/attendance?startDate=2020-01-01T08:00:00+0800 \
  -H 'Authorization: Basic <base64-encoded-credentials-string>' \
  -H 'accept: application/json'
```

**Sample Response**

If the request is successful, it will return an HTTP status code of 200 with a response payload similar below:
```
{
    "result": "SUCCESS",
    "message": "Listing all attendance record from Thu Jan 01 08:00:00 PST 2020 to Wed Nov 25 13:25:42 PST 2020",
    "attendanceRecords": [
        {
            "date": "2019-10-01T08:00:00+0800",
            "hoursWorked": 8.0,
            "id": "00bd2330-b8d8-4b65-b9a0-bd7ce137e3ea",
            "userId": "0bb1f92c-7f1c-4016-b001-2684ba6c4900"
        },
        {
            "date": "2019-10-03T08:00:00+0800",
            "hoursWorked": 8.0,
            "id": "11f9e2bd-3173-4f9f-8a08-d6569f8fef67",
            "userId": "0bb1f92c-7f1c-4016-b001-2684ba6c4900"
        },
        {
            "date": "2019-10-03T08:00:00+0800",
            "hoursWorked": 8.0,
            "id": "eefb89b0-7ce3-4fe0-be65-c182f8960a19",
            "userId": "75f1c0ed-e3c5-4edb-bf64-c578319a3bd0"
        },
        {
            "date": "2019-10-05T08:00:00+0800",
            "hoursWorked": 8.0,
            "id": "f98d505b-aeed-40a1-a009-fa9c3fb69455",
            "userId": "bd38b61a-bc8e-436d-b062-4785e685da42"
        }
    ]
}
```

`POST /users/<userId>/attendance`

Records the attendance of the user that matches the provided `userId`

**Sample Request**

**curl**
```
curl -X POST \
  http://localhost:8080/api/mock-payroll-api/users/cdd1ab2c-8e2a-4e73-bd12-aaedd590ef35/attendance \
  -H 'Authorization: Basic <base64-encoded-credentials-string>' \
  -H 'Accept: application/json' \
  -H 'Content-Type: application/json' \
  -d '{
    "date": "2020-03-06T16:17:40+0800",
    "hoursWorked": 8.5
}'
```

**Sample Response**

If the request is successful, it will return an HTTP status code of 200 with the response payload below:
```
{
    "result": "SUCCESS",
    "message": "Successfully recorded attendance with id ac755de9-0bb6-4262-8926-7dcfddabf877 for userId: cdd1ab2c-8e2a-4e73-bd12-aaedd590ef35",
    "attendanceRecord": {
        "date": "2020-11-26T00:17:40+0800",
        "hoursWorked": 8.5,
        "userId": "469514e0-6161-4293-8c9c-c6549385ad8c",
        "id": "ac755de9-0bb6-4262-8926-7dcfddabf877"
    }
}
```


#### Deductions

`GET /deductions`

Returns a list of all deductions

**Sample Request**

**curl**
```
curl -X GET \
  http://localhost:8080/api/mock-payroll-api/deductions \
  -H 'Authorization: Basic <base64-encoded-credentials-string>' \
  -H 'accept: application/json'
```

**Sample Response**

If the request is successful, it will return an HTTP status code `200` with the response payload below:
```
{
    "result": "SUCCESS",
    "message": "Deductions fetch successfully.",
    "deductions": [
        {
            "name": "Child Support",
            "description": "Child support.",
            "type": "POST-TAX",
            "method": "FIXED",
            "amount": 50.0,
            "applicableTo": "bd38b61a-bc8e-436d-b062-4785e685da42",
            "enabled": true,
            "id": "148dd9cc-235f-4339-9589-aa0465f12eb0"
        },
        {
            "name": "Health Insurance",
            "description": "Health insurance.",
            "type": "PRE-TAX",
            "method": "FIXED",
            "amount": 20.0,
            "applicableTo": "ALL",
            "enabled": true,
            "id": "a7331863-e000-4a63-9a4c-a91afa22a8ec"
        },
        {
            "name": "Income Tax",
            "description": "Income tax.",
            "type": "TAX",
            "method": "PERCENTAGE",
            "amount": 20.0,
            "applicableTo": "ALL",
            "enabled": true,
            "id": "b64b8e5e-5c2c-4d6a-a2c4-e53b08f1589a"
        }
    ]
}
```

`POST /deductions`

Creates a new deduction record

**Sample Request with custom field**

**curl**
```
curl -X POST \
  http://localhost:8080/api/mock-payrollr-api/deductions \
  -H 'Accept: application/json' \
  -H 'Authorization: Basic <base64-encoded-credentials-string>' \
  -H 'Content-Type: application/json' \
  -d '{
    "name": "Health Insurance",
    "description": "Allocation for health insurance policy",
    "type": "PRE-TAX",
    "method": "FIXED",
    "amount": "100.00",
    "applicableTo": "ALL",
    "enabled": "true"
}'
```

* `applicableTo` can also accept user ids in which the deduction will only apply to.

**Sample Response**

If the request is successful, it will return an HTTP status code `201` with the response payload below:
```
{
    "result": "SUCCESS",
    "message": "Successfully created deduction with id: a7331863-e000-4a63-9a4c-a91afa22a8ec.",
    "deduction": {
        "name": "Health Insurance",
        "description": "Allocation for health insurance policy.",
        "type": "PRE-TAX",
        "method": "FIXED",
        "amount": 100.0,
        "applicableTo": "ALL",
        "enabled": true,
        "id": "a7331863-e000-4a63-9a4c-a91afa22a8ec"
    }
}
```

`GET /deductions/<deductionId>`

Returns a single deduction record that matches the given `deductionId`

**Sample Request**

**curl**
```
curl -X GET \
  http://localhost:8080/api/mock-payroll-api/deductions/2e9df7ef-b47c-4fbf-bc77-13abd5113bae \
  -H 'Authorization: Basic <base64-encoded-credentials-string>' \
  -H 'Accept: application/json'
```

**Sample Response**

If the request is sucessful, it will return an HTTP response `200` with the response payload below:
```
{
    "result": "SUCCESS",
    "deduction": {
        "name": "Income Tax",
        "description": "Income tax.",
        "type": "TAX",
        "method": "PERCENTAGE",
        "amount": 20.0,
        "applicableTo": "ALL",
        "enabled": true,
        "id": "2e9df7ef-b47c-4fbf-bc77-13abd5113bae"
    }
}
```

`PUT /deductions/<deductionId>`

Updates details about a deduction.

**Sample Request**

**curl**
```
curl -X PATCH \
  http://localhost:8080/api/mock-payroll-api/deductions/2e9df7ef-b47c-4fbf-bc77-13abd5113bae \
  -H 'Accept: application/json' \
  -H 'Authorization: Basic <base64-encoded-credentials-string>' \
  -H 'Content-Type: application/json' \
  -d '{
    "description": "Updated description for health insurance allocation.",
    "enabled": "false"
}'
```

**Sample Response**

If the request is successful, it will return an HTTP status `200` with the response payload below:
```
{
    "result": "SUCCESS",
    "message": "Successfully updated deduction with id: 2e9df7ef-b47c-4fbf-bc77-13abd5113bae.",
    "deduction": {
        "name": "Child Support",
        "description": "Child support.",
        "type": "POST-TAX",
        "method": "FIXED",
        "amount": 50.0,
        "applicableTo": "bd38b61a-bc8e-436d-b062-4785e685da42",
        "enabled": true,
        "id": "148dd9cc-235f-4339-9589-aa0465f12eb0"
    }
}
```

`DELETE /deductions/<deductionId>`

Deletes a deduction record that matches the given `deductionId`

**Sample Request**

**curl**
```
curl -X DELETE \
  http://localhost:8080/api/mock-payroll-api/deductions/2e9df7ef-b47c-4fbf-bc77-13abd5113bae \
  -H 'Authorization: Basic <base64-encoded-credentials-string>' \
  -H 'Accept: application/json'
```

**Sample Response**

If the request is successful, it will return an HTTP response `200` with the response payload below:
```
{
    "result": "SUCCESS",
    "message": "Successfully deleted deduction with id: 2e9df7ef-b47c-4fbf-bc77-13abd5113bae"
}
```

#### Generating the payroll summary

The following endpoint url can be used for generating a payroll summary for the given user

`POST /generate-payroll`

**Sample Request**

**curl**
```
curl -X POST \
  http://localhost:8080/api/mock-payroll-api/generate-payroll/2e9df7ef-b47c-4fbf-bc77-13abd5113bae \
  -H 'Accept: application/json' \
  -H 'Authorization: Basic <base64-encoded-credentials-string>' \
  -H 'Content-Type: application' \
  -d '{
    "userId": "0bb1f92c-7f1c-4016-b001-2684ba6c4900",
    "period": {
       "startDate": "2019-10-01T00:00:00+0800",
       "endDate": "2020-10-15T00:00:00+0800"
    }
}'
```

**Sample Response**

If the request is successful, it will return an HTTP response `200` with the response payload below:
```
{
    "payrollSummary": {
        "user": {
            "name": "Kim Pinnere",
            "hourlyRate": 23.0,
            "userId": "0bb1f92c-7f1c-4016-b001-2684ba6c4900"
        },
        "grossPay": 119025.0,
        "netPay": 119005.0,
        "attendanceRecords": [
            {
                "date": "2019-10-01T08:00:00+0800",
                "hoursWorked": 8.0,
                "id": "00bd2330-b8d8-4b65-b9a0-bd7ce137e3ea"
            },
            {
                "date": "2019-10-03T08:00:00+0800",
                "hoursWorked": 8.0,
                "id": "11f9e2bd-3173-4f9f-8a08-d6569f8fef67"
            },
            {
                "date": "2019-10-02T08:00:00+0800",
                "hoursWorked": 8.0,
                "id": "1f888729-c92e-4d0e-ad66-a4fb8b9c7cf4"
            },
            {
                "date": "2019-10-04T08:00:00+0800",
                "hoursWorked": 11.75,
                "id": "8efd3c4d-8d73-41e7-9981-05b297c92a29"
            },
            {
                "date": "2019-10-05T08:00:00+0800",
                "hoursWorked": 8.0,
                "id": "ec7c78c2-9956-4130-a0c3-7be37ae323bd"
            }
        ],
        "deductions": [
            {
                "id": "a7331863-e000-4a63-9a4c-a91afa22a8ec",
                "name": "Health Insurance",
                "type": "PRE-TAX",
                "method": "FIXED",
                "amount": 20.0
            }
        ]
    }
}
```