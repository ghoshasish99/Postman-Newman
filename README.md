# API testing with Postman and Newman
 
[![Build Status](https://dev.azure.com/AutomationsTools/Execution/_apis/build/status/Postman-Newman?branchName=main)](https://dev.azure.com/AutomationsTools/Execution/_build/latest?definitionId=8&branchName=main)

* *Postman* is a lightweight API testing solution. Its predominantly used for Testing but can be used for API developments too. 
* *Newman* is the commandline collection runner for Postman which facilitates execution via CICD

Postman supports REST, SOAP, or plain HTTP. One can download Postman from here : https://www.postman.com/downloads/

In Postman, one can create individual requests and add them to a *Collection*. Inorder to run a *Collection* from commandline, we need **Newman**.

### To download and install Newman , run this command on the terminal :
```shell
npm install -g newman
```
This will install newman globally.

### Some basic Newman commands are :

Operations | Commands
---- | ----
Simple collection run | `newman run MyCollection.json`
Run a collection with environment details | `newman run MyCollection.json -e Env.json`
Run a collection with an external data source | `newman run MyCollection.json -d UserData.csv`
Generate Junit report | `newman run MyCollection.json --reporters junit --reporter-junit-export junit.xml`

### Chaining API requests :

Sometimes we may require to fire a chain of APIs passing data from one API response to another API request. Lets take the following example :
- Create a user
- Extract the User ID from the response
- Use the User ID to Delete the user

Here we will use the `Tests` section of a Request in Postman.

If the response of the Create User is :
```json
{
    "first_name": "Ashish",
    "last_name": "Ghosh",
    "email": "Ashish.Ghosh@jsonserver.com",
    "id": 3
}
```
To extract the `id` value and store it in a variable `UserID` defined in Postman, in the Test section add this :

```javascript
var jsonData = JSON.parse(responseBody);
postman.setEnvironmentVariable("UserID", jsonData.id);
``` 
Now this `UserID` variable can be used in the next Postman request.

For XML responses, the approach will be similar, but syntax will be different :

If the XML response is :
```xml
<?xml version='1.0' encoding='utf-8'?>
<soapenv:Envelope xmlns:soapenv="http://schemas.xmlsoap.org/soap/envelope/">
    <soapenv:Body>
        <soapenv:Result>
             <CreateUser success="true">
                <user version="1.0" alias="800006698200" status="Created" id="3"/>
            </CreateUser>
        </soapenv:Result>
    </soapenv:Body>
</soapenv:Envelope>
```
To extract the `id` value and store it in a variable `UserID` defined in Postman, in the Test section add this :

```javascript
var jsonObject = xml2Json(responseBody);
var user_id = jsonObject['soapenv:Envelope']['soapenv:Body']['soapenv:Result']['CreateUser']['user'].$.id;
pm.environment.set ("UserID", user_id);
``` 
To view a document generated by Postman click here : https://documenter.getpostman.com/view/5085233/TWDcEDxP
