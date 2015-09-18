# Tango Card - Rewards as a Service&trade;

# RaaS&reg; API - Gift Card API

## Table of Contents

- [RaaS API Introduction - Version 1.1](#raas-api---version-1.1)
	- [What is RaaS](#what-is-the-raas-api)
	- [What changed in v1.1](#what-changed-in-v11)
	- [Platform Setup](#platform-setup-and-authentication)
		- [Platform Configurations](#platform-configurations)
		- [Authentication](#authentication)
		- [Sandbox Credentials](#sandbox-credentials)
		- [Production Credentials](#production-credentials)
		- [System Notes](#system-notes)
		- [RaaS Best Practices](#raas-best-practices)
		- [SSL/TLS](#ssltls)
- [Methods](#methods)
	- [Create Account](#create-account)
	- [Get Account Information](#get-account-information)
	- [Register a Credit Card](#register-a-credit-card)
	- [Delete a Credit Card](#delete-a-credit-card)
	- [Fund an Account](#fund-an-account)
	- [Get Reward Catalog](#get-reward-catalog)
	- [Place an Order](#place-an-order)
	- [Resend an Order](#resend-an-order)
	- [Get Order Information](#get-order-information)
	- [Get Order History](#get-order-history)
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
	- [Method Errors](#method-errors)
		- [Fund Create - Payment Error (Credit Card)](#fund-create---payment-error-credit-card)
		- [Order create - Insufficient Funds](#order-create---insufficient-funds)
	- [Method Successes](#method-successes)
		- [Account Created](#account-created)
		- [Account](#account)
		- [Credit card registered](#credit-card-registered)
		- [Fund Created](#fund-created)
		- [Credit card deleted](#credit-card-deleted)
		- [Order Created](#order-created)
		- [Order List](#order-list)
		- [Order](#order)
		- [Rewards List](#rewards-list)

## RaaS API Version 1.1 Introduction

## What is the RaaS&reg; API?

With our RESTful RaaS API you can integrate a global reward or incentive program into your platform. 

Our API is made to support multiple account structures. Following [Platform Setup](#platform-setup) and [Authentication](#authentication), the following methods are available:

* CREATE AN ACCOUNT - Versatility to create multiple account structures to support many use cases
* GET ACCOUNT INFORMATION - Call up information about accounts in your platform
* REGISTER A CREDIT CARD - Securely save up to 10 credit cards to accounts with our third-party payment partner
* FUND AN ACCOUNT - Call up your saved credit card(s) and use them to fund accounts
* DELETE A CREDIT CARD - Remove saved credit cards for good
* GET THE CATALOG - Call up our catalog of e-gift cards, donations, prepaid visas, and bitcoin e-gift cards
* PLACE AN ORDER - Place an order for a reward. Choose whether Tango Card delivers the reward via email
* RESEND AN ORDER - Simply resend an order that has already been placed
* GET ORDER INFORMATION - Call up order information about an individual order
* GET ORDER HISTORY - Call up a list of orders for an account

## What Changed in v1.1

The goal of the RaaS API v1.1 is to support global reward and incentive programs in localized currencies and languages.

### Summary of Key Changes

Get Reward Catalog Method Changes
* Removed currency_type (all items) because it was confusing. It was always USD and referred to the currency type that is deducted from the account not the face value of the reward.
* Removed unit_price (all items) because the cost of international items will update at least daily. Customers are now directed to calculate xrates against denomination to estimate the current cost.  
* Removed locale (fixed items) due to limitations. Not currently a use case to isolate products by different cultures within a country and cannot designate rewards as being available for more than a single country.
* Added type to indicate whether the type is “reward” or “npo” or possibly some other type in the future.
* Added denomination (fixed items) consistently to catalog call for the face value. 
* Added countries (all items) to catalog call– Replaces locale. Two digit country code for reward redemption. Now able to provide multiple countries in an array (e.g. SWIFT Visa Euro)
* Added is_variable (all items) to catalog call– With the removal of unit_price we needed to add a way for customers to parse by whether a reward is variable or fixed. 
* Added xrates and exchange rates disclaimer language to catalog call – provides a list of exchange rates by country, updated on a daily basis. 

Order Response Changes
* Removed amount (all items) – see above 
* Removed "[data]": echo response. (applies to all item types). Fixed empty reward_subject response.
* Added denomination / value / currency_code (all items) – See above
* Added amount_charged (all items) to reflect the amount (in USD) deducted from the account. This eliminates the confusion previously associated with “amount”

Order Details & Order History Changes
* Added denomination / value / currency_code  - See above
* Added amount_charged - See above

API Methods with no changes between v1.0 and v1.1
* Platform set up
* Create Account (/accounts)
* Get Account Information (/"account-id")
* Register a Credit Card (/cc_register)
* Fund Account (/cc_fund)
* Delete a Credit Card (/cc_unregister)
* Order Request (/orders)
* Resend an Order (/resend)

Note: v1.1 order details and order history responses will include transactions placed on v1.0. For Order History, the "denomination" value will appear as null for orders placed in v1.0.


## Platform Setup and Authentication

### Platform Configurations

The RaaS API is capable of supporting rewards and incentives for multiple platform structures.

One example is a 1 to 1 structure, wherein there is one platform, one company, and one account purchasing rewards:

![alt text](https://www.tangocard.com/wp-content/themes/tc/uploads/Tango-Card-RaaS-API-1-to-1-Accounts2.png "RaaS API 1 to 1 Account Structure")


A second example is a structure with multiple companies, each with their own account or accounts to purchase rewards:

![alt text](https://www.tangocard.com/wp-content/themes/tc/uploads/Tango-Card-RaaS-API-Multiple-Companies-and-Accounts21.png "RaaS API Multiple Companies and Accounts")

### Test drive the RaaS API

You can test drive the RaaS API without writing a single line of code!  

Using the [RaaS API Test Console](https://integration-www.tangocard.com/raas_api_console/), you can easily go through each method supported in our API and see the requests and the responses for each of these resources. When you are comfortable with the concepts you can begin coding by requesting Sandbox credentials for our test site. Then, if you run into problems during the coding phase you can come back to our console and compare your JSON to our requests and responses.

### Sandbox credentials

Please email sdk@tangocard.com to receive your own credentials for the RaaS API Sandbox environment. 
The endpoint for the RESTful interface on the Sandbox environment is https://sandbox.tangocard.com/raas/v1.1/ .

You may also use these general credentials to get started - they are the same credentials that are pre-loaded in the RaaS API Test Console.

Platform Name: TangoTest

Platform Key: 5xItr3dMDlEWAa9S4s7vYh7kQ01d5SFePPUoZZiK/vMfbo3A5BvJLAmD4tI=

Platform Authorization: 
Basic VGFuZ29UZXN0OjV4SXRyM2RNRGxFV0FhOVM0czd2WWg3a1EwMWQ1U0ZlUFBVb1paaUsvdk1mYm8zQTVCdkpMQW1ENHRJPQ==

An important note about *Test Codes*: Rewards delivered from Tango Card's Sandbox environment contain sample codes and have no monetary or redeemable value. Additionally, please note that some Amazon.com denominations, like $20, may fail in the Sandbox environment. This is intentional behavior for the purpose of simulating an API error. If you encounter an error when testing an Amazon.com code, please try another denomination. Be assured that Amazon.com orders work consistently in the production environment. 

When you have completed your development and are ready for production testing / launch you can request production credentials.


### Production credentials

Once you are ready to move to production, you need to agree to the [RaaS API Terms & Conditions](https://www.tangocard.com/tango_card_agreements/#ETOS). 
Once you agree to the Terms & Conditions, we will create credentials on the Production environment and follow up with you. 
The endpoint for the RESTful interface on the Production environment is https://api.tangocard.com/raas/v1.1/


### RaaS API credentials versus TangoCard.com user credentials

Our now-deprecated legacy SDKs used username and password credentials (like those used for user accounts on our website). If you are upgrading to the RaaS API from one of our legacy SDKs, please note that our RaaS API uses a Platform Name and Platform Key that need to be created for your RaaS API account and you can not mix the credentials for TangoCard.com site with the credentials for the RaaS API.

### System Notes

* [JSON](http://en.wikipedia.org/wiki/JSON) is the only currently-supported content type for inputs or outputs. It is not required that the requests contain an Accept header, but if they do it should contain "application/json" or "*/*.
* All calls require the platform's authentication. Authentication uses [HTTP Basic auth](http://en.wikipedia.org/wiki/Basic_access_authentication) with the platform name as the username and the platform access key as the password. These will be assigned by Tango Card.
* All dates will/must be represented in a format that is compatible with [ISO 8601](http://en.wikipedia.org/wiki/ISO_8601) (for example: *2013-02-21T20:37:07-07:00*).
* Platform will be responsible for handling access to it's accounts (e.g. not allowing ACC1 to place an order using ACC2's account)... requests from authenticated platforms against their accounts will be implicitly trusted.
* Whenever money is concerned, it is in cents (e.g. $4 = 400).
* Fields in the RaaS API response objects may be added at any time. This means that whatever is used to demarshall the JSON responses into native data types needs to be able to handle unknown fields without failing.
* All responses may include a `system_message` field. This is a note from TangoCard to the platforms and not intended for end users. This will notify of things like planned outages and should be monitored.

### RaaS Best Practices

Retries - Network vagaries, infrastructure and supplier factors mean occasional network errors are inevitable and must be planned for. For this reason we recommend that you build an "exponential back off" or similar retry algorithm in which the timeout value for retry increases after each unsuccessful attempt. Exponential retries are well-documented elsewhere and beyond the scope of this document.

### SSL/TLS

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



# Methods


## Account Methods

The following is a section of methods related to the platform's accounts.

* An "account identifier" in the system can be whatever is convenient for the platform as long as it adheres to the following rules:
 * Between 5 and 96 characters in length (inclusive).
 * Must be made up exclusively of: alphanumeric (A-Z, a-z, 0-9), underscore (_), dash (-), or plus (+).
 * Must NOT contain characters invalid in a URI. Examples include, but are not limited to: . , ! &. Spaces are not allowed either. A full account of character rules can be found [here](#https://tools.ietf.org/html/rfc3986#section-2.2).
 * Must be unique to the company.
* A "customer" is a mechanism for denoting a company, department, etc an account belongs to. 



### Create Account

POST /raas/v1.1/accounts

The input object is defined by [this JSON-Schema](account_create.schema.json).

Example request/response:

	> POST /raas/v1.1/accounts HTTP/1.1
	> Authorization: Basic VGFuF29UZXN0OjV4SXRyM2RNRGxFV0FhOHM0czd2WWg3a1EwMWQ1U0ZlUFCVb1paaUsvdk1mYm8zQSVCdkpMQW1ENHRJPP==
	> Host: integration-api.tangocard.com
	> Accept: */*
	> Content-Length: 72
	> Content-Type: application/json
	>
	{
		"customer":"CompanyA",
		"identifier":"Account1",
		"email":"email@companya.com"
	}


	< HTTP/1.1 201 Created
	< Content-Type: application/json; charset=utf-8
	< Location: https://integration-api.tangocard.com/raas/v1.1/accounts/123456
	< Content-Length: 118
	< 
	{
  		"success": true,
  		"account": {
    			"identifier": "Account1",
    			"email": "email@companya.com",
    			"customer": "CompanyA",
    			"available_balance": 0
  		}
	}





### Get Account Information

GET /raas/v1.1/accounts/{customer}/{account_identifier}

Example request/response:

	> GET /raas/v1.1/accounts/CompanyA/Account1 HTTP/1.1
	> Authorization: Basic VGFuF29UZXN0OjV4SXRyM2RNRGxFV0FhOHM0czd2WWg3a1EwMWQ1U0ZlUFCVb1paaUsvdk1mYm8zQSVCdkpMQW1ENHRJPP==
	> Host: integration-api.tangocard.com
	> Accept: */*
	> 


	< HTTP/1.1 200 OK
	< Content-Type: application/json; charset=utf-8
	< Content-Length: 112
	< 
	{
		"identifier":"Account1",
		"email":"scotty@tangocard.com",
		"customer":"CompanyA",
		"available_balance":0
	}
 
## Fund Methods


### Register a Credit Card

POST /raas/v1.1/cc_register

The input object is defined by [the cc_register JSON-Schema](cc_register.schema.json).

Example request/response:

	> POST /raas/v1.1/cc_register HTTP/1.1
	> Authorization: Basic ...
	> Host: integration-api.tangocard.com
	> Accept: */*
	> Content-Length: ...
	> Content-Type: application/json
	> 
	{
  		"customer": "CompanyA",
  		"account_identifier": "Account1",
  		"client_ip": "127.0.0.1",
  		"credit_card": {
    			"number": "4111111111111111",
    			"security_code": "123",
    			"expiration": "2016-01",
    			"billing_address": {
      				"f_name": "FName",
      				"l_name": "LName",
      				"address": "Address",
      				"city": "Seattle",
      				"state": "WA",
      				"zip": "98116",
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
  		"cc_token": "33465088",
  		"active_date": 1442434992
	}
	
- cc_token represents your payment method and cannot be retrieved later, *DO NOT LOSE IT*. 
- active_date is a unix timestamp (UTC) representing when this card completes the approval stage. It is not usable until this time. You can use [EpochConverter](http://www.epochconverter.com/) to decode the timestamp.
- In the sandbox environment the credit card registration delay is set to 5 minutes. The registration delay will be longer in the production environment. In both cases, the duration of the delay will be reflected in active_date. 
- For Sandbox testing you *MUST* use the test credit card number "4111111111111111", as reflected in the above example. Other fake credit card numbers and valid credit card numbers are not supported in the Sandbox environment.


### Delete a Credit Card

POST /raas/v1.1/cc_unregister

The input object is defined by [the cc_unregister JSON-Schema](cc_unregister.schema.json).

Example request/response:

	> POST /raas/v1.1/cc_unregister HTTP/1.1
	> Authorization: Basic ...
	> Host: integration-api.tangocard.com
	> Accept: */*
	> Content-Length: ...
	> Content-Type: application/json
	> 
	{
  		"customer": "CompanyA",
  		"account_identifier": "Account1",
  		"cc_token": "33465088"
	}

	< HTTP/1.1 200 OK
	< Content-Type: application/json; charset=utf-8
	< Content-Length: ...
	<
	{
  		"success": true,
  		"message": "This card is no longer present in the system."
	}


### Fund an Account.

POST /raas/v1.1/cc_fund

The input object is defined by [the cc_fund JSON-Schema](cc_fund.schema.json).

Example request/response:

	> POST /raas/v1.1/cc_fund HTTP/1.1
	> Authorization: Basic ...
	> Host: integration-api.tangocard.com
	> Accept: */*
	> Content-Length: ...
	> Content-Type: application/json
	> 
	{
  		"customer": "CompanyA",
  		"account_identifier": "Account1",
  		"amount": 1000,
  		"client_ip": "127.0.0.1",
  		"cc_token": "33465088",
  		"security_code": "123"
	}

	< HTTP/1.1 200 OK
	< Content-Type: application/json; charset=utf-8
	< Content-Length: ...
	<
	{
  		"success": true,
  		"fund_id": "115-0934-16",
  		"amount": 1000
	}



## Reward Methods


### Get Reward Catalog

GET /raas/v1.1/rewards

Response Notes
* "type" denotes whether the "brand" (catalog item) is a "reward" (e-gift card or prepaid card) or a "npo" (non-profit donation)
* "description" is the brand approved name of the reward
* "sku" is the unique ID to use when placing an order
* A variable range of values means that a brand can be purchased for any denomination between a minimum value and a maximum value.
* "is-variable" denotes whether a brand has a variable range of values ("true") or has fixed denominations to choose from ("false").
* If a brand is variable, the range is defined, in cents, by "min_price" and "max_price"
* If a brand is fixed, the face value of the brand is defined, in cents, by "denomination"
* "countries" uses a two digit country code to define the languages the brand is available in. "countries" is an array.
* "xrates" is a list of international exchange rates to USD. Tango Card updates the exchange rates for non-US items at least once a day. As part of the Get Rewards Catalog method, the API returns the exchange rates currently loaded into our system.

Example request/response(s) below. There are four different example responses: US rewards with fixed denominations, US rewards with variable denominations, international fixed, and international variable.

Example request:

	> GET /raas/v1.1/rewards HTTP/1.1
	> Authorization: Basic VGFuF29UZXN0OjV4SXRyM2RNRGxFV0FhOHM0czd2WWg3a1EwMWQ1U0ZlUFCVb1paaUsvdk1mYm8zQSVCdkpMQW1ENHRJPP==
	> Host: integration-api.tangocard.com
	> Accept: */*
	>

Example response, US fixed denomination:
	
	< HTTP/1.1 200 OK
	< Content-Type: application/json; charset=utf-8
	< 
	{
  	"success": true,
  	"brands": [
    		{
      		"description": "Chipotle",
      		"image_url": "https://d54ks1x7dxslx.cloudfront.net/graphics/item-images/chipotle-gift-card.png",
      		"rewards": [
        	{
          		"type": "reward",
          		"description": "Chipotle Gift Card $5",
          		"sku": "CHIP-E-500-STD",
          		"is_variable": false,
          		"denomination": "500",
          		"currency_code": "USD",
          		"available": true,
          		"countries": [
            			"US"
          		]
        	},
        	{
          		"type": "reward",
          		"description": "Chipotle Gift Card $10",
          		"sku": "CHIP-E-1000-STD",
        		"is_variable": false,
          		"denomination": "1000",
          		"currency_code": "USD",
          		"available": true,
          		"countries": [
            			"US"
          		]
        	},
        	{
          		"type": "reward",
          		"description": "Chipotle Gift Card $25",
          		"sku": "CHIP-E-2500-STD",
          		"is_variable": false,
          		"denomination": "2500",
          		"currency_code": "USD",
          		"available": true,
          		"countries": [
           			"US"
          		]
        	},
        	{
          		"type": "reward",
          		"description": "Chipotle Gift Card $50",
          		"sku": "CHIP-E-5000-STD",
          		"is_variable": false,
          		"denomination": "5000",
          		"currency_code": "USD",
          		"available": true,
          		"countries": [
            			"US"
          			]
        		}
      		]
    	},

Example response, US variable denomination:

	{
      		"description": "Tango Card",
      		"image_url": "https://d54ks1x7dxslx.cloudfront.net/graphics/item-images/tango-card-gift-card.png",
      		"rewards": [
        	{
          		"type": "reward",
          		"description": "Tango Card E-Custom",
          		"sku": "TNGO-E-V-STD",
          		"is_variable": true,
          		"min_price": 1,
          		"max_price": 100000,
          		"currency_code": "USD",
          		"available": true,
          		"countries": [
            			"US"
          			]
        		}
      		]
    	},

Example response, international fixed denomination:

	{
      		"description": "iTunes Australia",
      		"image_url": "https://d54ks1x7dxslx.cloudfront.net/graphics/item-images/itunes-australia-gift-card.png",
      		"rewards": [
        	{
          		"type": "reward",
          		"description": "iTunes Australia Gift Card AUD 10",
          		"sku": "APAU-E-1000-STD",
          		"is_variable": false,
          		"denomination": "1000",
          		"currency_code": "AUD",
          		"available": true,
          		"countries": [
            			"AU"
          		]
        	},
        	{
          		"type": "reward",
        	 	"description": "iTunes Australia Gift Card AUD 15",
          		"sku": "APAU-E-1500-STD",
          		"is_variable": false,
          		"denomination": "1500",
          		"currency_code": "AUD",
          		"available": true,
          		"countries": [
            			"AU"
          		]
        	},
        	{
          		"type": "reward",
          		"description": "iTunes Australia Gift Card AUD 25",
          		"sku": "APAU-E-2500-STD",
          		"is_variable": false,
          		"denomination": "2500",
          		"currency_code": "AUD",
          		"available": true,
          		"countries": [
            			"AU"
          		]
        	},
        	{
         		"type": "reward",
          		"description": "iTunes Australia Gift Card AUD 50",
          		"sku": "APAU-E-5000-STD",
          		"is_variable": false,
          		"denomination": "5000",
          		"currency_code": "AUD",
          		"available": true,
          		"countries": [
            			"AU"
          			]
        		}
      		]
    	},

Example response, international variable denomination:

	{
      		"description": "Amazon.ca*",
      		"image_url": "https://d54ks1x7dxslx.cloudfront.net/graphics/item-images/amazon-ca-gift-card-custom.png",
      		"rewards": [
        	{
          		"type": "reward",
          		"description": "Amazon.ca Gift Certificate (Custom)",
          		"sku": "AMZCA-E-V-STD",
          		"is_variable": true,
          		"min_price": 1,
          		"max_price": 200000,
          		"currency_code": "CAD",
          		"available": true,
          		"countries": [
            			"CA"
          			]
        		}
      		]
    	},

Example response, daily exchange rates:

		"xrates":{
			"disclaimer": "Exchange rates are provided for estimation. Actual rates at time of order may vary… etc. ",
			"timestamp": 1439402461,
			"rates": [
				{
					"AUD": 1.356359,
					"CAD": 1.30066,
					"CNY": 6.373144,
					"EUR": 0.895154,
					"GBP": 0.639733,
					"JPY": 124.246
				}
			]
		}
	}
		



## Order Methods



### Place an Order

POST /raas/v1.1/orders

The input object is defined by [this JSON-Schema](order_create.schema.json).

Example request/response:

	> POST /raas/v1.1/orders HTTP/1.1
	> Authorization: Basic VGFuF29UZXN0OjV4SXRyM2RNRGxFV0FhOHM0czd2WWg3a1EwMWQ1U0ZlUFCVb1paaUsvdk1mYm8zQSVCdkpMQW1ENHRJPP==
	> Host: integration-api.tangocard.com
	> Accept: */*
	> Content-Length: 192
	> Content-Type: application/json
	> 
	{
  		"customer": "CompanyA",
  		"account_identifier": "Account1",
  		"campaign": "emailtemplate1",
  		"recipient": {
    			"name": "John To",
    			"email": "johnto@email.com"
  			},
  		"sku": "TNGO-E-V-STD",
  		"amount": 1000,
  		"reward_from": "Joan From",
  		"reward_subject": "Here is your reward!",
  		"reward_message": "Way to go, John! Thanks!",
  		"send_reward": true
	}
	
	
	< HTTP/1.1 201 Created
	< Content-Type: application/json; charset=utf-8
	< Location: https://integration-api.tangocard.com/raas/v1.1/orders/
	< Content-Length: 297
	< 
	{
  		"success": true,
  		"order": {
    			"order_id": "115-0992-17",
    			"account_identifier": "Account1",
    			"customer": "CompanyA",
    			"sku": "TNGO-E-V-STD",
    			"denomination": {
      				"value": 1000,
      				"currency_code": "USD"
    				},
    			"amount_charged": {
      				"value": 1000,
      				"currency_code": "USD"
    				},
    			"reward_message": "Way to go, John! Thanks!",
    			"reward_subject": "Here is your reward!",
    			"reward_from": "Joan From",
    			"delivered_at": "2015-09-17T16:49:39+00:00",
    			"recipient": {
      				"name": "John To",
      				"email": "johnto@email.com"
    				},
    			"reward": {
      				"token": "55faef23ec33a2.36972978",
      				"number": "7001004001338100213",
      				"pin": "345038"
    			}
  		}
	}

For international variable SKUs, there will be an expiration date ("expiration") in the successful response, if applicable. The format for expiration date is ISO6801 standard date format, example: 2016-06-19 (Year-Month-Day).

*Expiration dates in email templates will appear in localized format, for example: 2016-06-19 could be 2016年6月19日. We are using this PHP library to format international dates: http://php.net/manual/en/class.intldateformatter.php

Example success response for international variable SKU:

	< HTTP/1.1 201 Created
	< Content-Type: application/json; charset=utf-8
	< Location: https://integration-api.tangocard.com/raas/v1.1/orders/
	< Content-Length: 297
	< 
	{
  		"success": true,
  		"order": {
    			"order_id": "115-09104-17",
    			"account_identifier": "Account2",
    			"customer": "CompanyA",
    			"sku": "AMZDE-E-V-STD",
    			"denomination": {
      				"value": 500,
      				"currency_code": "EUR"
    			},
    			"amount_charged": {
      				"value": 564,
      				"currency_code": "USD"
    			},
    			"reward_message": "Way to go, John! Thank you!",
			"reward_subject": "Here is your reward!",
    			"reward_from": "Jerry From",
    			"delivered_at": "2015-09-17T23:51:08+00:00",
    			"recipient": {
      				"name": "Harry To",
      				"email": "harryto@email.com"
    			},
    			"reward": {
      				"token": "55fb51f14d3f85.97417356",
      				"number": "8B5M-8P8BTG-U3HB",
      				"expiration": "2025-09-18"
    			}
  		}
	}


### Resend an Order

The Resend functionality allows Tango Card RaaS API Platform partners to resend reward emails to the original recipient on demand. This may be useful if a recipient reports that they never received or cannot find a reward email. 

Format:

POST raas/v1.1/orders/{ORDER_NUMBER}/resend

RULES & IMPORTANT NOTES

1. Resend is not supported for emails originally sent prior to February 18, 2015.

2. The target order (to be resent) must have been sent with the "send_reward" property set to "true" (the default value). This means Tango Card sent the original email. If it was “false” we cannot resend the email because we never sent the original. 

3. Orders can be resent once every 24 hours. This is reflected in the error message timestamp if a second attempt is made less than 24 hours after the last send.

4. Resend functionality does not have to be specific to a RaaS account. The Resend functionality occurs at the platform level of the RaaS integration. 

5. You will need the original order number in order to perform the resend. The Tango Card Order History call allows for a search of past orders and includes the order number for order result.

6. Orders can only be resent to the original recipient. If an administrator needs to verify reward information for a given order, consider using the Get Order Information call instead of the Resend an Order call.

7. Resending an email does not guarantee delivery. If the recipient does not receive an email due to spam filter settings, corporate firewalls, etc. resending an email will not get them their reward. The underlying problem will need to be determined and resolved. 


Example request/response:

   	> POST /raas/v1.1/orders/123-12345678-12/resend HTTP/1.1
    	> Authorization: Basic VGFuF29UZXN0OjV4SXRyM2RNRGxFV0FhOHM0czd2WWg3a1EwMWQ1U0ZlUFCVb1paaUsvdk1mYm8zQSVCdkpMQW1ENHRJPP==
    	> Host: integration-api.tangocard.com
    	> Accept: */*
    	> 

    	< HTTP/1.1 200 OK
    	< Content-Type: application/json; charset=utf-8
    	<
    	{
        	"success": true
    	}

Error Responses:

Cannot send more than once every 24 hours
Resend functionality is limited to once every 24 hours per order. The 24 hour period starts at the last recorded sent time in UTC.

https://api.tangocard.com/raas/v1.1/orders/115-0112257739-30/resend

24-hr limit error response:

	{
		"success": false,
		"error_message": "Cannot send more than once every 24 hours (last send at 2015-02-18T20:17:31+00:00)"
	}
	
Order Not Found
Incorrect order ID or order not found in the system for this platform.

https://api.tangocard.com/raas/v1.1/orders/115-0112257739-32/resend

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
	
	
	

### Get Order Information

GET /raas/v1.1/orders/{order_id}

Example request/response:

	> GET /raas/v1.1/orders//115-0999-17 HTTP/1.1
	> Authorization: Basic VGFuF29UZXN0OjV4SXRyM2RNRGxFV0FhOHM0czd2WWg3a1EwMWQ1U0ZlUFCVb1paaUsvdk1mYm8zQSVCdkpMQW1ENHRJPP==
	> Host: integration-api.tangocard.com
	> Accept: */*
	> 


	< HTTP/1.1 200 OK
	< Content-Type: application/json; charset=utf-8
	< Content-Length: 297
	< 
	{
  		"success": true,
  		"order": {
    			"order_id": "115-0999-17",
    			"account_identifier": "Account2",
    			"customer": "CompanyA",
    			"sku": "AMZN-E-V-STD",
    			"denomination": {
      				"value": 2500,
      				"currency_code": "USD"
    				},
    			"amount_charged": {
      				"value": 2500,
      				"currency_code": "USD"
    				},
    			"reward_message": "You're awesome! Keep it up!",
    			"reward_subject": "Here is your reward!",
    			"reward_from": "Jane From",
    			"delivered_at": "2015-09-17T17:19:31+00:00",
    			"recipient": {
      				"name": "Jake To",
      				"email": "jaketo@email.com"
    				},
    			"reward": {
      				"token": "55faf623e97603.25696715",
      				"number": "AR3E-T74NRL-VVQQ"
    			}
  		}
	}



### Get Order History

GET /raas/v1.1/orders{?start_date,end_date,offset,limit,customer,account_identifier}

* All inputs are optional.
 * start_date / end_date : datetimes (ISO 8601) between which to search
 * offset : How far into the resultset to start.
 * limit : How many to return (maximum of 100).
 * account_identifier : Filter to only a single platform account.
 * customer : Filter only to a single customer.

Example request/response:

	> GET /raas/v1.1/orders?customer=CompanyA&account_identifier=Account2&offset=0&limit=10&start_date=2015-09-01T00:00:00&end_date=2015-10-01T23:59:59 HTTP/1.1
	> Authorization: Basic VGFuF29UZXN0OjV4SXRyM2RNRGxFV0FhOHM0czd2WWg3a1EwMWQ1U0ZlUFCVb1paaUsvdk1mYm8zQSVCdkpMQW1ENHRJPP==
	> Host: integration-api.tangocard.com
	> Accept: */*
	> 
	
	
	< HTTP/1.1 200 OK
	< Content-Type: application/json; charset=utf-8
	< Content-Length: 603
	< 
	{
  		"success": true,
  		"offset": 0,
  		"limit": 10,
  		"start_date": "2015-09-01T00:00:00",
  		"end_date": "2015-10-01T23:59:59",
  		"result_count": 4,
  		"total_count": 4,
  		"orders": [
    		{
      			"order_id": "115-0998-17",
			"account_identifier": "Account2",
      			"customer": "CompanyA",
      			"sku": "TNGO-E-V-STD",
      			"denomination": {
        			"value": 100000,
        			"currency_code": "USD"
      				},
      			"amount_charged": {
        			"value": 100000,
        			"currency_code": "USD"
      				},
      			"reward_message": null,
      			"reward_subject": null,
      			"reward_from": null,
      			"delivered_at": "2015-09-17T17:19:22+00:00",
      			"recipient": {
        			"name": "",
        			"email": "AUTOBIND"
      			}
    		},
    		{
      			"order_id": "115-0999-17",
      			"account_identifier": "Account2",
      			"customer": "CompanyA",
      			"sku": "AMZN-E-V-STD",
      			"denomination": {
        			"value": 2500,
        			"currency_code": "USD"
      				},
      			"amount_charged": {
        			"value": 2500,
        			"currency_code": "USD"
      				},
      			"reward_message": "You're awesome! Keep it up!",
      			"reward_subject": "Here is your reward!",
      			"reward_from": "Jane From",
      			"delivered_at": "2015-09-17T17:19:31+00:00",
      			"recipient": {
        			"name": "Jake To",
        			"email": "jaketo@email.com"
      			}
    		},
    		{
      			"order_id": "115-09100-17",
      			"account_identifier": "Account2",
      			"customer": "CompanyA",
      			"sku": "TNGO-E-V-STD",
      			"denomination": {
        			"value": 1000,
        			"currency_code": "USD"
      				},
      			"amount_charged": {
        			"value": 1000,
        			"currency_code": "USD"
      				},
      			"reward_message": "Way to go, John! Thank you!",
      			"reward_subject": "Here is your reward!",
      			"reward_from": "Joan From",
      			"delivered_at": "2015-09-17T17:20:12+00:00",
      			"recipient": {
        			"name": "John To",
        			"email": "johnto@email.com"
      			}
    		},
    		{
      			"order_id": "115-09101-17",
      			"account_identifier": "Account2",
      			"customer": "CompanyA",
      			"sku": "AMZCA-E-V-STD",
      			"denomination": {
        			"value": 1500,
        			"currency_code": "CAD"
      				},
      			"amount_charged": {
        			"value": 1133,
        			"currency_code": "USD"
      				},
      			"reward_message": "Way to go, John! Thank you!",
      			"reward_subject": "Here is your reward!",
      			"reward_from": "Jerry From",
      			"delivered_at": "2015-09-17T17:21:23+00:00",
      			"recipient": {
        			"name": "Harry To",
        			"email": "harryto@email.com"
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


## Method Errors



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



## Method Successes



### Account Created

An account was created.

* **HTTP Status Code:** 201 Created
* **HTTP Headers:** 
	* Location: https://integration-api.tangocard.com/raas/v1.1/accounts/{customer}/{account_identifier}
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
	* Location: https://integration-api.tangocard.com/raas/v1.1/{order_id}
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
			* unit_price : (integer) The price of the reward (negative values denote variable prices).
			* available : (boolean) Whether the reward is currently available.
			* min_price : (integer) For variable price rewards this denotes the minimum price available. Field may not be present for non-variable.
			* max_price : (integer) For variable price rewards this denotes the maximum price available. Field may not be present for non-variable.
			* denomination : (integer) For fixed price rewards, representing the face value. Field will not be present for variable.
			* currency_code : (string) For fixed price rewards, this is the 3-letter currency code of the denomination.  Field will not be present for variable.
		

