- [Request processing](#request-processing)
- [Public data](#public-data)
  - [Common Enums](#common-enums)
    - [Order Types](#order-types)
    - [Order Statuses](#order-statuses)
    - [Supported blockchains](#supported-blockchains)
    - [Deposit Types](#deposit-types)
    - [Withdrawal Types](#withdrawal-types)
    - [Exchange Types](#exchange-types)
  - [Getting active currencies list](#getting-active-currencies-list)
  - [Getting active pairs list](#getting-active-pairs-list)
  - [Getting prices](#getting-prices)
- [User data](#user-data)
  - [Creating account](#creating-account)
  - [Account types](#account-types)
  - [Authorization types](#authorization-types)
  - [JWT authorization](#jwt-authorization)
  - [Authorization by request signature](#authorization-by-request-signature)
  - [Getting balance](#getting-balance)
- [Orders](#orders)
  - [Getting order details](#getting-order-details)
  - [Getting order history](#getting-order-history)
  - [Callback notification](#callback-notification)
  - [Deposit order](#deposit-order)
    - [Operation scheme](#deposit-order-operation-scheme)
    - [Getting settings](#getting-deposit-order-settings)
    - [Getting deposit address](#getting-deposit-address)
    - [Order details for callback notification](#deposit-order-details-for-callback-notification)
    - [Getting deposit order details](#getting-deposit-order-details)
  - [Withdrawal order](#withdrawal-order)
    - [Operation scheme](#withdrawal-order-operation-scheme)
    - [Getting settings](#getting-withdrawal-order-settings)
    - [Creating order](#creating-withdrawal-order)
    - [Cancelling order](#cancelling-withdrawal-order)
    - [Repeating order](#repeating-withdrawal-order)
    - [Getting details](#getting-withdrawal-order-details)
    - [Order details for callback notification](#withdrawal-order-details-for-callback-notification) 
  - [Internal movement order](#internal-movement-order)
    - [Operation scheme](#internal-movement-order-operation-scheme)  
    - [Getting settings](#getting-internal-movement-order-settings)
    - [Creating order](#creating-internal-movement-order)
    - [Repeating order](#repeating-internal-movement-order)
    - [Getting order details](#getting-internal-movement-order-details)
    - [Order details for callback notification](#internal-movement-order-details-for-callback-notification)
  - [Invoice order](#invoice-order) 
    - [Operation scheme](#invoice-order-operation-scheme)
    - [Getting settings](#getting-invoice-order-settings)
    - [Creating invoice](#creating-invoice)
    - [Getting details](#getting-invoice-details)
    - [Order details for callback notification](#invoice-order-details-with-callback-notification)
  - [Exchange order](#exchange-order)
    - [Calculating exchange cost](#calculating-exchange-cost)
    - [Exchange types](#exchange-types)
    - [Getting settings](#getting-exchange-order-settings)
    - [Creating exchange](#creating-exchange)
    - [Cancelling limit exchange](#cancelling-limit-exchange)
    - [Repeating limit exchange](#repeating-limit-exchange)
    - [Getting details](#getting-exchange-order-details)
    - [Order details for callback notification](#exchange-order-details-with-callback-notification)

## Request Processing
Base api url - https://api.bbcore.co/  
Content-Type – json

Each response contains a structure in which there is a ‘status’ field indicating if such response was processed successfully. A successfully processed response has 2XX status code, and an unsuccessfully processed one - either 4XX or 5XX.

###### Example of a successfully processed request:

```javascript
 {"status": "success"}
```

###### Example of an unsuccessfully processed request:

``` javascript
 {"status": "error", "error": <error text>}
```

There are no error codes, there is only a verbally described reason.  
By default, all errors are returned in English.  
It is possible to localize errors in Russian by adding the following title to the request itself – ‘Accept-Language’ with the value ‘ru’.

# Public Data
## Common Enums
### Order Types:
     1. DEPOSIT
     2. WITHDRAWAL
     3. EXCHANGE
     4. INVOICE
     5. INTERNAL_MOVEMENT
     
### Order Statuses
   
#####  Final Order Statuses:
     1. ERROR (final status, order finished with error)
     2. CLOSED (final status, order finished with success)
     3. CANCELLED (final status, order cancelled by user or system)
     4. EXPIRED (final status)
     
##### Pending Order Statuses:
     1. NEW
     2. WAITING_FOR_PRICE (only for limit exchange order)
     3. WAITING_FOR_CONFIRMATION (only for Deposit order)
     4. WAITING_FOR_OPERATOR_CONFIRMATION
     5. PAYMENT_IN_PROGRESS
   
### Supported blockchains:
     1. ERC-20
     2. TRC-20
   
### Deposit Types:
     1. GATEWAY
     2. BANK-COLLECTION
     3. CASHBOX
   
### Withdrawal Types:
     1 GATEWAY
     2 BANK-COLLECTION
     3 CASHBOX
   
### Exchange types:
     1 MARKET
     2 LIMIT
   
   
## Getting active currencies list

```javascript
 GET "/api/v1/currency"
```

###### Sample response:

```javascript
{
  "status": "success",
  "currencies": ["BTC","EUR",...]
}

```

The system returns a list of currencies active at the moment as well as the name of a currency itself, as identified in the system.

## Getting active pairs list
```javascript
GET "/api/v1/pair"

```

###### Sample response:
```javascript
{
  "status": "success",
  "pairs": [
    [
      {
        "currency_to_spend": "UAH",
        "currency_to_get": "BTC",
        "name": "BTC_UAH",
        "base_currency": "BTC"
      }
    ],
```

API endpoint returns a list of active pairs by which exchanges can be made.

**Explanation of a pair name:**

Usually two currencies are involved in an exchange - one of them is spent, another one is received.  
In the current example, ‘BTC_UAH’ pair stands for buying BTC for UAH.
In order to purchase UAH for BTC, one needs to use another pair – ‘UAH_BTC’. In fact, we only have one pair - BTC and UAH, but two directions of exchange.

## Getting prices

```javascript
 GET "api/v1/exchange_rate"
```

###### Sample response:

```javascript
{  
  "rates": [  
    {  
      "pair": "UAH_EUR",  
      "base_currency_price": 0.03800836,  
      "price": 26.31  
    }  
  ]  
}                              
																						
```

With the current API endpoint, you can get all the prices for all exchange pairs (ticker).


###### Sample response:

```javascript
{
  "currency_to_get_amount": 1,
  "currency_to_get": "BTC",
  "currency_to_spend_amount": 202170.7475,
  "currency_to_spend": "UAH",
  "status": "success",
  "exchange_price": 202170.7475
}
```

In each calculation, a response always returns the calculated exchange (exchange price, how much will be spent, the currency, and how much will be received).

# User data
## Creating account

```javascript
  POST "api/v1/user/create"
```

###### Parameters:

```javascript
{
    "email": "string",
    "password": "string",
}                                                  

```

All the above fields are required. After API request is successfully completed, user will need to confirm their email (email letter will be sent to their email address).  
User can log in only after confirming their email. The email contains a link which user needs to click in order to confirm their email. Link lifetime is 1 day.

## Account types
After a user has confirmed their email, their account becomes ‘NOT_VERIFIED’.  
After verification, the account changes to ‘VERIFIED’.  
The third account type is designated for business users (merchants) – ‘BUSINESS’.

## Authorization types
Currently, there are two types of API authorization - with JWT token and with request signature through api_key and api_secret.
Users choose the preferable authorization type themselves. We recommend using the request signature.

### JWT authorization


```javascript
POST "api/v1/user/obtain_token"
```
###### Parameters:

```javascript
{
  "email": "string",
  "password": "string"
}                                              

```

###### Example of a successful response (token received):
```javascript
{
  "username": "<username>",
  "token": "<token>"
}
```

After a token has been received, user can perform requests that require authorization.
The token must be specified in the header --header 'Authorization: Bearer <token> header. Token lifetime is 15 minutes.
The token for ‘Swagger’ (through Authorize button) is specified in the same format so that it reloads and shows all available methods allowed for the user.  
After the token expires, it should be ‘reobtained’ through ‘obtain token’ method.

### Authorization by request signature
`api_key` and `api_secret` can be generated in the account and will be shown after the security confirmation process.
In order to use authorization by request signature, user needs to provide two headers:
```javascript
--header 'Sign: <generated_signature>' --header 'Key: <api_key>'                                          
```

###### Signature creation algorithm:
**Step 1:** You need to sort the request parameters in alphabetical order and transform nesting in parameters, if there is any.

**Example without nesting:**
```javascript
{
  "currency": "string",
  "amount": "string"
}							
```

In this case, you should get a dictionary, but with sorted keys:
```javascript
{
  "amount": "string",
  "currency": "string"
}							
```

**Example if there is nesting:**
```javascript
{
  "currency": "string",
  "amount": "string",
  "additional_info": {"client_ip": "8.8.8.8"}
}
```

**Transforms into the following dictionary:**
{
  "currency": "string",
  "amount": "string",
  "additional_info_client_ip": "8.8.8.8"
}

The keys of the resulting dictionary should be sorted in alphabetical order.

**Step 2:** A string for signature is created in this format:
```javascript
{request.method} - {request.path} - {sorted_params}
```

**Step 3:** The api_secret string is signed using the algorithm hmac_sha_256.

Similar manipulations are to be carried out with each request.

## Getting balance

```javascript
  GET "api/v1/user/balance"
```

###### Sample response:
```javascript
{
"wallets": {
    "USDT": {
      "ERC20": {
        "address": "0xa259A2a591341f7f169dCacD70Bbaf8CD7bA2B7D",
        "qr_file_data": "<qr file in base64 format>"
      },
    },
    "BTC": {
      "address": "3Bv2hPWSUcvE13F1BjuhWdCLNJnn1ZRLqb",
      "qr_file_data": "<qr file in base64 format>"
    },
    ...
  },
  "balance": {
    "EUR": {
      "currency": "EUR",
      "EUR": {
        "reserved": 0,
        "total": 0
      },
      "ETH": {
        "reserved": 0,
        "total": 0
      },
      "UAH": {
        "reserved": 0,
        "total": 0
      },
      "USDT": {
        "reserved": 0,
        "total": 0
      },
      "USD": {
        "reserved": 0,
        "total": 0
      },
      "RUB": {
        "reserved": 0,
        "total": 0
      },
      "BTC": {
        "reserved": 0,
        "total": 0
      }
    },
    ...
  }
}                                                  

```

This response contains crypto addresses that are assigned to the user and available for deposit. 
Crypto addresses are generated for different payment methods (blockchains), if we support different networks. 
The balance itself also comes in two fields, ‘total’ and ‘reserved’. ‘Total’ stands for the total amount of funds, ‘Reserved’ – for the funds already taken and allocated in the orders. Using the balance, you can find out how much of a particular currency is currently available; moreover, you can get a conversion of funds from one currency to another.



**For example:**

1) The amount of ‘EUR’ in your account - balance [‘EUR’] [‘EUR’]

2) The amount of ‘EUR’ in ‘ETH’ - balance [‘EUR’] [‘ETH’]

## Getting account settings
```javascript
  GET "api/v1/user/account_info"
```

```javascript
  {
  "name": "Svyat",
  "email": "svyatoslavkravchenko@gmail.com",
  "account_type": "VERIFIED",
  "is_email_verified": true,
  "referral_id": "89f67887-cae6-4a6f-a393-a89079878a57",
  "created": "2019-02-05T08:13:48.952334",
  "is_2fa_enabled": false,
  //limits, fee, processing_rules
  }
```

The response includes a username, email address, type of account, information on whether such mail has been verified, whether two-factor authorization is enabled, as well as limits, fees, processing_rules, personal funds, etc. for each order type.

# Orders
## Getting order details
When creating an order through API, system provides order_id of such order, thus giving the opportunity to get the details.
```javascript
GET "api/v1/orders/detail"
```
###### Parameters:

```javascript
{
  "order_id": $order_id
}						
```

The response provides order details such as whether the order has been created by a specific account or the account has participated in this order. There are orders in which several accounts participate.
Further below, we provide examples of the specific response for each order type.
## Getting order history
```javascript
GET "api/v1/orders/history"
```
###### Sample response:

```javascript
{
  "orders": [<orders_details_list>]
  "status": "success",
  "pages_count": 78
}
```

Order history has pagination with 10 orders displayed per page.

#### Params
  - `order_type` - [OrderTypeEnum](#order-types)
  - `order_status` - [OrderStatusEnum](#order-statuses)
  - `order_sub_type` - the subtype of an order. Some orders have their own subtypes that can be used to filter for them.
  - `page` - page number
  - `limit` - items per page (default=10)
  - `from_timestamp`, `till_timestamp` - filtering by time of order creation.
  - `currency` - filtering by currency - displays all orders for a particular currency.
  - `address` - finds all orders with address. These can be either deposit or withdrawal orders.

### Callback notification
When creating an order, it is possible to specify the url for notification when the status of such order changes.
With callback notification, a POST request is sent to the specified address. 
If the request is processed successfully - response will have code 200, and if an error occurs, the system will try to send a callback 3 times with an interval of 5 minutes.

## Deposit order
### Deposit order operation scheme

To deposit an account, what is needed first is to get an address for deposit, and then transfer money to this account; it can be either a crypto-currency address or a link to pay by card. A deposit order will be created after a successful deposit.

#### Example of status transitions by cryptocurrencies:
Successful deposit - 'NEW' -> 'WAITING_FOR_CONFIRMATION' -> 'CLOSED'

As soon as there is a transaction in the network, the order gets the status ‘NEW’ followed by the status ‘WAITING_FOR_CONFIRMATION’, and will remain in this status until a certain number of confirmations for crediting money to the balance is reached. The order may become ‘EXPIRED’ if the required number of transaction confirmations is not reached within 24 hours.

#### Example of status transitions by fiat currencies:
Successful deposit - 'NEW'-> 'CLOSED'  
Deposit failed - 'NEW' -> 'ERROR'  
Link has expired 'NEW' -> 'EXPIRED' ('CANCELLED')

### Getting deposit order settings
```git
GET "api/v1/user/account_info"
```
There are three types of settings – ‘fees’, ‘limits’, ‘processing_rules’.
#### Limits
```javascript
"deposit_order_limits": {
    "UAH": {
      "GATEWAY": {
        "P2P": {
          "max_amount": 14000,
          "min_amount": 10
        }
      }
    }
  }
 ```

As of now, only fiat currencies have limits for deposit with an indicated limit for one operation. In this example, deposit limit is shown for ‘UAH’, with ‘P2P’ type of deposit.


#### Fee
Shows fees available for a particular deposit currency:
```javascript
"deposit_order_fees": {
	"BTC": {
          "GATEWAY": {
            "P2P": {
             "static_fee": 0,
             "percent_fee": null
        }
      }
    },
    "USDT": {
      "GATEWAY": {
        "P2P": {
          "ERC20": {
            "percent_fee": 0,
            "static_fee": 0
          },
}													
```

`static_fee` shows the static value that is taken within the deposit transaction.  
`percent_fee` shows the percentage value that is taken within the deposit transaction.

    
#### Processing rules
Indicates whether the deposit is enabled:
```javascript
"deposit_order_processing_rules":{
      "BTC":{
         "GATEWAY":{
            "P2P":{
               "is_cancel_enabled":true,
               "confirmations_count":null,
               "is_deposit_enabled":true,
               "is_repeat_enabled":true,
               "disable_description":null
            }
         }
      },
      "USDT": {
      "GATEWAY": {
        "P2P": {
          "ERC20": {
            "is_cancel_enabled": false,
            "confirmations_count": 2,
            "is_deposit_enabled": true,
            "is_repeat_enabled": false,
            "disable_description": ""
          },
   }
```

`confirmations_count` is irrelevant for fiat currencies while for cryptocurrency it shows the number of required confirmations for the deposi

If deposit is disabled - we show the reason why.

### Getting deposit address

```javascript
POST "api/v1/deposit/address"
```

###### Parameters:

```javascript
{
  "payment_method": "string",
  "success_url": "string",
  "currency": "string",
  "amount_to_spend": "string",
  "callback_url": "string",
  "payment_type": "string",
  "currency_convert_to": "string",
  "amount_to_receive": "string",
  "additional_info": {},
  "fail_url": "string",
  "comment": "string",
  "processing_url": "string"
}
```

**Parameters description:**
- `payment_method` - should be set, only in case when currency support different payment methods (blockchains). For now is used only for USDT.
- `success_url, fail_url, processing_url` - should be set, only in case when redirect needed to custom url, after payment.
- `callback_url` - url for callback notifications.
- `amount_to_spend`, `amount_to_receive` - you need to specify one of these parameters. In the first case, the system will create a link for deposit taking into account the amount a client can spend, considering fees. In the second case, the balance will be deposited by this amount (amount_to_receive), and fees will be charged on top of this amount. These fields are mandatory only for fiat currencies deposit.
- `comment` - a comment that will be shown when creating an order at a specific address and received in callback - (optional parameter).
- `payment_type` - deposit method; if it is not specified, P2P will be used by default.
- `additional_info` - a dictionary with additional parameters. `customer_id` is an optional parameter, `client_ip` is a required parameter, `deposit_email` or `deposit_phone` must be specified if user's account status is 'BUSINESS'. Customer id can be also specified as id in your system
- `currency_convert_to` - auto-conversion mode. When deposit is successful, currency will be exchanged to currency_convert_to

###### Example of obtaining a deposit address for cryptocurrency:

```javascript
{
  "currency": "BTC",
  "callback_url": "http://",
  "additional_info": {"client_ip": "1.1.1.1",
	                  "customer_id": "<some_id>"}
}
```

###### Example of obtaining a deposit address for UAH:

```javascript
{
  "amount_to_spend": "10", 
  "callback_url": "http://",
  "currency": "UAH",
  "additional_info": {
	"client_ip": "62.80.170.214",
	"deposit_email": "<some_email>"
    }
}
```

###### Example of obtaining a deposit address for UAH and exchange it to USDT:

```javascript
{
  "amount_to_spend": "1000",
  "currency_convert_to": "USDT",
  "callback_url": "http://",
  "currency": "UAH",
  "additional_info": {
	"client_ip": "62.80.170.214",
	"deposit_email": "<some_email>"
    }
}
```

###### Response analysis:

```python
{
  "status": "success",
  "addr": "37Yj3bGzjXq1ZGAKqGKcQQxKP75QKbWSrU",
  "external_id": "91c5df75-5fd0-4149-9231-56587fdc2ac6",
  "url": null, # link to payment web frame 
  "qr": "<qr data in base64>"
}
```

`qr` will be filled only for cryptocurrency
 
### Deposit order details for callback notification

```javascript
{"address": "0x3204a7913e9Da272dD30F0fb89dB228e4677908E", 
"currency": "ETH", 
"amount": 0.69690944, 
"status": "CLOSED", 
"confirmations_count": 12, 
"customer_id": null, 
"masked_card_number": null, 
"received_amount": 0.69690944, 
"tr_hash": "0x9db73ea5b216c215c63d613b9cbc6379211585a6640df9e5637b9a9b79972cbe", 
"comment": "Account Creation Wallet", 
"order_id": "fba51a08-4e3d-4e1e-af56-b169d34c4b81"}
``` 

### Getting deposit order details

###### Example

```javascript
{
  "external_id": "92ba1fb2-b0af-4281-89e9-21f47ae16874",
  "order_type": "DEPOSIT",
  "status": "CLOSED",
  "fee": 5.15,
  "details": {
		"fee": 5.15,
		"address": "https://mapi.xpay.com.ua/ru/frame/widget/e89ce23c-4d5b-4124-897a-84e75ea972c9",
		"tr_id": "e89ce23c-4d5b-4124-897a-84e75ea972c9",
		"comment": null
	     },
  "currency": "UAH",
  "confirmations_count": null,
  "order_sub_type": "GATEWAY",
  "amount": 10,
  "dt": "2020-02-04 20:11:13.860751",
  "comment": null,
  "tr_hash": "e89ce23c-4d5b-4124-897a-84e75ea972c9"
}
```

## Withdrawal order
### Withdrawal order operation scheme

Upon successful creation of a withdrawal order, such order automatically switches to the status ‘NEW’.  
Upon successful withdrawal, the order's status changes to ‘CLOSED’. This is the final status, which means success.  
In case of withdrawal error, the order`s status changes to ‘ERROR’.  
It is possible to cancel the order if withdrawal transaction/transactions have not been completed in full. Upon successful acceptance of a cancellation request, the status of the order changes to ‘CANCELLING’ and ‘CANCELLED’ when finally cancelled.  
When withdrawing fiat, the withdrawal amount can be divided into several transactions, and there may be situations when some of the transactions are successful and some are not. In this case, two orders are created, one with ‘CLOSED’ status showing the amount of funds that was successfully withdrawn, and the second order with ‘ERROR’ status showing the amount of funds that has not been withdrawn.

### Getting withdrawal order settings

Shows the values for limits, fees, and whether withdrawal is enabled:
```git
 GET "api/v1/user/account_info"
```
Indicates whether withdrawal is enabled, whether withdrawal cancellation is enabled, whether withdrawal repeat is enabled:

```javascript
withdrawal_order_processing_rules": {
	"DOGE": {
      "GATEWAY": {
        "disable_description": null,
        "is_cancel_enabled": true,
        "is_repeat_enabled": true,
        "is_withdrawal_enabled": true
      }
    },
    "USDT": {
      "GATEWAY": {
        "TRC20": {
          "disable_description": "",
          "is_cancel_enabled": false,
          "is_repeat_enabled": false,
          "is_withdrawal_enabled": true
        }
        }
    }
        
}
```
Displays the fees:

```javascript
"withdrawal_order_fees": {
	"DOGE": {
      "GATEWAY": {
        "static_fee": 5,
        "percent_fee": 0
      }
    },
    "USDT": {
      "GATEWAY": {
        "ERC20": {
          "static_fee": 1,
          "percent_fee": 0
        },
}
```
The following options are possible:  
`static_fee` shows the static value that is taken within the withdrawal transaction.  
`percent_fee` shows the percentage value that is taken within the withdrawal transaction.


Displays the limits:
```javascript
"withdrawal_order_limits": {
	"DOGE": {
      "GATEWAY": {
        "min_amount": 10,
        "max_amount": 100000
      }
    },
    "USDT": {
      "GATEWAY": {
        "ERC20": {
          "min_amount": 10,
          "max_amount": 10000
        },
}
```


### Creating withdrawal order

```git
 POST "api/v1/withdrawal"
```

###### Parameters:
```javascript
{
  "comment": "string",
  "callback_url": "string",
  "currency_convert_from_amount": 0,
  "wallet_to": "string",
  "withdrawal_type": "string",
  "payment_method": "string",
  "currency": "string",
  "amount": 0,
  "additional_info": {},
  "currency_convert_from": "string"
}
```

**Parameter analysis:**  
 - `currency` - currency, required parameter  
 - `withdrawal_type` - withdrawal type, required parameter. ‘GATEWAY’ should be indicated when withdrawing to cards or cryptocurrency addresses  
 - `comment` - a comment to withdrawal, optional parameter  
 - `callback_url` - url for notifications, optional parameter. If it is indicated, notifications with status updates on a specific order will be received  
 - `amount` – withdrawal amount, required parameter  
 - `wallet_to` - wallet address, required parameter  
 - `additional_info` - dictionary with additional parameters. `client_ip` - optional, `withdrawal_email` must be specified if user's account status is 'BUSINESS'  
 - `сurrency_convert_from` - optional parameter, to make exchange before the withdrawal takes place. For example - user has 100 USDT and wants to withdraw 100 UAH - system will exchange USDT to UAH and then make withdrawal for 100 UAH.  
 - `currency_convert_from_amount` - optional parameter, to make exchange with given amount and withdraw the result of the exchange.  


**Examples:**

###### Creating withdrawal order (UAH):

```javascript
{
   "currency": "UAH",
   "withdrawal_type": "GATEWAY",
   "comment": "comment",
   "callback_url": "http://",
   "additional_info": {"withdrawal_email": $customer_email},
   "amount": 100,
   "wallet_to": $card_number
}
```

###### Creating withdrawal order (cryptocurrency):

```javascript
{
   "currency": "BTC",
   "withdrawal_type": "GATEWAY",
   "comment": "comment",
   "callback_url": "http://",
   "amount": 100,
   "wallet_to": $btc_addr
}
```

In response for creating an order, API returns order_id, ID of the order.

### Cancelling withdrawal order

A withdrawal order can be cancelled if cancellation is allowed and the relevant order is being processed.
If withdrawal has already been completed (transaction has already been sent), such transaction cannot be canceled. User has time to cancel before the withdrawal is complete.

###### URI

```javascript
 POST "api/v1/withdrawal/cancel"
```

###### Parameters:

```javascript
{
  "order_id": "string"
}
```

If withdrawal cancellation has been successful, the system will return code 200 in response, otherwise an error will be returned.

### Repeating withdrawal order

It is possible to repeat a withdrawal order if repeat is enabled and such order has been processed.

```javascript
 POST " /api/v1/withdrawal/repeat            "
```

###### Parameters:

```javascript
{
  "amount": 0,
  "order_id": "string"
}
```

`order_id` is required.   
`amount` specifies the amount for withdrawal as a part of the new order.


### Getting withdrawal order details
```javascript
{
      "currency": "UAH",
      "order_type": "WITHDRAWAL",
      "withdrawal_transactions": [
        4029.21576113
      ],
      "dt": "2021-10-07 06:42:04.927243",
      "status": "CLOSED",
      "details": {
        "comment": "",
        "address": "414943*2331",
        "tr_id": "357c9e10-dadd-4291-8f27-542d3f4c3d2f",
        "fee": 20.14607881
      },
      "customer_id": null,
      "amount": -4049.36183994,
      "comment": "",
      "order_sub_type": "GATEWAY",
      "external_id": "0f56a4f4-de2a-42e8-8576-6276b25a9177",
      "amount_to_withdrawal": -4029.21576113
    }
```

### Withdrawal order details for callback notification

```javascript
{
    "amount": -4238.20359, 
    "comment": "Payout order: 144545", 
    "withdrawal_transactions": [4217.118], 
    "currency": "UAH", 
    "order_type": "WITHDRAWAL", 
    "status": "CLOSED", 
    "order_sub_type": "GATEWAY", 
    "amount_to_withdrawal": -4217.118, 
    "external_id": "bf7aceea-d430-40ab-b466-49cc47418c61", 
    "dt": "2021-10-30 09:51:30.621008", 
    "customer_id": null, 
    "details": {
                "tr_id": "70c9b4e6-3792-430b-974c-21856ac8ae12", 
                "fee": 21.08559, 
                "address": "537541*2875", 
                "comment": "Payout order: 144545"}
                }
```

## Internal movement order
### Internal movement order operation scheme

This type of order involves the transfer of funds between the users of the platform. For example, user A has 1000 USD, thus, knowing the recipient's email in the system, you can instantly transfer 1000 USD.  
Such orders may have the following statuses:  
‘NEW’ – the order is being processed  
‘CLOSED’ - successfully closed  
‘ERROR’ – resulted in an error

### Getting internal movement order settings
Shows the values for limits, fees, and whether internal movement is enabled
```javascript
 GET "api/v1/user/account_info"
```

Indicates whether the movement of funds within the platform is enabled, whether the repeat is enabled:  

```javascript
"internal_movement_processing_rules": {
   "ETH": {
      "CROSS_ACCOUNT": {
        "is_enabled": true,
        "disable_description": null,
        "is_repeat_enabled": true
      }
    },
}
```

Shows available fees (which are paid by the sender):
```javascript
"internal_movement_fees": {
   "ETH": {
      "CROSS_ACCOUNT": {
        "static_fee": 0,
        "percent_fee": 0
      }
    },
}
```

The following options are possible:
- `static_fee` - a static amount is paid when transferring funds  
- `percent_fee` – percentage-based amount is paid    

\
Shows the limits for the operation (the amount of the transfer can't go beyond the specified amounts):
```javascript
"internal_movement_limits": {
  "ETH": {
      "CROSS_ACCOUNT": {
        "min_amount": 0,
        "max_amount": 500
      }
    }
}
```


### Creating internal movement order

```javascript
 POST "api/v1/internal_movement"
```

###### Parameters:

```javascript
{
  "comment": "string",
  "amount": "string",
  "callback_url": "string",
  "currency": "string",
  "destination_account_email": "string"
}
```

**Parameter analysis:**
- `currency` - currency, required parameter
- `comment` – comment, optional parameter
- `callback_url` - url for notifications, optional parameter. If used, notifications with status updates on a specific order will be received
- `amount` – amount to move, required parameter
- `destination_account_email` - email of the recipient of funds on the platform

If successful, the response will contain the order_id of the created order.  

### Repeating internal movement order

It is possible to repeat an internal movement order if repetition is enabled and the order is complete.

```javascript
POST "/api/v1/internal_movement/repeat"
```

###### Parameters:

```javascript
{
  "amount": 0,
  "order_id": "string"
}
```
`order_id` is required. You can specify the amount - this will change the amount for transfer as part of a new order.

### Getting internal movement order details

```javascript
     {
      "fee": 0,
      "amount": 333,
      "details": {
        "destination_account": "svyatoslavkravchenko@gmail.com",
        "comment": null,
        "source_account": "popovich.evgenii@gmail.com"
      },
      "order_type": "INTERNAL_MOVEMENT",
      "status": "CLOSED",
      "internal_movement_type": "CROSS_ACCOUNT",
      "external_id": "63d15591-03b7-4975-8d21-a85f05197c2c",
      "dt": "2021-10-03 09:35:25.921207",
      "currency": "EUR"
    },
```

### Internal movement order details for callback notification

```javascript
{
      "fee": 0,
      "amount": 333,
      "details": {
        "destination_account": "svyatoslavkravchenko@gmail.com",
        "comment": null,
        "source_account": "popovich.evgenii@gmail.com"
      },
      "order_type": "INTERNAL_MOVEMENT",
      "status": "CLOSED",
      "internal_movement_type": "CROSS_ACCOUNT",
      "external_id": "63d15591-03b7-4975-8d21-a85f05197c2c",
      "dt": "2021-10-03 09:35:25.921207",
      "currency": "EUR"
    },
 ```
 
## Invoice order
### Invoice order operation scheme
The platform allows you to create invoices for users to pay.
For example, a merchant may issue an invoice for payment of goods or services and send it to a user.

#### Invoice operation stages:
1) Invoice is created (a link for payment is generated).
2) User follows the link, chooses in which way payment should be done and pays it (using funds available on BBCore platform  or sending money to crypto-address or paying with card)


#### Possible statuses:
1) ‘NEW’ - order created.
2) ‘PAYMENT_IN_PROGRESS’ - payment is being processed. Awaiting the final status.
3) ‘CLOSED’ - invoice successfully paid.
4) ‘ERROR’ – invoice payment failed.
5) ‘EXPIRED’ - invoice is void. 
6) ‘WAITING_FOR_CONFIRMATION’ - waiting for payment on deposit address to pay invoice via FIAT GATEWAY or CRYPTO GATEWAY.

### Getting invoice order settings

```javascript
GET api/v1/user/account_info
```

#### Getting settings on whether invoice creation and payment are enabled.

  ```javascript
 "invoice_order_processing_rules": {
    "ETH": {
      "INVOICE_PAYMENT": {
        "is_enabled": true,
        "disable_description": null
      },
      "INVOICE_CREATION": {
        "is_enabled": true,
        "disable_description": null
      }
  ```
Indicates whether invoice creation and payment for a particular currency is enabled.

#### Getting fee settings

```javascript
"invoice_order_fees": {
    "ETH": {
      "percent_fee": 0
    },
  ```

Shows fees' amounts and how the fees will be calculated for each invoice.

#### Getting operation limits settings

```javascript
"invoice_order_limits": {
    "ETH": {
      "min_amount": 0.00327593,
      "max_amount": 0.21839538
    },
  ```

Shows the limits on invoices' amounts.

### Creating invoice
 ```javascript
 POST api/v1/invoice
 ```
 
 #### Parameters
 ```javascript
 {
  "comment": "string" – optional parameter
  "amount": "string" – invoice amount (required parameter)
  "currency": "string" – required parameter
  "pay_account_email":  - email of invoice recipient, required parameter
  "payment_option": restricts the ways in which user can pay the invoice. Choices - ALL, PLATFORM, FIAT, CRYPTO. Default value - ALL
  "additional_info": optional parameter (dict). Can include "callback_url", "success_url", "fail_url". 
  If "fail url" is set - customer will be redirected to the specified page in case of unsuccessful payment.
  If "success_url" customer will be redirected to the specified page in case of successful payment.
}
```

 #### Payment option choices
 
 ALL - user can pay via all available ways in which to pay
 PLATFORM - user can pay with funds available on the platform (login to BBCore is required)
 FIAT - user can pay via FIAT GATEWAY (login to BBCore not required)
 CRYPTO - user can pay via CRYPTO GATEWAY(login to BBCore not required)

#### Sample response for a successfully created invoice
```javascript
 {
  "url": "https://app.bbcore.co/invoice/cf700d3b-20b4-4b33-b195-38709b7e51fa",
  "order_id": "cf700d3b-20b4-4b33-b195-38709b7e51fa",
  "status": "success"
}
```

In order to pay, user needs to follow the specified link, choose the way in which to pay or currency and proceed through the payment process.

### Getting invoice details
Invoice details can be seen by the account that created this invoice, and by the account that paid it.

```javascript
{
      "external_id": "986a391d-ffe4-49fc-9117-75f384226f2c",
      "details": {
        "destination_account": "svyatoslavkravchenko@gmail.com",
        "customer_email": "svyatoslavkravchenko@gmail.com",
        "comment": "test_invoice",
        "source_account": null,
        "payment_option": "ALL"
      },
      "dt": "2021-10-28 12:56:42.394583",
      "internal_id": 556102,
      "order_type": "INVOICE",
      "url": "https://app.bbcore.co/invoice/986a391d-ffe4-49fc-9117-75f384226f2c",
      "fee": 0,
      "pay_amount": 27183,
      "pay_currency": "UAH",
      "order_sub_type": null,
      "currency": "USDT",
      "amount": 1000,
      "status": "EXPIRED"
    },
```

### Invoice order details with callback notification

```javascript
{
      "external_id": "986a391d-ffe4-49fc-9117-75f384226f2c",
      "details": {
        "destination_account": "svyatoslavkravchenko@gmail.com",
        "customer_email": "svyatoslavkravchenko@gmail.com",
        "comment": "test_invoice",
        "source_account": null,
        "payment_option": "ALL"
      },
      "dt": "2021-10-28 12:56:42.394583",
      "internal_id": 556102,
      "order_type": "INVOICE",
      "url": "https://app.bbcore.co/invoice/986a391d-ffe4-49fc-9117-75f384226f2c",
      "fee": 0,
      "pay_amount": 27183,
      "pay_currency": "UAH",
      "order_sub_type": null,
      "currency": "USDT",
      "amount": 1000,
      "status": "EXPIRED"
    },
```

## Exchange order

### Calculating exchange cost

```javascript
 GET "api/v1/exchange/calculate"
```

###### Parameters:

`currency_to_get` - required, the currency user buys.
`currency_to_spend` - required, the currency user sells.
`currency_to_get_amount`, `currency_to_spend_amount` - one (and only one) of those parameters should be specified (see details in [Exchange creation rules](#exchange-creation-rules))  
`exchange_price`- optional parameter. User can specify the desired exchange price of the base currency (see details in [Exchange creation rules](#exchange-creation-rules)). If the price is not specified, market price is used.


### Exchange types
The platform allows you to make two different types of exchange:

1) Market exchange (order subtype – ‘MARKET_EXCHANGE’)
2) Exchange when the specified price is reached on the market - (order subtype – ‘LIMIT_EXCHANGE’)

Market exchange has the following statuses:
1) ‘NEW’ – order created
2) ‘CLOSED’ - exchange successfully completed
3) ‘ERROR’ – exchange resulted in an error

Limit exchange has the following statuses:
1) ‘NEW’, ‘WAITING_FOR_PRICE’ - waiting for the price on the platform
2) ‘CLOSED’ - exchange successfully completed
3) ‘ERROR’ – exchange resulted in an error
4) ‘CANCELLING’ – exchange is being cancelled
5) ‘CANCELLED’ - exchange was cancelled


### Getting exchange order settings

```javascript
GET api/v1/user/account_info
```

#### Getting settings for exchange transactions availability 

```javascript
"exchange_order_processing_rules": {
    "EUR_USD": {
      "is_market_exchange_enabled": true,
      "is_cancel_enabled_for_limit_exchange": false,
      "is_repeat_enabled_for_limit_exchange": false,
      "is_limit_exchange_enabled": true
    },
```
Parameters:   
`is_market_exchange_enabled` - indicates whether market exchange is enabled
`is_limit_exchange_enabled` - indicates whether limit exchange is enabled
`is_cancel_enabled_for_limit_exchange` – indicates whether cancellation is enabled for limit exchange  
`is_repeat_enabled_for_limit_exchange` – indicates whether repeat is enabled for limit exchange  

#### Getting settings for exchange order limits

```javascript
"exchange_order_limits": {
    "EUR_USD": {
      "max_amount": 10000,
      "currency_to_spend": "USD",
      "min_amount": 5
    },
```


#### Getting exchange order fees

```javascript
"exchange_order_fees": {
    "LTC_USD": {
      "LIMIT_EXCHANGE": {
        "static_fee": 0,
        "percent_fee": 0
      },
      "MARKET_EXCHANGE": {
        "static_fee": 0,
        "percent_fee": 0
      }
    },
```
Fees can be different for market and limit exchange 


### Creating exchange

```javascript
POST /api/v1/exchange
```

#### Parameters
```javascript
{
  "currency_to_spend": "string",
  "currency_to_get": "string",
  "currency_to_spend_amount": "string",
  "callback_url": "string",
  "currency_to_get_amount": "string",
  "exchange_price": "string"
}
```

#### Exchange creation rules
1) It is necessary to specify the currency that is sold (`currency_to_spend`) and the currency that is bought (`currency_to_get`)
2) User needs to specify one (and only one) of the amounts - either make an exchange selling N amount of currency X (`currency_to_spend_amount`), or make a purchase to get N amount of currency Y (`currency_to_get_amount`)
3) If user wants to purchase at a specific price, they need to provide the `exchange_price` parameter value. This value indicates the price of the base currency in the secondary currency. `base currency` is shown in the response to the request to [api/v1/exchange/calculate](#calculating-exchange-cost). E.g. with BTC and USDT pair, for both directions (BTC_USDT and USDT_BTC) `base currency` = BTC. So, `exchange_price` for both directions is shown in USDT. For BTC_USDT it shows how many USDT user needs to buy 1 BTC, and for USDT_BTC it shows how many USDT user gets for selling 1 BTC.
4) User can specify `callback_url` to subscribe to notifications on changes in exchange order status

#### Examples

1) Purchasing 1 BTC for UAH at a market price
    ```javascript
    {
      "currency_to_spend": "UAH",
      "currency_to_get": "BTC",
      "currency_to_get_amount": "1"
    }
    ```
2) Purchasing 1 BTC for UAH at the market price spending 1000 UAH on exchange
    ```javascript
    {
      "currency_to_spend": "UAH",
      "currency_to_get": "BTC",
      "currency_to_spend_amount": "10000"
    }
    ```

3) Purchasing 1 BTC for UAH at the price of 200.000
    ```javascript
    {
      "currency_to_spend": "UAH",
      "currency_to_get": "BTC",
      "currency_to_get_amount": "1",
      "exchange_price": "200000"
    }
    ```

### Cancelling limit exchange
```javascript
POST api/v1/exchange/cancel
```

#### Parameters
```javascript
{
  "order_id": "string"
}
```

Only a limit exchange order can be cancelled if cancellation is enabled and only while the order is being processed.

### Repeating limit exchange

```javascript
POST /api/v1/exchange/repeat
```
#### Parameters
```javascript
{
  "order_id": "string"
}
```

Repeating the order is possible if repeat is enabled and the order is completed.


### Getting exchange order details

```javascript
{
      "currency": "UAH_USD",
      "order_type": "EXCHANGE",
      "status": "NEW",
      "order_sub_type": "MARKET_EXCHANGE",
      "external_id": "1a6b4e9d-750f-40b8-8f27-71171e9f432c",
      "dt": "None",
      "details": {
        "currency_to_get_amount": 879.45,
        "currency_to_spend_amount": 33,
        "price": 26.65,
        "fee": 0
      }
    },
```

### Exchange order details with callback notification

```javascript
{
      "currency": "UAH_USD",
      "order_type": "EXCHANGE",
      "status": "NEW",
      "order_sub_type": "MARKET_EXCHANGE",
      "external_id": "1a6b4e9d-750f-40b8-8f27-71171e9f432c",
      "dt": "None",
      "details": {
        "currency_to_get_amount": 879.45,
        "currency_to_spend_amount": 33,
        "price": 26.65,
        "fee": 0
      }
    },
```

