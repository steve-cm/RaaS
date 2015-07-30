# Tango Card - Rewards as a Service&trade;

# RaaS&reg; API - Gift Card API

## Table of Contents

- [RaaS API - Version 1](#raas-api---version-1)
	- [What is RaaS](#what-is-the-raas-api)
	- [System-wide Notes](#system-wide-notes)
	- [SSL/TLS](#ssltls)
- [Resources](#resources)
	- [Account Resources](#account-resources)
		- [Create a new platform account](#create-a-new-platform-account)
		- [Get the information for a specific platform account](#get-the-information-for-a-specific-platform-account)
	- [Fund Resources](#fund-resources)
		- [Register a credit card to an account](#register-a-credit-card-to-an-account)
		- [Fund a platform's account](#fund-a-platforms-account)
		- [Delete a credit card from an account](#delete-a-credit-card-from-an-account)
	- [Reward Resources](#reward-resources)
		- [Get the catalog of available items](#get-the-catalog-of-available-items)
	- [Order Resources](#order-resources)
		- [Place an order](#place-an-order)
		- [Resend an order](#resend-an-order)
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
		- [Credit card registered](#credit-card-registered)
		- [Fund Created](#fund-created)
		- [Credit card deleted](#credit-card-deleted)
		- [Order Created](#order-created)
		- [Order List](#order-list)
		- [Order](#order)
		- [Rewards List](#rewards-list)

		
## What is the RaaS&reg; API?

With our RaaS API you can elegantly knit a sophisticated rewards program into your platform. Create an account, fund an account, manage catalog, send rewards, and get order history - all available on demand, real time, and as a service.

## Test drive the RaaS API

You can test drive the RaaS API without writing a single line of code!  

Using the [RaaS API Test Console](https://integration-www.tangocard.com/raas_api_console/), you can easily go through each resource supported our API and see the requests and the responses for each of these resources. When you are comfortable with the concepts you can begin coding by requesting Sandbox credentials for our test site. Then, if you run into problems during the coding phase you can come back to our console and compare your JSON to our requests and responses to see what is different.


## Sandbox credentials

Please email sdk@tangocard.com to receive credentials for the RaaS API Sandbox environment. 
The endpoint for the RESTful interface on the Sandbox environment is https://sandbox.tangocard.com/raas/v1/

An important note about *Test Codes*: Rewards delivered from Tango Card's Sandbox environment contain sample codes and have no monetary or reemable value. Additionally, please note that some Amazon.com denominations, like $20, may fail in the Sandbox environment. This is intentional behavior for the purpose of simulating an API error. If you encounter an error when testing an Amazon.com code, please try another denomination. Be assured that Amazon.com orders work consistently in the production environment. 

When you have completed your development and are ready for production testing / launch you can request production credentials.


## Production credentials

Once you are ready to move to production, you need to agree to the [RaaS API Terms & Conditions](https://www.tangocard.com/tango_card_agreements/#ETOS). 
Once you agree to the Terms & Conditions, we will create credentials on the Production environment and follow up with you. 
The endpoint for the RESTful interface on the Production environment is https://api.tangocard.com/raas/v1/


## RaaS API credentials versus TangoCard.com user credentials

Our now-depricated legacy SDKs used username and password credentials (like those used for user accounts on our website). If you are upgrading to the RaaS API from one of our legacy SDKs, please note that our RaaS API uses a Platform Name and Platform Key that need to be created for your RaaS API account and you can not mix the credentials for TangoCard.com site with the credentials for the RaaS API.


## System-wide Notes

* [JSON](http://en.wikipedia.org/wiki/JSON) is the only currently-supported content type for inputs or outputs. It is not required that the requests contain an Accept header, but if they do it should contain "application/json" or "*/*.
* All calls require the platform's authentication. Authentication uses [HTTP Basic auth](http://en.wikipedia.org/wiki/Basic_access_authentication) with the platform name as the username and the platform access key as the password. These will be assigned by Tango Card.
* All dates will/must be represented in a format that is compatible with [ISO 8601](http://en.wikipedia.org/wiki/ISO_8601) (for example: *2013-02-21T20:37:07-07:00*).
* Platform will be responsible for handling access to it's accounts (e.g. not allowing ACC1 to place an order using ACC2's account)... requests from authenticated platforms against their accounts will be implicitly trusted.
* Whenever money is concerned, it is in cents (e.g. $4 = 400).
* Fields in the RaaS API response objects may be added at any time. This means that whatever is used to demarshall the JSON responses into native data types needs to be able to handle unknown fields without failing.
* All responses may include a `system_message` field. This is a note from TangoCard to the platforms and not intended for end users. This will notify of things like planned outages and should be monitored.


## SSL/TLS

All communication with Tango Card's RaaS API is handled over SSL, a commonly-used protocol for managing secured message transmissions on the Internet. As a result, clients of the RaaS API need to ensure that you have the [chain (intermediate) certificate](http://en.wikipedia.org/wiki/Intermediate_certificate_authorities) in place on your server. This is important as not having the chain certificate in place will (at best) disallow communication or (at worst) expose you to the potential for [man-in-the-middle attacks](http://en.wikipedia.org/wiki/Man-in-the-middle_attack). To accomplish this, we recommend you add the CA's cert to your system's trusted list. If that's not possible, an alternative is to include the certificate in your application. Major CAs deliver a ‘bundled’ file containing the complete certificate chain providing a single installation method for the certificate.
 
The Certificate Authority that issued our server certificates is [DigiCert](https://www.digicert.com/), and we have one of their *DigiCert SHA2 Secure Server CA* certs. You can get DigiCert's root and intermediate certificates from [https://www.digicert.com/digicert-root-certificates.htm](https://www.digicert.com/digicert-root-certificates.htm).  If you choose to reference the certificate chain from your application's code, the details on how to do this are highly specific to the library being used to make the connection, but here are a few examples to demonstrate the idea:
 
 ```ruby
 # Ruby (using net/https)
 https = Net::HTTP.new('integration-api.tangocard.com', 443) 
 https.use_ssl = true 
 https.verify_mode = OpenSSL::SSL::VERIFY_PEER 
 https.ca_file = File.join(File.dirname(__FILE__), "../ca_certs/digicert_chain.pem") 
 https.request_get('/fake/example')
 ```
 
 ```PHP
 # PHP (using curl)
 $curl = curl_init('https://integration-api.tangocard.com/fake/example');
 curl_setopt($curl, CURLOPT_SSL_VERIFYPEER, true);
 curl_setopt($curl, CURLOPT_CAINFO, __DIR__ . "../ca_certs/digicert_chain.pem");
 curl_exec($curl);
 ```
 
 ```python
 # python (using requests)
import requests
from requests.auth import HTTPBasicAuth
# SSL is used by default.
requests.get('https://integration-api.tangocard.com/fake/example',
             auth=HTTPBasicAuth(username, password))
```

One thing to take note of in both examples is that OpenSSL is being instructed to **VERIFY PEER**. This setting is essential as without it you will know that your communication is encrypted, but you won't know who it is you're talking to.
 
Note: We use a wild-card certificate from DigiCert and hence the certificate chain is the same for both Sandbox and Production environments.
 


# Resources


## Account Resources

The following is a section of resources related to the platform's accounts.

* An "account identifier" in the system can be whatever is convenient for the platform as long as it adheres to the following rules:
 * Between 5 and 96 characters in length (inclusive).
 * Must be made up exclusively of: alphanumeric (A-Z, a-z, 0-9), underscore (_), dash (-), or plus (+).
 * Must NOT contain characters invalid in a URI. Examples include, but are not limited to: . , ! &. Spaces are not allowed either.
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


### Register a credit card to an account.

POST /raas/v1/cc_register

The input object is defined by [the cc_register JSON-Schema](cc_register.schema.json).

Example request/response:

	> POST /raas/v1/cc_register HTTP/1.1
	> Authorization: Basic ...
	> Host: integration-api.tangocard.com
	> Accept: */*
	> Content-Length: ...
	> Content-Type: application/json
	> 
	{
	  "customer": "CompanyA",
	  "account_identifier": "123456",
	  "client_ip": "55.44.33.22",
	  "credit_card": {
	    "number": "4111111111111111",
	    "security_code": "123",
	    "expiration": "2016-11",
	    "billing_address": {
	      "f_name": "John",
	      "l_name": "Doe",
	      "address": "1234 Fake St",
	      "city": "Springfield",
	      "state": "WA",
	      "zip": "99196",
	      "country": "USA",
	      "email": "test@example.com"
	    }
	  }
	}
	
	< HTTP/1.1 200 OK
	< Content-Type: application/json; charset=utf-8
	< Content-Length: ...
	<
	{
      "success": true,
      "cc_token": "152686945",
      "active_date": 1405444573
	}
	
- cc_token represents your payment method and cannot be retrieved later, *DO NOT LOSE IT*. 
- active_date is a unix timestamp (UTC) representing when this card completes the approval stage. It is not usable until this time. You can use [EpochConverter](http://www.epochconverter.com/) to decode the timestamp.
- In the sandbox environment the credit card registration delay is set to 5 minutes. The registration delay will be longer in the production environment. In both cases, the duration of the delay will be reflected in active_date. 
- For Sandbox testing you *MUST* use the test credit card number "4111111111111111", as reflected in the above example. Other fake credit card numbers and valid credit card numbers are not supported in the Sandbox environment.


### Fund a platform's account.

POST /raas/v1/cc_fund

The input object is defined by [the cc_fund JSON-Schema](cc_fund.schema.json).

Example request/response:

	> POST /raas/v1/cc_fund HTTP/1.1
	> Authorization: Basic ...
	> Host: integration-api.tangocard.com
	> Accept: */*
	> Content-Length: ...
	> Content-Type: application/json
	> 
	{
	  "customer": "CompanyA",
	  "account_identifier": "123456",
	  "amount": 100000,
	  "client_ip": "55.44.33.22",
	  "security_code": "123",
	  "cc_token": "152686945"
	}

	< HTTP/1.1 200 OK
	< Content-Type: application/json; charset=utf-8
	< Content-Length: ...
	<
	{
	  "success": true,
	  "fund_id": "RF11-22222222-33",
	  "amount" : 100000
	}


	
### Delete a credit card from an account.

POST /raas/v1/cc_unregister

The input object is defined by [the cc_unregister JSON-Schema](cc_unregister.schema.json).

Example request/response:

	> POST /raas/v1/cc_unregister HTTP/1.1
	> Authorization: Basic ...
	> Host: integration-api.tangocard.com
	> Accept: */*
	> Content-Length: ...
	> Content-Type: application/json
	> 
	{
	  "customer": "CompanyA",
	  "account_identifier": "123456",
	  "cc_token": "152686945"
	}

	< HTTP/1.1 200 OK
	< Content-Type: application/json; charset=utf-8
	< Content-Length: ...
	<
	{
      "success": true,
      "message": "This card is no longer present in the system."
	}



## Reward Resources


### Get the catalog of available items.

GET /raas/v1/rewards

* A negative-value "unit price" indicates that the item has a variable price. In this case the minimum and maximum fields should be consulted.
* Fixed value items also include "denomination", "currency_code", and "locale".  These values represent the face value currency, while "currency_type" relates to "unit_price"
* The "denomination" is the face value of the gift card and the "unit_price" is the cost of the gift card in USD.

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
						"available"    : true,
						"denomination" : 2500,
						"currency_code": "USD",
						"locale"       : "en_US"
					},
					{
						"description"  : "iTunes E-Gift Card $50",
						"sku"          : "APPL-E-5000-STD",
						"currency_type": "USD",
						"unit_price"   : 5000,
						"available"    : true,
						"denomination" : 5000,
						"currency_code": "USD",
						"locale"       : "en_US"
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
				"name"      : "John Doe",
				"email"     : "john.doe@example.com"
			},
    			"reward": {
      				"token"     : "55b95ad97f5b99.96804739",
      				"number"    : "7001204001834392312",
      				"pin"       : "861693"
    			}
		}
	}

For international variable skus, there will be an expiration date ("expiration") in the successful response, if applicable. The format for expiration date is ISO6801 standard date format, example: 2016-06-19 (Year-Month-Day).

*Expiration dates in email templates will appear in localized format, for example: 2016-06-19 could be 2016年6月19日. We are using this PHP library to format international dates: http://php.net/manual/en/class.intldateformatter.php

Example success response for international variable sku:

	< HTTP/1.1 201 Created
	< Content-Type: application/json; charset=utf-8
	< Location: https://integration-api.tangocard.com/raas/v1/orders/
	< Content-Length: 297
	< 
	{
		"success": true,
		"order": {
			"order_id"          : "115-07734196-29",
			"account_identifier": "123456",
			"customer"          : "CompanyA",
			"sku"               : "AMZUK-E-V-STD",
			"amount"            : 1550,
			"reward_message"    : "Test of reward response",
			"reward_subject"    : {
				"data"      : ""
				},
			"reward_from"       : "Jane Doe",
			"delivered_at"      : "2015-07-29T22:46:36+00:00",
			"recipient"         : {
				"name" : "John Doe",
				"email": "john.doe@example.com"
    	},
		"reward": {
			"token"        : "55b957d0c54b69.57249418",
			"number"       : "27VP-CJRLS6-HFV7",
			"expiration"   : "2025-07-29"
    			}
  		}
	}


### Resend an order.

The Resend functionality allows Tango Card RaaS API Platform partners to resend reward emails to the original recipient on demand. This may be useful if a recipient reports that they never received or cannot find a reward email. 

Format:

POST raas/v1/orders/{ORDER_NUMBER}/resend

RULES & IMPORTANT NOTES

1. Resend is not supported for emails originally sent prior to February 18, 2015

2. The target order (to be resent) must have been sent with the "send_reward" property set to "true" (the default value). This means Tango Card sent the original email. If it was “false” we cannot resend the email because we never sent the original. 

3. Orders can be resent once every 24 hours. This is reflected in the error message timestamp if a second attempt is made less than 24 hours after the last send.

4. Resend functionality does not have to be specific to a RaaS account. The Resend functionality occurs at the platform level of the RaaS integration. 

5. You will need the original order number in order to perform the resend. The Tango Card Order History call allows for a search of past orders and includes the order number for order result.

6. Orders can only be resent to the original recipient. If an administrator needs to verify reward information for a given order, consider using the Get Order Information call instead of the Resend an Order call.

7. Resending an email does not guarantee delivery. If the recipient does not receive an email due to spam filter settings, corporate firewalls, etc. resending an email will not get them their reward. The underlying problem will need to be determined and resolved. 


Example request/response:

   	> POST /raas/v1/orders/123-12345678-12/resend HTTP/1.1
    	> Authorization: Basic C0FFEEC0FFEEC0FFXXXXXXXX
    	> Host: integration-api.tangocard.com
    	> Accept: */*
    	> 

    	< HTTP/1.1 200 OK
    	< Content-Type: application/json; charset=utf-8
    	<
    	{
        	"success": true
    	}

Error Reponses:

Cannot send more than once every 24 hours
Resend functionality is limited to once every 24 hours per order. The 24 hour period starts at the last recorded sent time in UTC.

https://api.tangocard.com/raas/v1/orders/115-0112257739-30/resend

24-hr limit error response:

	{
		"success": false,
		"error_message": "Cannot send more than once every 24 hours (last send at 2015-02-18T20:17:31+00:00)"
	}
	
Order Not Found
Incorrect order ID or order not found in the system for this platform.

https://api.tangocard.com/raas/v1/orders/115-0112257739-32/resend

Order Not Found Error Response: 
	{
		"success": false,
		"error_message": "order not found"
	}

Unable To Send Reward
If Tango Card cannot resend the reward for a reason other than listed above we will provide a generic error. One example cause for this error would be if Tango Card was not configured to send the email in the original order (send_reward flag was set to “false”). 

Unable to Send Reward Response: 

	{
		“success”: false,
		“error_message”: "Unable to resend requested reward.”
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

* All responses will have a field called "success" which is a Boolean to denote high-level success/failure.



## Generic Errors

* The generic errors can be triggered by any (multiple) calls.
* Many errors will have a field called "error_message" which can be used to give more information as to why it failed (for debugging, not displaying to end-users).

Please ensure that the response body is read to get more information on an error condition. Some HTTP client libraries may choose to not read the response body in case of any errors (4xx or 5xx). 


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



### RAAS OC 1003

Error RAAS:OC:1003

This a catch-all system error for which more specific messaging has not yet been created.  If you encounter this error please contact sdk@tangocard.com and include your request and response JSON.


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
	


### Credit card registered

A credit card was registered.

* **HTTP Status Code:** 200 OK
* **Response Object:** 
	* success : (boolean)
	* cc_token : (string) A token needed to recall this card to fund an account.
	* active_date : (string) unix timestamp (UTC). When the card will be available for funding.

	
	
### Fund Created

An account was funded.

* **HTTP Status Code:** 200 OK
* **Response Object:** 
	* success : (boolean)
	* fund_id : (string) A reference id that Tango Card can use to lookup this fund.
	* amount : (integer) The amount that was funded.
	


### Credit card deleted

A credit card was un-registered from an account.

* **HTTP Status Code:** 200 OK
* **Response Object:** 
	* success : (boolean)
	* message: (string) "This card is no longer present in the system."

	
	
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
		* sku : (string) The reward that was purchased.
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
	* offset : (int) Defaults to 0 if not provided
	* limit : (int) Defaults to 100 if not provided (and can't be more than 100)
	* total_count : (int) Total number of all the orders placed against the given account (>= result_count)
	* result_count : (int) Count of orders returned (<= total_count)
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
			* min_price : (integer) For variable price rewards this denotes the minimum price available. Field may not be present for non-variable.
			* max_price : (integer) For variable price rewards this denotes the maximum price available. Field may not be present for non-variable.
			* denomination : (integer) For fixed price rewards, representing the face value. Field will not be present for variable.
			* currency_code : (string) For fixed price rewards, this is the 3-letter currency code of the denomination.  Field will not be present for variable.
			* locale : (string) For fixed price rewards, this denotes the main region/culture of the denomination. (e.g., "en_US") This is provided for ease of regional formatting.  Field will not be present for variable.
		


[![githalytics.com alpha](https://cruel-carlota.pagodabox.com/0d99126c1cf8686391099d328e3c2363 "githalytics.com")](http://githalytics.com/github.com/tangocarddev)

<img height='1' width='1' border='0' src='Https://www.clickmeter.com/conversion.aspx?id=DA1A71157B29421C874CA3B6AEB6F02E&val=0.00&param=empty&com=0.00' />
