# Restful booker-API

The Restful booker-API shows the testing of API using Postman, providing a collection of tests to validate various endpoints of the API and newman is used to generate all the test cases together.

_### Features_

- Detailed testing for GET, POST, PUT, PATCH, and DELETE requests  
- A structured collection of tests spanning various API endpoints  
- Configured environments for effortless switching between different setups  
- Pre-request scripts to initialize data before execution  
- Validation and assertion scripts for thorough test coverage

### **Technology used:**
- Postman
- Newman

### **Prerequisite:**
- Node Js
- Newman
- Newman Html Report Library
### **Installation**

1. Postman: If you haven't already, [download and install Postman.](https://www.postman.com/downloads/)
2. Clone the repository:
 ```console 
  git clone https://github.com/mdnazrulislam7255/Restful-booker-API.git
```
3. Import the Postman collection:
    - Open Postman.
    - Click on the Import button.
    - Select the file from the repository.
4. Import the Postman environment:
    - In Postman, click on the gear icon in the top right corner.
    - Select **Import** and choose the file.
5. Newman and Report Installation Process:
    - Newman Install Command:
     ```console 
      npm install -g newman
    ```
    - Newman Html Report Install Command:
     ```console 
      npm install -g newman-reporter-htmlextra
    ```
### **Usage**
1. Select Environment:
    -   In Postman, select the appropriate environment (e.g., Development, Production) from the top-right dropdown.
3. Run Collection:
    -   Select the imported collection from the Collections sidebar.
    -   Click on the Runner button to open the collection runner.
    -   Select the desired environment.
    -   Click Start Test to run the collection.
8. View Results:
    -   Once the tests are complete, view the results in the Runner tab.
    -   Detailed test results can be viewed for each request.
# Base URL
The base URL is: https://restful-booker.herokuapp.com 

# End points
## _** Create A Token For Authentication.**_
### Request URL: https://restful-booker.herokuapp.com/auth
### Request Method: POST
### Header: Content-Type: application/json
### Pre-request Script: None
### Post-response script:
``` console
var token_acess= pm.response.json();
pm.environment.set("tokenID", token_acess.token);
var storedToken = pm.environment.get("tokenID");
if (storedToken === token_acess.token) {
    pm.test("Token is generated", function () {
        pm.response.to.have.status(200);
    });
}
else{
     pm.test(" Regenerate Token", function () {
        pm.response.to.have.status(404);
    });
}
```
### Request Body:
 ```console 
{
	"username": "admin",
	"password": "password123"
}
```
###Response Body:
 ```console 
{
    "token": "06eb798bf6f2caa"
}
```
## _** Create Booking**_

### Request URL: https://restful-booker.herokuapp.com/booking/
### Request Method: POST
### Header: Content-Type: application/json
### Pre-request Script:
```console
var firstName= pm.variables.replaceIn("{{$randomFirstName}}")
pm.environment.set("firstName", firstName)

var lastName= pm.variables.replaceIn("{{$randomLastName}}")
pm.environment.set("lastName", lastName)

var totalPrice= pm.variables.replaceIn("{{$randomInt}}")
pm.environment.set("totalPrice", totalPrice)

var depositpaid= pm.variables.replaceIn("{{$randomBoolean}}")
pm.environment.set("depositpaid", depositpaid)

let currentDate = new Date();
let checkinDate = currentDate.toISOString().split("T")[0]; // Format: YYYY-MM-DD
// Set check-out as 30 days later
let checkoutDate = new Date();
checkoutDate.setDate(currentDate.getDate() + 30);
checkoutDate = checkoutDate.toISOString().split("T")[0];
pm.environment.set("checkin", checkinDate);
pm.environment.set("checkout", checkoutDate);
console.log("Generated Check-in Date:", checkinDate);
console.log("Generated Check-out Date:", checkoutDate);

var additionalneeds = pm.variables.replaceIn("{{$randomProduct}}")
pm.environment.set("additionalneeds",additionalneeds)
```
### Post response script:
``` console
var jsonData= pm.response.json();//generate id from json data
pm.environment.set("id",jsonData.bookingid);

var storedID = pm.environment.get("id");
if (storedID === jsonData.bookingid) {
    pm.test("Booking ID is generated", function () {
        pm.response.to.have.status(200);
    });
}
else{
     pm.test(" Booking ID is not generated", function () {
        pm.response.to.have.status(404);
    });
}
```
### Request body:
``` console
{
    "firstname" : "{{firstName}}",
    "lastname" : "{{lastName}}",
    "totalprice" : {{totalPrice}},
    "depositpaid" : {{depositpaid}},
    "bookingdates" : {
        "checkin" : "{{checkin}}",
        "checkout" : "{{checkout}}"
    },
    "additionalneeds" : "{{additionalneeds}}"
}
```
### Response body:
``` console
{
    "bookingid": 2471,
    "booking": {
        "firstname": "Helmer",
        "lastname": "Kemmer",
        "totalprice": 330,
        "depositpaid": true,
        "bookingdates": {
            "checkin": "2024-11-30",
            "checkout": "2024-12-30"
        },
        "additionalneeds": "Mouse"
    }
}
```
 ## _** Get Booking By ID**_
### Request URL: https://restful-booker.herokuapp.com/booking/{{id}}
### Request Method: GET
### Header: Content-Type: application/json
### Post response script:
``` console

var status = pm.response.code;
console.log(status);
switch (status) {
    case 200:
        var jsonData = pm.response.json();

        pm.test("Check Status code 200 OK", function () {
            pm.response.to.have.status(200);
        });

        pm.test("Check first name", function () {
            pm.expect(jsonData.firstname).to.equal(pm.environment.get("firstName"));
        });
        pm.test("Check last name", function () {
            pm.expect(jsonData.lastname).to.equal(pm.environment.get("lastName"))
        });
        pm.test("Check Total price", function(){
            pm.expect(jsonData.totalprice.toString()).to.equal(pm.environment.get("totalPrice"));
        });
        pm.test("Check Deposit Paid", function(){
            pm.expect(jsonData.depositpaid).to.equal(Boolean(pm.environment.get("depositpaid")));
        });
        pm.test("Check checkin", function () {
            pm.expect(jsonData.bookingdates.checkin).to.equal(pm.environment.get("checkin"))
        });
        pm.test("Check checkout", function () {
            pm.expect(jsonData.bookingdates.checkout).to.equal(pm.environment.get("checkout"))
        });
        pm.test("Check additional needs", function () {
            pm.expect(jsonData.additionalneeds).to.equal(pm.environment.get("additionalneeds"))
        });
        break;

    case 404:
        pm.test("Something went wrong/Not found");
        break;

    case 500:
        pm.test("Something went wrong");
        break;

    default:
}
pm.test("Response time is less than 1000ms", function () {
    pm.expect(pm.response.responseTime).to.be.below(1000);
});

pm.test("Content-Type is present", function () {
    pm.expect(pm.response.headers.get('Content-Type')).to.eql('application/json; charset=utf-8')
});
//Data types
pm.test("Test the data type of response",()=>{
    pm.expect(jsonData.firstname).to.be.a("string");
    pm.expect(jsonData.lastname).to.be.a("string");
    pm.expect(jsonData.totalprice).to.be.a("number");
    pm.expect(jsonData.depositpaid).to.be.a("Boolean");
    pm.expect(jsonData.bookingdates.checkin).to.be.a("string");
    pm.expect(jsonData.bookingdates.checkout).to.be.a("string");
    pm.expect(jsonData.additionalneeds).to.be.a("string");
});
```
### Response Body:
``` console
{
    "firstname": "Helmer",
    "lastname": "Kemmer",
    "totalprice": 330,
    "depositpaid": true,
    "bookingdates": {
        "checkin": "2024-11-30",
        "checkout": "2024-12-30"
    },
    "additionalneeds": "Mouse"
}
```
## _** Update the Booking Details**_
### Request URL: https://restful-booker.herokuapp.com/booking/{{id}}
### Request Method: PUT
### Headers: Content-Type: application/json, Cookie : token={{tokenID}}
### Pre-request Script:
``` console
var firstName= pm.variables.replaceIn("{{$randomFirstName}}")
pm.environment.set("firstName", firstName)

var lastName= pm.variables.replaceIn("{{$randomLastName}}")
pm.environment.set("lastName", lastName)

var totalPrice= pm.variables.replaceIn("{{$randomInt}}")
pm.environment.set("totalPrice", totalPrice)

var depositpaid= pm.variables.replaceIn("{{$randomBoolean}}")
pm.environment.set("depositpaid", depositpaid)

let currentDate = new Date();
let checkinDate = currentDate.toISOString().split("T")[0]; // Format: YYYY-MM-DD
// Set check-out as 30 days later
let checkoutDate = new Date();
checkoutDate.setDate(currentDate.getDate() + 30);
checkoutDate = checkoutDate.toISOString().split("T")[0];
pm.environment.set("checkin", checkinDate);
pm.environment.set("checkout", checkoutDate);
console.log("Generated Check-in Date:", checkinDate);
console.log("Generated Check-out Date:", checkoutDate);

var additionalneeds = pm.variables.replaceIn("{{$randomProduct}}")
pm.environment.set("additionalneeds",additionalneeds)
```
### Request body:
``` console
{
    "firstname" : "{{firstName}}",
    "lastname" : "{{lastName}}",
    "totalprice" : {{totalPrice}},
    "depositpaid" : {{depositpaid}},
    "bookingdates" : {
        "checkin" : "{{checkin}}",
        "checkout" : "{{checkout}}"
    },
    "additionalneeds" : "{{additionalneeds}}"
}
```
### Response body:
``` console
{
    "firstname": "Shanna",
    "lastname": "Thompson",
    "totalprice": 827,
    "depositpaid": true,
    "bookingdates": {
        "checkin": "2024-11-30",
        "checkout": "2024-12-30"
    },
    "additionalneeds": "Chips"
}
```
## _** Update the Booking Details**_
### Request URL: https://restful-booker.herokuapp.com/booking/{{id}}
### Request Method: PATCH
### Headers: Content-Type: application/json, Cookie : token={{tokenID}}
### Pre-request Script: none
### Request body:
``` console
{
    "firstname" : "Jova",
    "lastname" : "Mili",
    "totalprice" : 77
}
```
### Response body:
``` console
{
    "firstname": "Jova",
    "lastname": "Mili",
    "totalprice": 77,
    "depositpaid": true,
    "bookingdates": {
        "checkin": "2024-11-30",
        "checkout": "2024-12-30"
    },
    "additionalneeds": "Chips"
}
```
## _** Delete Booking Details**_
### Request URL: https://restful-booker.herokuapp.com/booking/{{id}}
### Request Method: DELETE
### Headers: Content-Type: application/json, Cookie : token={{tokenID}}
### Response Body: None 
### post response script:
``` console
if (pm.environment.get("tokenID")) {
   pm.environment.unset("tokenID");
   console.log("Booking is cleared successfully.");
} else {
   console.log("No Booking found in the environment.");
}
```
## Run Command:  
- Run Command for Console: 
```console 
newman run Restful_Booker.postman_collection.json -e restful_booking_env.postman_environment.json  
```
- Run Command for Report: 
```console 
newman run Restful_Booker.postman_collection.json -e restful_booking_env.postman_environment.json -r cli,htmlextra
```
## Newman report
![image](https://github.com/user-attachments/assets/fd378642-650d-4d71-a693-6173fbf392b0)
![image](https://github.com/user-attachments/assets/00c30c22-dca5-4182-8c2e-3b6a1d190c37)

## Contact
For questions or support, contact the development team at:
Email: _**nazrul15-7255@diu.edu.bd**_
