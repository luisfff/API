# Howdy API Documentation
> These pages contains samples and specifications on how to integrate Howdy with HR-systems in order to maintain employee data

## Endpoints

- Test endpoint: https://wlb-uat-ne1-api.azurewebsites.net/
- Production endpoint: https://api-ne1.worklifebarometer.com/

## Security
All calls to the API must be authenticated by presenting a valid JWT ([JSON Web Token](https://jwt.io/)).
The Token can be set in one of two ways:
- As a HTTP Header: Add is as `Authorization: Bearer <API_TOKEN_HERE>` to each request (as shown below)
- As a Query String parameter: Pass it as `?access_token=<API_TOKEN_HERE>` to each request

The token itself will be issued by a Portal Administrator

## GET /v1.0/Company/{companyId}/Employee
This call returns all Employees stored in Howdy.
### Request
```http
GET /v1.0/company/{companyId}/employee HTTP/1.1
Host: <API_ENDPOINT>
Authorization: Bearer <API_TOKEN_HERE>
Cache-Control: no-cache
```

## PUT /v1.0/Company/{companyId}/Employee
This call makes a complete set based change of all employees in the system.

>If employee A, B and C exists in Howdy and employee B, C and D are sent via this call then:
>- A will be removed
>- B and C will be updated with the values provided
>- D will be added

#### Data model

| Field             | Description                                                                  |
| ----------------- | ---------------------------------------------------------------------------- |
| EmployeeID*       | Your internal primary key. Unique. String. Max length: 50                    |
| InvitationDate    | Date and time when an invitation should be sent out. Only for new employees. If obmitted then then invitation will be sent immediately or at 8 o'clock if outside business hours. String. yyyy-MM-ddTHH:mm:ssZ        |
| Firstname*        | Firstname of the employee. String. Max length: 150. Must match Regex: ``^[\p{L} \-\'\´\`\.0-9]+$`` |
| Lastname*         | Lastname of the employee. String. Max length: 150. Must match Regex: ``^[\p{L} \-\'\´\`\.0-9]+$`` |
| Phonenumber       | Cell phone. Unique. String. Must match regex: `^\+[0-9]{6,20}$` E.g. +4523232323                |
| Email*            | E-mail address. Unique.                   |
| Gender            | Gender. Integer. Values: 0 = Male, 1 = Female, 9 = Not known                   |
| EmploymentStatus* | Employment Status. Integer. 0 = Active, 1 = "on leave" e.g. maternity leave etc.  |
| JobTitle         | String. Max length: 50. Must match regex: `^[\p{L} \\\/\-\'\.\,0-9]+$`. E.g. "Sales Manager" or "CEO"     |
| Department       | Reporting specific data. String. Max length: 50. Must match regex: `^[\p{L} \%\#\$\(\)\&\/\,\.\\0-9\-]+$` |
| Role             | Reporting specific data. String. Max length: 50. Must match regex: `^[\p{L} \%\#\$\(\)\&\/\,\.\\0-9\-]+$` |
| ImmediateManager | Reporting specific data. String. Max length: 50. Must match regex: `^[\p{L} \%\#\$\(\)\&\/\,\.\\0-9\-]+$` |
| Dim1             | Reporting specific data. String. Max length: 50. Must match regex: `^[\p{L} \%\#\$\(\)\&\/\,\.\\0-9\-]+$` |
| Dim2             | Reporting specific data. String. Max length: 50. Must match regex: `^[\p{L} \%\#\$\(\)\&\/\,\.\\0-9\-]+$` |
| Dim3             | Reporting specific data. String. Max length: 50. Must match regex: `^[\p{L} \%\#\$\(\)\&\/\,\.\\0-9\-]+$` |
| Dim4             | Reporting specific data. String. Max length: 50. Must match regex: `^[\p{L} \%\#\$\(\)\&\/\,\.\\0-9\-]+$` |
| Dim5             | Reporting specific data. String. Max length: 50. Must match regex: `^[\p{L} \%\#\$\(\)\&\/\,\.\\0-9\-]+$` |
| Dim6             | Reporting specific data. String. Max length: 50. Must match regex: `^[\p{L} \%\#\$\(\)\&\/\,\.\\0-9\-]+$` |
| Dim7             | Reporting specific data. String. Max length: 50. Must match regex: `^[\p{L} \%\#\$\(\)\&\/\,\.\\0-9\-]+$` |
| Dim8             | Reporting specific data. String. Max length: 50. Must match regex: `^[\p{L} \%\#\$\(\)\&\/\,\.\\0-9\-]+$` |
| Dim9             | Reporting specific data. String. Max length: 50. Must match regex: `^[\p{L} \%\#\$\(\)\&\/\,\.\\0-9\-]+$` |
| Dim10            | Reporting specific data. String. Max length: 50. Must match regex: `^[\p{L} \%\#\$\(\)\&\/\,\.\\0-9\-]+$` |


### Request
The request consists of an array of Employees based on the model described above.
```http
PUT /v1.0/company/{companyId}/employee HTTP/1.1
Host: <API_ENDPOINT>
Authorization: Bearer <API_TOKEN_HERE>
Content-Type: application/json
Cache-Control: no-cache
 
[
  {
    "Phonenumber": "+00008462",
    "Email": "8462@dev.test",
    "Birthday": null,
    "Gender": 9,
    ...
  },
  ...
]
```

### Response

*200 OK*
```json
{
  "ApiOperationId": "string"
}
```

*400 Bad Data*
```json
{
  "ApiOperationId": "string",
 "ValidationErrors": ["string"]
}
``` 
The `ApiOperationId` can be used for further diagnostics so please log this if any errors are returned from the service
 
 
 
 ## Validate /v1.0/Company/{companyId}/Employee/validate
 
This call validates the Employee data that will be used in the PUT call.It checks if the inserted Emails or Phone number that are already in the database, or if there are duplicated emails/phone numbers in the input data.


### Request

The request consists of an array of Employees each of those containing an array with the corresponding dimensions based on the model described above.


'''PUT /v1.0/Company/{companyId}/Employee/validate HTTP/1.1
Host: <API_ENDPOINT>
Authorization: Bearer <API_TOKEN_HERE>
Content-Type: application/json;charset=UTF-8
Cache-Control: no-cache

Request Payload:
[{"Dimensions":{},"Firstname":"a1","Lastname":"a2","Email":"lbad@we.cvb","Phonenumber":"+4531310311",...},...]``` 


### Response- Update

*200 OK*

```json
{"IsValid":true,"Summary":{"Update":1},"Items":
[{"Action":"Update","UpdateId":456874,"ValidationMessages":null}]}``` 

The information regarding the email is already in the database, and the inserted phone number is not associated with any other employee, thus the employee dimensions can be update.


###Response- Duplicated Email or duplicated phone number in the input data

*200 OK*

```json
{"IsValid":false,"Summary":{"DuplicateEmail":2},"Items":[{"Action":"DuplicateEmail","UpdateId":null,"ValidationMessages":null},{"Action":"DuplicateEmail","UpdateId":null,"ValidationMessages":null}]}```


There is a duplicated email/ phone number in the input data. The data is not valid for insertion/ update.


###Response– duplicated phone number

*200 OK*

```json
{"IsValid":false,"Summary":{"DuplicatePhoneInsideOfCompany":1},"Items":[{"Action":"DuplicatePhoneInsideOfCompany","UpdateId":null,"ValidationMessages":null}]}``` 

The phone number already exists in the database, in association with another employee.
The data is not valid for insertion/ update.


###Response- duplicated Email outside of company

*200 OK*

```json
{"IsValid":false,"Summary":{"DuplicateEmailOutsideOfCompany":1},"Items":[{"Action":"DuplicateEmailOutsideOfCompany","UpdateId":null,"ValidationMessages":null}]}``` 

The email that is being inserted is already associated with another employee outside of the company.
The data is not valid for insertion/ update.


###Response- Duplicated phone number outside of company

*200 OK*

```json
{"IsValid":false,"Summary":{"DuplicatePhoneOutsideOfCompany":1},"Items":[{"Action":"DuplicatePhoneOutsideOfCompany","UpdateId":null,"ValidationMessages":null}]}
The phone number that is being inserted already exists outside of the company.```

The phone number that is being inserted is already associated with another employee outside of the company.
The data is not valid for insertion/ update.


###Response- Insert

*200 OK*

```json
{"IsValid":true,"Summary":{"Insert":1},"Items":[{"Action":"Insert","UpdateId":null,"ValidationMessages":null}]}
Both the email and phone numbers are new to the database.```

The email and phone numbers are new to the database.



