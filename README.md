# Tango Card - Rewards as a Service

# RaaS API - Version 1 

## Sandbox credentials
Please email sdk@tangocard.com to receive credentials for the RaaS API Sandbox environment. 
The endpoint for the RESTful interface on the Sandbox environment is https://sandbox.tangocard.com/raas/v1/

To review the end-to-end user experience, any Tango Cards purchased on the Sandbox environment can be redeemed on the [Tango Card Sandbox website](https://integration-www.tangocard.com/)

## Test drive the RaaS API

You have couple of options to test drive the RaaS API without even writing a single line of code.

### RaaS API Test Console
One way to test drive the RaaS API, you can visit the 
[RaaS API Test Console](https://integration-www.tangocard.com/test_raas_api). 

Using the Test Console you can go through each resource supported by the RaaS API, 
the requests and the responses for each of these resources.

### Mashape
Another way to test drive the RaaS API (albiet only against our Production environment) is 
[Rewards as a Service](https://www.mashape.com/tangocard/rewards-as-a-service/) page on Mashape.

## Production credentials
Once you are ready to move to production, you need to agree to the [RaaS API Terms & Conditions](https://creator.zoho.com/shighet/raas-api-agreement/form-perma/RaaS_API_Agreement/U12njtAWxArN6VnavC92RTZkw2kUOQhbkZxKsKD0jHAOqFwxvQRSQKfU2xW8xytQuMPHUafDD3P0vnsXVKDYAjOOR3qHOdq7kQHr/). 
Once you agree to the Terms & Conditions, we will create credentials on the Production environment and follow up with you. 
The endpoint for the RESTful interface on the Production environment is https://api.tangocard.com/raas/v1/

## TangoCard.com credentials and RaaS credentials
Our previous version of the API uses credentials (username and password) created on TangoCard.com

However, the RaaS API uses a Platform name and a Platform key that needs to be created for you. 

You can't mix the credentials for TangoCard.com site with the credentials for the RaaS API.

## Table of Contents

- [RaaS API - Version 1](#raas-api---version-1)
	- [Table of Contents](#table-of-contents)
	- [What is RaaS](#what-is-raas)
	- [System-wide Notes](#system-wide-notes)
- [Resources](#resources)
	- [Account Resources](#account-resources)
		- [Create a new platform account](#create-a-new-platform-account)
		- [Get the information for a specific platform account](#get-the-information-for-a-specific-platform-account)
	- [Fund Resources](#fund-resources)
		- [Fund a platform's account](#fund-a-platforms-account)
	- [Reward Resources](#reward-resources)
		- [Get the catalog of available items](#get-the-catalog-of-available-items)
	- [Order Resources](#order-resources)
		- [Place an order](#place-an-order)
		- [Retrieve a historical order](#retrieve-a-historical-order)
		- [Retrieve a list of historical orders](#retrieve-a-list-of-historical-orders)
- [Responses](#responses)
	- [Generic Errors](#generic-errors)
		- [Authentication Needed](#authentication-needed)
		- [Bad Authentication](#bad-authentication)
		- [Input Validation Failed](#input-validation-failed)
		- [Not Found](#not-found)
		- [System Error](#system-error)
		- [Resource Exists](#resource-exists)
		- [Invalid Resource Entity](#invalid-resource-entity)
		- [Invalid Accept Content-type](#invalid-accept-content-type)
	- [Resource Errors](#resource-errors)
		- [Fund Create - Payment Error (Credit Card)](#fund-create---payment-error-credit-card)
		- [Order create - Insufficient Funds](#order-create---insufficient-funds)
	- [Resource Successes](#resource-successes)
		- [Account Created](#account-created)
		- [Account](#account)
		- [Fund Created](#fund-created)
		- [Order Created](#order-created)
		- [Order List](#order-list)
		- [Order](#order)
		- [Rewards List](#rewards-list)
- [Troubleshooting](#troubleshooting)
	- [Communicatin Problems](#communicatin-problems)
		- [SSL/TLS](#ssltls)

## What is RaaS

With our RaaS™ API you can elegantly knit a sophisticated rewards program into your platform. Create an account, fund an account, manage catalog, send rewards, and get order history - all available on demand, real time, and as a service.


## System-wide Notes

* [JSON](http://en.wikipedia.org/wiki/JSON) is the only currently-supported content type for inputs or outputs. It is not required that the requests contain an Accept header, but if they do it should contain "application/json" or "*/*.
* All calls require the platform's authentication. Authentication uses [HTTP Basic auth](http://en.wikipedia.org/wiki/Basic_access_authentication) with the platform name as the username and the platform access key as the password. These will be assigned by Tango Card.
* All dates will/must be represented in a format that is compatible with [ISO 8601](http://en.wikipedia.org/wiki/ISO_8601) (for example: *2013-02-21T20:37:07-07:00*).
* Platform will be responsible for handling access to it's accounts (e.g. not allowing ACC1 to place an order using ACC2's account)... requests from authenticated platforms against their accounts will be implicitly trusted.
* Whenever money is concerned, it is in cents (e.g. $4 = 400).
* Fields in the RaaS API response objects may be added at any time. This means that whatever is used to demarshall the JSON responses into native data types needs to be able to handle unknown fields without failing.
* All responses may include a `system_message` field. This is a note from TangoCard to the platforms and not intended for end users. This will notify of things like planned outages and should be monitored.




# Resources



## Account Resources

The following is a section of resources related to the platform's accounts.

* An "account identifier" in the system can be whatever is convenient for the platform as long as it adheres to the following rules:
 * Between 5 and 96 characters in length (inclusive).
 * Must be made up exclusively of: alphanumeric (A-Z, a-z, 0-9), underscore (_), dash (-), or plus (+).
 * Must be unique to the platform.
* A "customer" is a mechanism for denoting a company, department, etc an account belongs to. 



### Create a new platform account.

POST /raas/v1/accounts

The input object is defined by [this JSON-Schema](account_create.schema.json).

Example request/response:

	> POST /raas/v1/accounts HTTP/1.1
	> Authorization: Basic C0FFEEC0FFEEC0FFEEC0FFEE
	> Host: integration-api.tangocard.com
	> Accept: */*
	> Content-Length: 72
	> Content-Type: application/json
	>
	{
		"identifier": "123456",
		"email"     : "demo@example.com",
		"customer"  : "CompanyA"
	}


	< HTTP/1.1 201 Created
	< Content-Type: application/json; charset=utf-8
	< Location: https://integration-api.tangocard.com/raas/v1/accounts/123456
	< Content-Length: 118
	< 
	{
		"success": true,
		"account": {
			"identifier"       : "123456",
			"email"            : "demo@example.com",
			"customer"         : "CompanyA",
			"available_balance": 0
		}
	}





### Get the information for a specific platform account.

GET /raas/v1/accounts/{customer}/{account_identifier}

Example request/response:

	> GET /raas/v1/accounts/CompanyA/123456 HTTP/1.1
	> Authorization: Basic C0FFEEC0FFEEC0FFEEC0FFEE
	> Host: integration-api.tangocard.com
	> Accept: */*
	> 


	< HTTP/1.1 200 OK
	< Content-Type: application/json; charset=utf-8
	< Content-Length: 112
	< 
	{
		"success": true,
		"account": {
			"identifier"       : "123456",
			"email"            : "demo@example.com",
			"customer"         : "CompanyA",
			"available_balance": 0
		}
	}






## Fund Resources 





### Fund a platform's account.

POST /raas/v1/funds

The input object is defined by [this JSON-Schema](fund_create.schema.json).

Example request/response:

	> POST /raas/v1/funds HTTP/1.1
	> Authorization: Basic C0FFEEC0FFEEC0FFEEC0FFEE
	> Host: integration-api.tangocard.com
	> Accept: */*
	> Content-Length: 323
	> Content-Type: application/json
	> 
	{
		"customer"          : "CompanyA",
		"account_identifier": "123456",
		"amount"            : 100000,
		"client_ip"         : "127.0.0.1",
		"credit_card"       : {
			"number"         : "4111111111111111",
			"expiration"     : "02/20",
			"security_code"  : "123",
			"billing_address": {
				"f_name" : "John",
				"l_name" : "Doe",
				"address": "123 Sesame St, Apt 1",
				"city"   : "Smallville",
				"state"  : "WA",
				"country": "USA",
				"zip"    : "11111",
				"email"  : "test@example.com"
			}
		}
	}


	< HTTP/1.1 200 OK
	< Content-Type: application/json; charset=utf-8
	< Content-Length: 71
	<
	{
		"success": true,
		"fund_id": "RF11-22222222-33",
		"amount" : 100000
	}






## Reward Resources





### Get the catalog of available items.

GET /raas/v1/rewards

* A negative-value "unit price" indicates that the item has a variable price. In this case the minimum and maximum fields should be consulted.

Example request/response:

	> GET /raas/v1/rewards HTTP/1.1
	> Authorization: Basic C0FFEEC0FFEEC0FFEEC0FFEE
	> Host: integration-api.tangocard.com
	> Accept: */*
	>
	
	
	< HTTP/1.1 200 OK
	< Content-Type: application/json; charset=utf-8
	< 
	{
		"success": true,
		"brands" : [
			{
				"description": "Tango Card",
				"image_url": "http://static.tangocard.com/graphics/item-images/tango-card-gift-card.png",
				"rewards"    : [
					{
						"description"  : "Tango Card E-Custom",
						"sku"          : "TNGO-E-V-STD",
						"currency_type": "USD",
						"unit_price"   : -1,
						"available"    : true,
						"min_price"    : "100",
						"max_price"    : "100000"
					}
				]
			},
			{
				"description": "Amazon.com",
				"image_url": "http://static.tangocard.com/graphics/item-images/amazon-gift-card.png",
				"rewards"    : [
					{
						"description"  : "Amazon E-Gift Card Custom",
						"sku"          : "AMZN-E-V-STD",
						"currency_type": "USD",
						"unit_price"   : -1,
						"available"    : true,
						"min_price"    : "100",
						"max_price"    : "100000"
					}
				]
			},
			{
				"description": "Apple iTunes",
				"image_url": "http://static.tangocard.com/graphics/item-images/itunes-gift-card.png",
				"rewards"    : [
					{
						"description"  : "iTunes E-Gift Card $15",
						"sku"          : "APPL-E-1500-STD",
						"currency_type": "USD",
						"unit_price"   : 1500,
						"available"    : true
					},
					{
						"description"  : "iTunes E-Gift Card $25",
						"sku"          : "APPL-E-2500-STD",
						"currency_type": "USD",
						"unit_price"   : 2500,
						"available"    : true
					},
					{
						"description"  : "iTunes E-Gift Card $50",
						"sku"          : "APPL-E-5000-STD",
						"currency_type": "USD",
						"unit_price"   : 5000,
						"available"    : true
					}
				]
			}
		]
	}






## Order Resources





### Place an order.

POST /raas/v1/orders

The input object is defined by [this JSON-Schema](order_create.schema.json).

Example request/response:

	> POST /raas/v1/orders HTTP/1.1
	> Authorization: Basic C0FFEEC0FFEEC0FFEEC0FFEE
	> Host: integration-api.tangocard.com
	> Accept: */*
	> Content-Length: 192
	> Content-Type: application/json
	> 
	{
		"customer"          : "CompanyA",
		"account_identifier": "123456",
		"recipient"         : {
			"name" : "John Doe",
			"email": "john.doe@example.com"
		},
		"sku"               : "TNGO-E-V-STD",
		"amount"            : 1000,
		"reward_message"    : "Thank you for participating in the XYZ survey.",
		"reward_subject"    : "XYZ Survey, thank you...",
		"reward_from"       : "Jon Survey Doe"
	}
	
	
	< HTTP/1.1 201 Created
	< Content-Type: application/json; charset=utf-8
	< Location: https://integration-api.tangocard.com/raas/v1/orders/
	< Content-Length: 297
	< 
	{
		"success": true,
		"order"  : {
			"order_id"          : "123-12345678-12",
			"account_identifier": "12345678",
			"sku"               : "TNGO-E-V-STD",
			"amount"            : 1000,
			"reward_message"    : "Thank you for participating in the XYZ survey.",
			"reward_subject"    : "XYZ Survey, thank you...",
			"reward_from"       : "Jon Survey Doe",
			"delivered_at"      : "2013-03-12T15:17:16+00:00",
			"recipient"         : {
				"name" : "John Doe",
				"email": "john.doe@example.com"
			}
		}
	}




### Retrieve a historical order.

GET /raas/v1/orders/{order_id}

Example request/response:

	> GET /raas/v1/orders/123-12345678-12 HTTP/1.1
	> Authorization: Basic C0FFEEC0FFEEC0FFEEC0FFEE
	> Host: integration-api.tangocard.com
	> Accept: */*
	> 


	< HTTP/1.1 200 OK
	< Content-Type: application/json; charset=utf-8
	< Content-Length: 297
	< 
	{
		"success": true,
		"order"  : {
			"order_id"          : "123-12345678-12",
			"account_identifier": "12345678",
			"sku"               : "TNGO-E-V-STD",
			"amount"            : 1000,
			"reward_message"    : "Thank you for participating in the XYZ survey.",
			"reward_subject"    : "XYZ Survey, thank you...",
			"reward_from"       : "Jon Survey Doe",
			"delivered_at"      : "2013-03-12T15:17:16+00:00",
			"recipient"         : {
				"name" : "John Doe",
				"email": "john.doe@example.com"
			}
		}
	}




### Retrieve a list of historical orders.

GET /raas/v1/orders{?start_date,end_date,offset,limit,customer,account_identifier}

* All inputs are optional.
 * start_date / end_date : datetimes (ISO 8601) between which to search
 * offset : How far into the resultset to start.
 * limit : How many to return (maximum of 100).
 * account_identifier : Filter to only a single platform account.
 * customer : Filter only to a single customer.

Example request/response:

	> GET /raas/v1/orders?start_date=2013-03-01T00:00:00-08:00&end_date=2013-04-01T00:00:00-08:00&offset=0&limit=2&account_identifier=12345678&customer=CompanyA HTTP/1.1
	> Authorization: Basic C0FFEEC0FFEEC0FFEEC0FFEE
	> Host: integration-api.tangocard.com
	> Accept: */*
	> 
	
	
	< HTTP/1.1 200 OK
	< Content-Type: application/json; charset=utf-8
	< Content-Length: 603
	< 
	{
		"success": true,
		"orders" : [
			{
				"order_id"          : "123-12345678-00",
				"account_identifier": "12345678",
				"sku"               : "TNGO-E-V-STD",
				"amount"            : 1000,
				"reward_message"    : "Thank you for participating in the ABC survey.",
				"reward_subject"    : null,
				"delivered_at"      : "2013-03-11T22:20:56+00:00",
				"recipient"         : {
					"name" : "John Doe",
					"email": "john.doe@example.com"
				}
			},
			{
				"order_id"          : "123-12345678-12",
				"account_identifier": "12345678",
				"sku"               : "TNGO-E-V-STD",
				"amount"            : 1000,
				"reward_message"    : "Thank you for participating in the XYZ survey.",
				"reward_subject"    : "XYZ Survey, thank you...",
				"reward_from"       : "Jon Survey Doe",
				"delivered_at"      : "2013-03-12T15:17:16+00:00",
				"recipient"         : {
					"name" : "John Doe",
					"email": "john.doe@example.com"
				}
			}
		]
	}






# Responses

* All responses will have a field called "success" which is a boolean to denote high-level success/failure.




## Generic Errors

* The generic errors can be triggered by any (multiple) calls.
* Many errors will have a field called "error_message" which can be used to give more information as to why it failed (for debugging, not displaying to end-users).




### Authentication Needed

Authentication credentials were not supplied, but they're required.

* **HTTP Status Code:** 401 Unauthorized
* **HTTP Headers:** 
	* WWW-Authenticate: Basic realm="integration-api.tangocard.com"
* **Response Object:**
	* success : (boolean)
	* error_message : (string) A debug message.
	
	
	
### Bad Authentication

The supplied authentication was invalid.

* **HTTP Status Code:** 403 Forbidden
* **HTTP Headers:**
	* X-Status-Reason: Authentication failed
* **Response Object:**
	* success : (boolean)
	* error_message : (string) A debug message.



### Input Validation Failed

The input object was invalid for the reasons explained below.

* **HTTP Status Code:** 403 Forbidden
* **HTTP Headers:** 
	* X-Status-Reason: Validation failed
* **Response Object:**
	* success : (boolean)
	* error_message : (string) A debug message.
	* invalid_inputs : (array) An array of objects defined by:
		* field : (string) The name of the input field which was invalid.
		* error : (string) A description of why the field was invalid (for debugging, not displaying to end-users).



### Not Found

The requested resource was not found.

* **HTTP Status Code:** 404 Not Found
* **Response Object:** 
	* success : (boolean)
	* error_message : (string) A debug message (description of what was not found).

	

### System Error

This is a fatal error that can not be recovered from. Please contact Tango Card engineering with the error code for assistance.

* **HTTP Status Code:** 500 System Error
* **Response Object:** 
	* success : (boolean)
	* error_message : (string) An error code to be relayed to Tango Card Engineering.
 


### Resource Exists

The resource that was to be created already exists.

* **HTTP Status Code:** 409 Conflict
* **Response Object:** 
	* success : (boolean)
	* error_message : (string) A debug message.



### Invalid Resource Entity

The entity provided does not match the content type.

* **HTTP Status Code:** 400 Bad Request
* **Response Object:** 
	* success : (boolean)
	* error_message : (string) A debug message.



### Invalid Accept Content-type

The Accept header did not contain content-types that we could fulfill.

* **HTTP Status Code:** 400 Bad Request
* **Response Object:** 
	* success : (boolean)
	* error_message : (string) A debug message.
	* content_types : (array) An array of content types we support.



## Resource Errors



### Fund Create - Payment Error (Credit Card)

There was an error processing the credit card.

* **HTTP Status Code:** 400 Bad Request
* **HTTP Headers:** 
	* X-Status-Reason: Credit card authorization failure.
* **Response Object:** 
	* success : (boolean)
	* denial_code : (string) Will be one of the following:  CC_DECLINED, CC_ERROR, CC_REVIEW, CC_UNKNOWN
	* denial_message : (string) An error message that CAN be displayed to end-users.
	
	
	
### Order create - Insufficient Funds

There were not enough funds available in the account to cover the cost of the order.

* **HTTP Status Code:** 400 Bad Request
* **HTTP Headers:** 
	* X-Status-Reason: Funding Failed
* **Response Object:** 
	* success : (boolean)
	* error_message : (string) A debug message (description of what was not found).
	* available_balance : (integer) The currently available funds for the account.




## Resource Successes



### Account Created

An account was created.

* **HTTP Status Code:** 201 Created
* **HTTP Headers:** 
	* Location: https://integration-api.tangocard.com/raas/v1/accounts/{customer}/{account_identifier}
* **Response Object:** 
	* success : (boolean)
	* account : (object)
		* identifier : (string) The identifier for this account.
		* email : (string) The email address associated with this account.
		* customer : (string) The customer associated with this account.
		* available_balance : (integer) The currently available funds for the account.



### Account

The result of looking up an account.

* **HTTP Status Code:** 200 OK
* **Response Object:** 
	* success : (boolean)
	* account : (object)
		* identifier : (string) The identifier for this account.
		* email : (string) The email address associated with this account.
		* customer : (string) The customer associated with this account.
		* available_balance : (integer) The currently available funds for the account.
	


### Fund Created

An account was funded.

* **HTTP Status Code:** 200 OK
* **Response Object:** 
	* success : (boolean)
	* fund_id : (string) A reference id that Tango Card can use to lookup this fund.
	* amount : (integer) The amount that was funded.
	


### Order Created

An order was created.

* **HTTP Status Code:** 201 Created
* **HTTP Headers:** 
	* Location: https://integration-api.tangocard.com/raas/v1/{order_id}
* **Response Object:** 
	* success : (boolean)
	* order : (object) Defined by:
		* order_id : (string) The id that the order can be referenced by in the future.
		* customer : (string) The customer associated with this account.
		* account_identifier : (string) The account used to place this order.
		* sku : (string) The reward that was purchsed.
		* amount : (int) The value of the reward that was purchased
		* reward_message : (string|null) The message (to the recipient) that was associated with this order.
		* reward_subject : (string|null) The subject for the reward email, if it was set.
		* delivered_at : (string) When the reward was delivered.
		* recipient : (object) Defined by:
			* name : (string) The name of the recipient.
			* email : (string) The recipient's email address.
		* reward : (object) defined by the following (will only be included if send_reward was false):
			* token : (string) A token that can be used by Tango Card to look up the reward (will always be included).
			* number : (string) The reward's card number (optional).
			* pin : (string) The reward's card pin (optional).
			* redemption_url : (string) The reward's redemption URL (optional).
			* event_number : (string) The reward's card event number (optional).
			
			
	


### Order List

A list of orders.

* **HTTP Status Code:** 200 OK
* **Response Object:** 
	* success : (boolean)
	* orders : (array) An array of order objects, defined by:
		* order_id : (string) The id that the order can be referenced by in the future.
		* customer : (string) The customer associated with this account.
		* account_identifier : (string) The account used to place this order.
		* sku : (string) The reward that was purchsed.
		* amount : (int) The value of the reward that was purchased
		* reward_message : (string|null) The message (to the recipient) that was associated with this order.
		* reward_subject : (string|null) The subject for the reward email, if it was set.
		* delivered_at : (string) When the reward was delivered.
		* recipient : (object) Defined by:
			* name : (string) The name of the recipient.
			* email : (string) The recipient's email address.
	


### Order

A single order.

* **HTTP Status Code:** 200 OK
* **Response Object:** 
	* success : (boolean)
	* order : (object) Defined by:
		* order_id : (string) The id that the order can be referenced by in the future.
		* customer : (string) The customer associated with this account.
		* account_identifier : (string) The account used to place this order.
		* sku : (string) The reward that was purchsed.
		* amount : (int) The value of the reward that was purchased
		* reward_message : (string|null) The message (to the recipient) that was associated with this order.
		* reward_subject : (string|null) The subject for the reward email, if it was set.
		* delivered_at : (string) When the reward was delivered.
		* recipient : (object) Defined by:
			* name : (string) The name of the recipient.
			* email : (string) The recipient's email address.



### Rewards List

A list of rewards.

* **HTTP Status Code:** 200 OK
* **Response Object:** 
	* success : (boolean)
	* brands : (array) An array of brand objects defined by:
		* description : (string) A human-readable description of the reward brand.
		* rewards : (array) An array of objects defined as:
			* description : (string) A human-readable description of the reward.
			* sku : (string) The SKU used when ordering.
			* currency_type : (string) The currency of the reward (e.g. USD).
			* unit_price : (integer) The price of the reward (negative values denote variable prices).
			* available : (boolean) Whether the reward is currently available.
			* min_price : (integer) For variable price rewards this denoted the minimum price available. Field may not be present for non-variable.
			* max_price : (integer) For variable price rewards this denoted the maximum price available. Field may not be present for non-variable.
		
		
		
		
		
# Troubleshooting

## Communicatin Problems

### SSL/TLS
Server environments are often set up to only trust the absolute minimum number of Certificate Authorities they can get away with. As such, many environments may not have the certificates available to allow your application to validate our certificates. As all communication with Tango Card's RaaS API is handled over SSL, not having the certificate chain in place to allow you to validate our cert will (at best) disallow communication or (at worst) expose you to the potential for [main-in-the-middle attacks](http://en.wikipedia.org/wiki/Man-in-the-middle_attack). Thus, it is essential that the environment be set up to allow for safe SSL communication.

There are a few ways to acheive this (like adding the CA's cert to the system's trusted list), but probably the easiest (and most portable) would be to include the certificate in your application. The Certificate Authority that issued our server certificates is [DigiCert](https://www.digicert.com/), and we have one of their *DigiCert High Assurance CA-3* certs. You can get DigiCert's root and intermediate certificates from [https://www.digicert.com/digicert-root-certificates.htm](their site). Alternatively, you can download the entire chain needed [here](ssl/tangocard_digicert_chain.pem) in PEM format.

Once you have the certificate in hand it will need to be referenced from your application's code. The details on how to do this are highly specific to the library being used to make the connection, but here are a few examples to demonstrate the idea:

```ruby
# Ruby (using net/https)
https = Net::HTTP.new('integration-api.tangocard.com', 443) 
https.use_ssl = true 
https.verify_mode = OpenSSL::SSL::VERIFY_PEER 
https.ca_file = File.join(File.dirname(__FILE__), "../ca_certs/tangocard_digicert_chain.pem") 
https.request_get('/fake/example')
```

```php
// PHP (using curl)
$curl = curl_init('https://integration-api.tangocard.com/fake/example');
curl_setopt($curl, CURLOPT_SSL_VERIFYPEER, true);
curl_setopt($curl, CURLOPT_CAINFO, __DIR__ . "../ca_certs/tangocard_digicert_chain.pem");
curl_exec($curl);
```


One thing to take note of in both examples is that OpenSSL is being instructed to **VERIFY PEER**. This setting is essential as without it you will know that your communication is encrypted, but you won't know who it is you're talking to.

Note: We use a wild-card certificate from DigiCert and hence the certificate chain is the same for both Sandbox (https://sandbox.tangocard.com) and Production (https://api.tangocard.com) environments.

[![githalytics.com alpha](https://cruel-carlota.pagodabox.com/0d99126c1cf8686391099d328e3c2363 "githalytics.com")](http://githalytics.com/github.com/tangocarddev)
