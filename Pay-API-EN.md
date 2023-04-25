# Pay.mercuryo API features
- Choosing a Host-to-host integration method or redirection (hosted payment page)
- Support for working with different payment methods.
- Execution of refunds: the point of sale operator can independently make full or partial refunds.
- Generating payment reports.


## Detailed description of working with the API


**Table of contents**

1.  [Getting started with the system](/Pay-API-EN.md#1-getting-started-with-the-system)
2. [Request parameters](/Pay-API-EN.md#2-request-parameters)
3. [Basic parameters](/Pay-API-EN.md#3-basic-parameters)
	
	3.1. [Additional parameters (optional parameters)](/Pay-API-EN.md#31-additional-parameters-optional-parameters)
	
	3.2. [Rules for creating a signature (the sign parameter)](/Pay-API-EN.md#32-rules-for-creating-a-signature-the-sign-parameter)
4. [Passing a feedback message between the payment gateway and the Partner's website](/Pay-API-EN.md#4-passing-a-feedback-message-between-the-payment-gateway-and-the-partners-website)
	
	4.1. [Description of the parameters of the feedback document](/Pay-API-EN.md#41-description-of-the-parameters-of-the-feedback-document)
5. [Request information on transactions (detailed information on selected orders or transactions)](/Pay-API-EN.md#5-request-information-on-transactions-detailed-information-on-selected-orders-or-transactions)
6. [Formation of cardholder data on the merchant side (PCI DSS certificate required)](/Pay-API-EN.md#6-formation-of-cardholder-data-on-the-merchant-side-pci-dss-certificate-required)
7. [Requesting a report on transactions for a period of time](/Pay-API-EN.md#7-requesting-a-report-on-transactions-for-a-period-of-time)
8. [Refund request](/Pay-API-EN.md#8-refund-request)
9. [Request for discarding/unholding funds](/Pay-API-EN.md#9-request-for-discardingunholding-funds)
10. [Request for withdrawal of held funds(funds put on hold)](/Pay-API-EN.md#10-request-for-withdrawal-of-held-fundsfunds-put-on-hold)
11. [Recurring payments (regular, recurring)](/Pay-API-EN.md#11-recurring-payments-regular-recurring)
12. [Test cards](/Pay-API-EN.md#12-test-cards)
13. [Customer initiated transactions](/Pay-API-EN.md#13-pay-token-for-customer-initiated-transactions)

[FAQ](/Pay-API-EN.md#faq)


### 1. Getting started with the system
       
For each User who wants to make a payment on the Partner's website (or mobile app), an HTTP POST request for the URL must be initiated https://pay.mercuryo.io/pay/.
> Sandbox (testing environment) URL use : https://pay.mrcr.io/pay
>> Sandbox environment is provided by pay.mercuryo manager

> Production URL use : https://pay.mercuryo.io/pay/

The user will be redirected to the payment page https://pay.mercuryo.io/pay/, where the store's payment systems will be available for selection

To see your api_key and secret_key, you must first add a test store (site) and connect a test wallet.

*Step 1*: ![Step 1](/images/en/Step-1.png?raw=true "Step 1")
 


*Step 2*: ![Step 2](/images/en/Step-2.png?raw=true "Step 2")  


*Step 3*: After saving the new store in the second step, api_key and secret_key are automatically generated. 
![Step 3-1](/images/en/Step-3.png?raw=true "Step 3-1")
![Step 3-2](/images/en/Step-3-1.png?raw=true "Step 3-2") 


*Step 4*: Go to the Payment form and  try to pay: system redirects to the payment form (which can be customized for you). 
![Step 4-1](/images/en/Step-4.png?raw=true "Step 4-1")
![Step 4-2](/images/en/Step-4-1.png?raw=true "Step 4-2")
![Step 4-3](/images/en/Step-4-2.png?raw=true "Step 4-3") 

 


### 2. Request parameters
Request for a URL https://pay.mercuryo.io/pay/ (POST) two parameters are passed:
- data - MIME base64 encoded string of the JSON document;
- sign - signature, which is formed on the basis of the data string using the hash_hmac function to confirm the validity of the data.

***

***Example of implementation in PHP:***

	<?php 
	$secret_key = '13148649399496d8'; // password of the store
	$order_data = array(
		'api_key' => 'c84f1ac0-e4f0-0131-5298-70921c57c2a2', 
		'expiration' => '2014-01-01 00:00', 
		'amount' => 327.78, 
		'currency' => 'RUR', 
		'description' => 'proba', 
		'reference' => '123456789', 
		'success_url' => 'http://test.ru/success',
		'failure_url' => 'http://test.ru/failure',
		'lang' => 'ru'
	);
	$data = base64_encode(json_encode($order_data));
	$sign = hash_hmac('md5', $data, $secret_key); //signature generation
	?>
	<form action=" https://pay.mercuryo.io/pay/" method="post" >
	     <input type="hidden" name="data" value="<?php echo $data ?>">
	     <input type="hidden" name="sign" value="<?php echo $sign ?>">
	     <input type="submit" value="Оплатить заказ">
	</form>
	
***



  ### 3. Basic parameters


| Parameter name  | Type  | Maximum length  | Description | Example  | Mandatory |
| ------------- | -------------  | :-------------: | -------------  | ------------  | ------------- |
| api_key  | String   | 255  | Store key (identifier)   | c84f1ac0-e4f0-0131-5298-70921c57c2a2   | Yes|
| expiration| String  | -  | Maximum time for order payment   | 2014-07-21 12:44  | Yes|
| amount  | Integer   | 10,2  | Price to be paid Format: a positive number with "." as the separator, no more than two digits after the dot | 123.45  | Yes|
| currency  | String   | 3  | Currency in which the product price is indicated. (3-digit alphabetic ISO 4217 currency code) |UAH, RUR, EUR, USD  | Yes|
| reference  | String   | 255  | Unique order number in the store system for further order identification   | 7EZUWB | Yes|
| group_reference | String   | 255 | Site name, group identifier   | `Group.site.com` | No |
| description | String  | 255  | Order description   | Оплата авиа билетов | Yes |
| success_url  | String   | 255  | URL that the user will be redirected to if the payment is successful  | `http://test.net/success`  | Yes |
| failure_url | String   | 255  | URL to which the user will be redirected in case of unsuccessful payment  | `http://test.net/failure` | Yes|
| lang  | String   | 2  | Language for displaying information on the payment page.   | ru,en,uk (ru — по умолчанию)| Yes|
| params.user_email  | String   | -  | User e-mail    | `test@yandex.ru` | Yes|
| params.user_id  | String   | -  | Name, user ID in the merchant's system | test_user  | Yes|
***

***Example of source JSON data for generating the data parameter:***

	{
	    "api_key":"c84f1ac0-e4f0-0131-5298-70921c57c2a2",
	    "expiration":"2014-01-01 00:00",
	    "amount":327.78,
	    "currency":"RUR",
	    "description":"proba",
	    "reference":"123456789",
	    "success_url":"http:\/\/test.ru\/success",
	    "failure_url":"http:\/\/test.ru\/failure",
	    "lang":"ru"
	}
	

As a result, the base64-encoded JSON document and signature (signature password "123") get the variables "data" and "sign":

data:
>`“eyJhcGlfa2V5IjoiYzg0ZjFhYzAtZTRmMC0wMTMxLTUyOTgtNzA5MjFjNTdjMmEyIiwiZXhwaXJhdGlvbiI6IjIwMTQtMDEtMDEgMDA6MDAiLCJhbW91bnQiOjMyNy43OCwiY3VycmVuY3kiOiJSVVIiLCJkZXNjcmlwdGlvbiI6InByb2JhIiwicmVmZXJlbmNlIjoiMTIzNDU2Nzg5Iiwic3VjY2Vzc191cmwiOiJodHRwOlwvXC90ZXN0LnJ1XC9zdWNjZXNzIiwiZmFpbHVyZV91cmwiOiJodHRwOlwvXC90ZXN0LnJ1XC9mYWlsdXJlIiwibGFuZyI6InJ1In0=”`

sign:
>`“1a707959f3c7736637e488465beb8287”`

***



#### 3.1. Additional parameters (optional parameters)

| Parameter name  | Type  | Maximum length  | Description | Example  |
| ------------- | -------------  | :-------------: | -------------  | ------------  |
| pay_token  | String   | 255  | Token for generating recurring payments. The parameter is optional and depends on the settings of the payment gateway. Once received can be used with all merchant shops.   |    |
| params.flag_get_url| Integer  | 1 | 0 — Forming the payment page. 1 — Flag that returns the URL for redirecting the user, without generating a payment page. (Pre-creation of a session for order payment)   | 0 или 1.  (по умолчаню 0)  |
| params.ip | String   | -  | Customers ip adress| `192.168.0.1` |
| params.user_phone  | String   | -  | User phone number    | `79034211332` |
| params.verification_flag  | Integer   | 1  | Payment card verification sign. (Depends on the acquirer) 1 - After successful card verification, funds are automatically unblocked. |0 or 1. (default 0) |
| params.random_amount_flag  | Integer   | 1  | NOT OPERATIONAL. Sign of blocking a random amount for card verification. 1 - A random amount is blocked in the range from 0 to the value specified by the amount parameter   |  |
| params.pay_token_flag | Integer   | 1 | Sign of receiving a token for making recurring payments. Detailed description of item 10   | 0 or 1. (default 0) |

***

***Example of source JSON data for generating the data parameter:***

	{
	    "api_key":"c84f1ac0-e4f0-0131-5298-70921c57c2a2",
	    "expiration":"2014-01-01 00:00",
	    "amount":327.78,
	    "currency":"RUR",
	    "description":"proba",
	    "reference":"123456789",
	    "success_url":"http:\/\/test.ru\/success",
	    "failure_url":"http:\/\/test.ru\/failure",
	    "lang":"ru",
	    "params":{
		"flag_get_url":0,
		"user_id":"test_user",
		"user_email":"test@yandex.ru"
	    }
	}

As a result, the base64-encoded JSON document and signature (signature password "123") get the variables "data" and " sign»:

data:
>`“eyJhcGlfa2V5IjoiYzg0ZjFhYzAtZTRmMC0wMTMxLTUyOTgtNzA5MjFjNTdjMmEyIiwiZXhwaXJhdGlvbiI6IjIwMTQtMDEtMDEgMDA6MDAiLCJhbW91bnQiOjMyNy43OCwiY3VycmVuY3kiOiJSVVIiLCJkZXNjcmlwdGlvbiI6InByb2JhIiwicmVmZXJlbmNlIjoiMTIzNDU2Nzg5Iiwic3VjY2Vzc191cmwiOiJodHRwOlwvXC90ZXN0LnJ1XC9zdWNjZXNzIiwiZmFpbHVyZV91cmwiOiJodHRwOlwvXC90ZXN0LnJ1XC9mYWlsdXJlIiwibGFuZyI6InJ1IiwicGFyYW1zIjp7ImZsYWdfZ2V0X3VybCI6MCwidXNlcl9pZCI6InRlc3RfdXNlciIsInVzZXJfZW1haWwiOiJ0ZXN0QHlhbmRleC5ydSJ9fQ==”`

sign:
>`“1077355c7f6ff21e09d6a80143cb54bc”`

***

***Example of a response when using the flag_get_url = 1 parameter***

*Generating a unique URL for redirecting the user, without generating a payment page:*

	`"data":"eyJzZXNzaW9uX2lkIjoiMzA2LWJjZDM2ZjczYmIyMzExYzc0OTkzNjRjYzkxZGNhNTBkYTY5MGZhZDE3OGY1NGRiOWJhMjY3NDYzNWI3MCIsInVybCI6Imh0dHBzOi8vZnAua2ktdGVjaG5vbG9neS5ydS9wYXkvMzA2LWJjZDM2ZjczYmIyMzExYzc0OTkzNjRjYzkxZGNhNTBkYTY5MGZhZDE3OGY1NGRiOWJhMjY3NDYzNWI3MCIsImZsYWdfZ2V0X3VybCI6MSwic2hvcF9hcGlfa2V5IjoiMGE0ZGNhYzAtODUzZS0wMTMyLTkxZmEtMDAxYzE0MDExZGVjIn0=",
	"sign":"2218c4266c183e8448e7ebc500daa23d",
	"success":true,
	"time_proc":"0.0sec."}`

After base64 decoding of the "data" parameter, we get the structure:

	{
	    "session_id":"306-bcd36f73bb2311c7499364cc91dca50da690fad178f54db9ba2674635b70",
	    "url":"https://pay.mercuryo.io/pay/306-bcd36f73bb2311c7499364cc91dca50da690fad178f54db9ba2674635b70",
	    "flag_get_url":1,
	    "shop_api_key":"0a4dcac0-853e-0132-91fa-001c14011dec"
	}

To redirect the user we use the “url” parameter. This URL will render the payment form. This URL may be redirected to or opened within an iframe, so that the user doesn't leave merchant's website.

*Before decoding the request, it is recommended to verify the signature.*

***



#### 3.2. Rules for creating a signature (the sign parameter)

The signature is generated based on the Data string (base64 encoded parameter string) using the hash_hmac function. The signature is generated using the "MD5" algorithm.

	hash_hmac ('md5', $data, $secret_key)
	
>where:
>>$data - MIME base64 encoded string of the JSON document;

>>$secret_key - the store's password.
>>



### 4. Passing a feedback message between the payment gateway and the Partner's website

To confirm the payment, for all payment attempts by the payer, as well as when the order status is changed, an inter-server feedback message is sent from the payment gateway to the Partner's website. 
The data is transmitted by an HTTP POST request to the Partner's website, where:
>data - MIME base64 encoded string of the JSON document;

>sign - signature that is generated from the data string using the hash_hmac function

***

***Example:***

`[data] => eyJ0cmFuc2FjdGlvbl9pZCI6IjE5NyIsInJlZmVyZW5jZSI6IjEyMzQ1Njc4OTExMTMiLCJhcGlfa2V5IjoiYzg0ZjFhYzAtZTRmMC0wMTMxLTUyOTgtNzA5MjFjNTdjMmEyIiwiYW1vdW50IjoiMzI3Ljc4IiwiY3VycmVuY3kiOiJSVVIiLCJzdGF0dXMiOiI5OSIsInN5c3RlbV9hbW91bnQiOiIzNzIuNDgiLCJzeXN0ZW1fY3VycmVuY3kiOiJSVVIiLCJjb21taXNzaW9uIjoiNDQuNyIsInBheW1lbnRfc3lzdGVtX2lkIjoiOSIsInBheW1lbnRfc3lzdGVtX25hbWUiOiJBbGZhYmFuayBSVSIsImNhcmRfbnVtYmVyIjoiNDExMTExKioqKioqMTExMSIsInByb2Nlc3NpbmdfZXJyb3JfbXNnIjoiVERTX0FVVEhfRkFJTEVEIn0=`

`[sign] => e7af00ae7ddb451546ab6a4022333311`
    
*Accordingly, signature verification:*

	hash_hmac('md5', $_POST[' data '], $secret_key) === $_POST[' sign ']

>where:

>>$secret_key - store password;

*Basic, MIME base64 decoded, JSON document:*

	print_r(json_decode(base64_decode($_POST[' data '])));
	stdClass Object
	(
	    [transaction_id] => 197
	    [reference] => 1234567891113
	    [api_key] => c84f1ac0-e4f0-0131-5298-70921c57c2a2
	    [amount] => 327.78
	    [currency] => RUR
	    [status] => 99
	    [system_amount] => 372.48
	    [system_currency] => RUR
	    [commission] => 44.7
	    [payment_system_id] => 9
	    [payment_system_name] => Alfabank RU
	    [card_number] => 411111******1111
	    [processing_error_msg] => Wrong auth
	)

***

#### 4.1. Description of the parameters of the feedback document

| Parameter name  | Type  | 
| ------------- | -------------  |
| transaction_id   | Unique identifier of the transaction in the payment gateway system.   |
| reference   | Unique order number in the store system.   |
| api_key  | Key (identifier) of the store.   |
| amount  | Base price for the payment.   |
| сurrency  | Currency in which the product price is indicated. (3-digit alphabetic ISO 4217 currency code). Examples of values: UAH, RUR, EUR, USD|
| status  | Processing status: 0 - the transaction was created (not processed, the user was redirected to complete the payment, for example, 3DS verification); 1 - successful authorization (blocking funds); 2 - successfully unlocking funds; 3 - successful payment (debiting funds); 4 - successful refund; 7 - error unlocking funds from the issuer 8 - processing is incomplete. This status is generated in case of temporary problems during processing on the provider's side. The current status of the provider is checked and the result of the transaction processing is sent by a feedback message between the payment gateway and the partner's website; 99 - processing error (unsuccessful payment).|
| system_amount  | Total price paid by the User, taking into account the commission of the payment system.   |
| system_currency  | Currency of the payment system.   |
| commission  | Value of the payment system commission (in system_currency).  |
| payment_system_id  | Payment system identifier.  |
| payment_system_name  | Payment system name.   |
| payment_system_type  | Payment system type   |
| card_number  | Incomplete card number (Availability depends on the type of connection and acquirer).  |
| pan6  | Card bin   |
| pan4  | Last 4 digits of the card number   |
| cardholder_name  | Cardholder's name (Availability depends on the type of connection and acquirer)   |
| processing_error_msg  | Payment error (in case of processing status - 99).   |
| external_error_id  | List of possible external_error_id`s [available here](https://github.com/mercuryoio/Mercuryo-Pay-Docs/blob/master/external_error_id%20list.xlsx "external_error_id list.xlsx")  |
| authorization_code  | Payment system authorization code. The parameter is optional   |
| pay_token  | Token for generating recurring payments. The parameter is optional and depends on the settings of the payment gateway   |
        
In response to the transmission of a feedback message from the Partner's website, the following string must be returned: **OK**
If the Partner's website does not return the "OK" string, the payment gateway will re-generate the feedback message.

***
	
***An example of a PHP implementation of handling a feedback request from a partner's website:***

	<?php 
	if(isset($_POST['data']) && isset($_POST['sign'])){
		$secret_key = '3a81bd912a82b4a2ed479b398fbb8445140c730f71080f06'; // store password

		//signature verification
		if(hash_hmac('md5', $_POST['data'], $secret_key) === $_POST['sign']){

			$data = json_decode(base64_decode($_POST['data']));
			if($data->status == 0){
				//Transaction initiation
			}elseif($data->status == 3){
				//successful payment (debiting of funds)
			}elseif($data->status == 99){
				//error
			}


		}

	}
	?>
	
***
	
For a single partner order, several transactions can be generated from the payment gateway.

>An example is several unsuccessful attempts to pay with a payment card, when the user made a mistake when entering data on the card, or the cardholder has a restriction from the issuing Bank. 
The user can contact their Bank to remove restrictions on the operation and repeat the payment on the payment gateway’s side without redirecting to the partner's site. 
Accordingly, one order can have several transactions with the error status and, as a result, with the successful payment status.



### 5. Request information on transactions (detailed information on selected orders or transactions)

Request for a URL https://pay.mercuryo.io/pay/get_orders_data (POST) two parameters are passed:
- data - MIME base64 encoded string of the JSON document;

- sign - signature, which is formed on the basis of the data string using the hash_hmac function to confirm the validity of the data.

***

***Example of implementation in PHP:***

	<?php 
	$secret_key = '3a81bd912a82b4a2ed479b398fbb8445140c730f71080f06'; // store password
	$order_data = array(
	     'api_key' => '16946ea0-7bfc-0132-b820-1cb65445527e', 
	     'transaction_id' => '2', 
	);
	$data = base64_encode(json_encode($order_data));
	$sign = hash_hmac('md5', $data, $secret_key); //signature generation

	$curl = curl_init(); 
	curl_setopt($curl, CURLOPT_URL, https://pay.mercuryo.io/pay/get_orders_data');
	curl_setopt($curl,CURLOPT_RETURNTRANSFER,true); 
	curl_setopt($curl,CURLOPT_POST,true);
	curl_setopt($curl,CURLOPT_POSTFIELDS,array('data' => $data, 'sign' => $sign)); 

	if($result = curl_exec($curl)){
		$result = json_decode($result);
		if($result->success && $result->data && $result->sign && hash_hmac('md5', $result->data, $secret_key) === $result->sign){

			$result = json_decode(base64_decode($result->data));
			// Processing result
			print_r($result);

		}
	}
	curl_close($curl);
	?>

***

| Parameter name  | Description  | Example  |
| ------------- | -------------  | ------------- | 
| api_key  | Store key (identifier)   | c84f1ac0-e4f0-0131-5298-70921c57c2a2   |
| transaction_id **OR** reference | Transaction identifier in the https://pay.mercuryo.io/ system, by which information should be requested **OR** Unique order number in the store system | 2231  |

***

***Result of processing is a JSON structure:***

`{"data":"eyJpdGVtcyI6W3sidHJhbnNhY3Rpb25faWQiOiIyIiwicmVmZXJlbmNlIjoi\nNTMwNjY1OCIsImFwaV9rZXkiOiIxNjk0NmVhMC03YmZjLTAxMzItYjgyMC0x\nY2I2NTQ0NTUyN2UiLCJhbW91bnQiOiI1NzYuMjUiLCJjdXJyZW5jeSI6IkVV\nUiIsInN0YXR1cyI6IjMiLCJzdGF0dXNfbmFtZSI6InN1Y2Nlc3MiLCJzeXN0\nZW1fYW1vdW50IjoiNjM3LjI0Iiwic3lzdGVtX2N1cnJlbmN5IjoiRVVSIiwi\nY29tbWlzc2lvbiI6IjYwLjk5IiwicGF5bWVudF9zeXN0ZW1faWQiOiIxIiwi\ncGF5bWVudF9zeXN0ZW1fbmFtZSI6IkRlZmF1bHQgUGF5bWVudCBTeXN0ZW0i\nLCJjYXJkX251bWJlciI6IjQwMTIwMCoqKioqKjg4ODQiLCJwcm9jZXNzaW5n\nX2Vycm9yX21zZyI6IiJ9XX0=\n",
"sign":"5c800b95b97238dce6fb108514465dce",
"success":true,"time_proc":"0.04sec."}`

***

As a result of decoding the data parameter, we get an array of elements with a structure similar to the feedback message between the payment gateway and the Partner's website [paragraph 4](/Pay-API-EN.md#2-request-parameters):

`stdClass Object ( [items] => Array ( [0] => stdClass Object ( [transaction_id] => 2 [reference] => 5306658 [api_key] => 16946ea0-7bfc-0132-b820-1cb65445527e [amount] => 576.25 [currency] => EUR [status] => 3 [status_name] => success [system_amount] => 637.24 [system_currency] => EUR [commission] => 60.99 [payment_system_id] => 1 [payment_system_name] => Default Payment System [card_number] => 401200******8884 [processing_error_msg] => ) ) )`



### 6. Formation of cardholder data on the merchant side (PCI DSS certificate required)

Request for URL https://pay.mercuryo.io/pay/direct (POST) is passed two parameters:
- data - the MIME base64 encoded string of the JSON document;
- sign - a signature that is generated from the data string using the hash_hmac function to confirm the validity of the data.

To form a request, parameters described in [paragraph 3](/Pay-API-EN.md#3-basic-parameters) are used as well as cardholder data.

| Parameter name  | Type  | Maximum length  | Description | Example  |
| ------------- | -------------  | :-------------: | -------------  | ------------  |
| card_data.pan  | String   | 16  | Payment card number  | 4111111111111111   |
| card_data.cvv  | Integer   | 3  | CVV-code   | 123 |
| card_data.exp_month  | Integer   | 2  | Card expiration month (1-12)   | 12   |
| card_data.exp_year  | Integer   | 4  | Card expiration year   | 2025  |
| card_data.cardholder  | String   | 255  | Cardholder name  | Ivan Petrov   |

***

***Еxample of initial JSON data to form the data parameter:***

	{
	    "api_key":"c84f1ac0-e4f0-0131-5298-70921c57c2a2",
	    "expiration":"2014-01-01 00:00",
	    "amount":327.78,
	    "currency":"RUR",
	    "description":"proba",
	    "reference":"123456789",
	    "success_url":"http:\/\/test.ru\/success",
	    "failure_url":"http:\/\/test.ru\/failure",
	    "lang":"ru",
	    "params":{
	    	"flag_get_url":1,
	    	"user_id":"test_user",
	    	"user_email":"test@yandex.ru",
	    	"ip":"192.168.1.1"},
	    "card_data":{
		"pan":"4111111111111111",
		"cvv":123,
		"exp_month":12,
		"exp_year":2018,
		"cardholder":"Ivan Petrov"
	    }
	}

As a result of base64 coding of JSON document and signature (password for signing “123”) we get the variables «data» and «sign»

data:
>`“eyJhcGlfa2V5IjoiYzg0ZjFhYzAtZTRmMC0wMTMxLTUyOTgtNzA5MjFjNTdjMmEyIiwiZXhwaXJhdGlvbiI6IjIwMTQtMDEtMDEgMDA6MDAiLCJhbW91bnQiOjMyNy43OCwiY3VycmVuY3kiOiJSVVIiLCJkZXNjcmlwdGlvbiI6InByb2JhIiwicmVmZXJlbmNlIjoiMTIzNDU2Nzg5Iiwic3VjY2Vzc191cmwiOiJodHRwOlwvXC90ZXN0LnJ1XC9zdWNjZXNzIiwiZmFpbHVyZV91cmwiOiJodHRwOlwvXC90ZXN0LnJ1XC9mYWlsdXJlIiwibGFuZyI6InJ1IiwiY2FyZF9kYXRhIjp7InBhbiI6IjQxMTExMTExMTExMTExMTEiLCJjdnYiOjEyMywiZXhwX21vbnRoIjoxMiwiZXhwX3llYXIiOjIwMTgsImNhcmRob2xkZXIiOiJJdmFuIFBldHJvdiJ9fQ==”`

sign:
>`“ce38d2fbca398e602e2e20ca63c770e8”`

***

***Example of a decoded response in case of successful payment (not 3DS):***

	{
	    "order""=>"{
		"transaction_id"=>40850,
		"reference""=>""test",
		"external_reference""=>""a1ec7d00-4bea-7584-a1ec-7d0000002924",
		"api_key""=>""a696f360-21ee-0135-ccb8-4c32759179bf",
		"amount""=>""1.0",
		"currency""=>""RUR",
		"status"=>3,
		"status_name""=>""success",
		"system_amount""=>""1.0",
		"commission""=>""0.0",
		"system_currency""=>""RUR",
		"payment_system_id"=>18,
		"payment_system_name""=>""Alfabank REST",
		"card_number""=>""555555******5599",
		"cardholder""=>""test",
		"processing_error_msg""=>nil",
		"authorization_code""=>""123456",
		"card_type""=>""MASTERCARD",
		"success_url""=>""http://ya.ru",
		"failure_url""=>""http://localhost:3000/pay"
	    },
	    "success""=>true",
	    "time_proc""=>""14.78 sec.",
	    "request_id""=>""9563185958"
	}

***An example of a decoded response in case of unsuccessful payment (not 3DS):***

	{
	    "order""=>"{
		"transaction_id"=>40853,
		"reference""=>""test123",
		"external_reference""=>""92ecbe1a-dbc9-7459-92ec-be1a00002924",
		"api_key""=>""a696f360-21ee-0135-ccb8-4c32759179bf",
		"amount""=>""1.0",
		"currency""=>""RUR",
		"status"=>99,
		"status_name""=>""error",
		"system_amount""=>""1.0",
		"commission""=>""0.0",
		"system_currency""=>""RUR",
		"payment_system_id"=>18,
		"payment_system_name""=>""Alfabank REST",
		"card_number""=>""555555******5599",
		"cardholder""=>""test",
		"processing_error_msg""=>""Operation declined. Please check the data and available balance of the card.",
		"authorization_code""=>nil",
		"card_type""=>""MASTERCARD",
		"success_url""=>""http://ya.ru",
		"failure_url""=>""http://localhost:3000/pay",
		"processing_error_group_reference""=>nil",
		"processing_error_kind"=>0
	    },
	    "success""=>false",
	    "error""=>""Operation declined. Please check the data and available balance of the card.",
	    "time_proc""=>""9.74 sec.",
	    "request_id""=>""9563185958",
	    "error_msg""=>""Operation declined. Please check the data and available balance of the card."
	}

***An example of a decoded response if it is necessary to redirect the payer to the URL for entering 3DS:***

	{
	    "order""=>"{
		"transaction_id"=>40854,
		"reference""=>""test123",
		"external_reference""=>""c16cf438-6092-7bee-c16c-f43800002924",
		"api_key""=>""a696f360-21ee-0135-ccb8-4c32759179bf",
		"amount""=>""1.0",
		"currency""=>""RUR",
		"status"=>0,
		"status_name""=>""created",
		"system_amount""=>""1.0",
		"commission""=>""0.0",
		"system_currency""=>""RUR",
		"payment_system_id"=>18,
		"payment_system_name""=>""Alfabank REST",
		"card_number""=>""411111******1111",
		"cardholder""=>""test",
		"processing_error_msg""=>nil",
		"authorization_code""=>nil",
		"card_type""=>""VISA",
		"success_url""=>""http://ya.ru",
		"failure_url""=>""http://localhost:3000/pay"
	    },
	    "success""=>true",
	    "url""=>""http://localhost:3010/redirect/?link_reference=2118437f5e317d505f5b76d09415edf5",
	    "time_proc""=>""7.7 sec.",
	    "request_id""=>""9563185958"
	}

*If 3DS verification is required, the response will return the url attribute to redirect the user to.*



### 7. Requesting a report on transactions for a period of time

Request for URL https://pay.mercuryo.io/pay/get_report (POST) is passed two parameters:
- data - the MIME base64 encoded string of the JSON document;
- sign - a signature that is generated from the data string using the hash_hmac function to confirm the validity of the data.

| Parameter name  | Type  | Maximum length  | Description | Example  |
| ------------- | -------------  | :-------------: | -------------  | ------------  |
| api_key | String   | 255  | Store key (identifier)  | c84f1ac0-e4f0-0131-5298-70921c57c2a2    |
| time_start (optional)  | String   | -  | Range start time in the format yyyy-mm-dd HH: MM | 2014-07-21 12:44 |
| time_end (optional)  | String   | -  | Range end time in the format yyyy-mm-dd HH: MM | 2014-07-21 12:45   |
| status (optional)  | Integer   | 1  | Processing status: 0 - the transaction was created (not processed, the user was redirected to complete the payment, for example, 3DS verification); 1 - successful authorization (blocking funds); 2 - successfully unlocking funds; 3 - successful payment (debiting of funds); 4 - successful refund; 7 - error in unblocking funds from the issuer; 99 - processing error (unsuccessful payment).| 99  |
| page (optional)  | Integer   | -  | Page number. (The maximum number of items per page is 1000!)  | 1   

***

***Example of a decoded response:***

		{
		    "items":[
			{
			    "id":1158,
			    "created_at":"2015-09-17T12:04:35.466+03:00",
			    "reference":"1442480562",
			    "status":1,
			    "system_amount":"327.78",
			    "system_currency":"RUR"
			}
		    ],
		    "total_count":1,
		    "pages":1,
		    "success":true,
		    "time_proc":"0.1sec."
	}

***



### 8. Refund request

Request for URL https://pay.mercuryo.io/pay/refund (POST) is passed two parameters:
- data - the MIME base64 encoded string of the JSON document;
- sign - a signature that is generated from the data string using the hash_hmac function to confirm the validity of the data.

***

***Example of initial JSON data to form the data parameter:***

	{
	    "api_key":"318dacc0-c571-0132-d48f-448a5b8820f8",
	    "transaction_id":"2299",
	    "amount":100.80
	}

*This operation is possible for transactions in **status 3 (success) or 4 (refund)***

***

| Parameter name  | Type  | Maximum length  | Description | Example  |
| ------------- | -------------  | :-------------: | -------------  | ------------  |
| api_key | String   | 255  | Store key (identifier)  | c84f1ac0-e4f0-0131-5298-70921c57c2a2     |
| transaction_id  | Integer   | -  | Transaction ID in the system https://pay.mercuryo.io/ | 12345678 |
| amount (optional, if there is no parameter, the refund is made for the entire transaction amount)  | Integer   | -  | Refund amount in the currency of the payment system | 100.40   |

***

***Example of a decoded response:***

	{
	    "order":{
		"transaction_id":2299,
		"reference":"1473747103",
		"api_key":"318dacc0-c571-0132-d48f-448a5b8820f8",
		"amount":"327.78",
		"currency":"RUR",
		"status":4,
		"status_name":"refund",
		"system_amount":"327.78",
		"commission":"0.0",
		"system_currency":"RUR",
		"payment_system_id":1,
		"payment_system_name":"AlfabankB2b (TEST)",
		"card_number":"411111******1111",
		"processing_error_msg":null
	    },
	    "success":true,
	    "time_proc":"1.2sec."
	}
	
***



### 9. Request for discarding/unholding funds

Request for URL https://pay.mercuryo.io/pay/void (POST) is passed two parameters:
- data - the MIME base64 encoded string of the JSON document;
- sign - a signature that is generated from the data string using the hash_hmac function to confirm the validity of the data.

***

***Example of initial JSON data to form the data parameter:***

	{
	    "api_key":"318dacc0-c571-0132-d48f-448a5b8820f8",
	    "transaction_id":"2299"
	}

*This operation is possible for transactions in **status 1 (authorize(lock amount))***

| Parameter name  | Type  | Maximum length  | Description | Example  |
| ------------- | -------------  | :-------------: | -------------  | ------------  |
| api_key | String   | 255  | Store key (identifier)  | c84f1ac0-e4f0-0131-5298-70921c57c2a2     |
| transaction_id  | Integer   | -  | Transaction ID in the system https://pay.mercuryo.io/ | 12345678 |

***

***Example of decoded response:***

	{
	    "order":{
		"transaction_id":2299,
		"reference":"1473747103",
		"api_key":"318dacc0-c571-0132-d48f-448a5b8820f8",
		"amount":"327.78",
		"currency":"RUR",
		"status":2,
		"status_name":"void(unlock amount)",
		"system_amount":"327.78",
		"commission":"0.0",
		"system_currency":"RUR",
		"payment_system_id":1,
		"payment_system_name":"AlfabankB2b (TEST)",
		"card_number":"411111******1111",
		"processing_error_msg":null
	    },
	    "success":true,
	    "time_proc":"1.2sec."
	}
	
***



### 10. Request for withdrawal of held funds(funds put on hold)

Request for a URL https://pay.mercuryo.io/pay/complete (POST) two parameters are passed:
- data - MIME base64 encoded string of the JSON document;
- sign - signature, which is formed on the basis of the data string using the hash_hmac function to confirm the validity of the data.

***

***Example of initial JSON data to form the data parameter:***

	{
	    "api_key":"318dacc0-c571-0132-d48f-448a5b8820f8",
	    "transaction_id":"2299"
	}

This operation is possible for transactions in **status 1 (authorize(lock amount))**

***

| Parameter name  | Type  | Maximum length  | Description | Example  |
| ------------- | -------------  | :-------------: | -------------  | ------------  |
| api_key | String   | 255  | Store key (identifier)  | c84f1ac0-e4f0-0131-5298-70921c57c2a2     |
| transaction_id  | Integer   | -  | Transaction ID in the system https://pay.mercuryo.io/ | 12345678 |

***

***Example of decoded response:***

	{
	    "order":{
		"transaction_id":2299,
		"reference":"1473747103",
		"api_key":"318dacc0-c571-0132-d48f-448a5b8820f8",
		"amount":"327.78",
		"currency":"RUR",
		"status":3,
		"status_name":"success",
		"system_amount":"327.78",
		"commission":"0.0",
		"system_currency":"RUR",
		"payment_system_id":1,
		"payment_system_name":"AlfabankB2b (TEST)",
		"card_number":"411111******1111",
		"processing_error_msg":null
	    },
	    "success":true,
	    "time_proc":"1.2sec."
	}

***



### 11. Recurring payments (regular, recurring)

**Recurring payments** are payments that do not require re-entering the card details. The first payment is made with the input of all card details, subsequent payments are made without entering the card details and without the participation of the card holder. By default, this functionality is disabled for the seller.

When making a payment, the merchant can pass a **special parameter (params.pay_token_flag)**, which means that this payment is the first payment in the queue of recurring payments. If the payment is made successfully, the seller will receive a token in the feedback message [paragraph 4](/Pay-API-EN.md#4-passing-a-feedback-message-between-the-payment-gateway-and-the-partners-website) to generate subsequent recurring payments.

All subsequent (recurring) requests are sent to the URL https://pay.mercuryo.io/pay/recurrent. 

The request parameters are similar to the description in [paragraph 3](/Pay-API-EN.md#3-basic-parameters), taking into account the need to transfer the recurring payment token "pay_token".

Testing of recurring payments can be carried out with the card without 3Ds

### 12. Test cards

Card number: 4111111111111111 **OR** 5555555555555599

Expiry date: 12/25

Cardholder: Ivan Petrov

CVV for successful payment withouth redirect to 3DS: 123

CVV for successful payment with redirect to 3DS (password is not entered): 333 

CVV for failure payment: 222

### 13. Pay token for Customer Initiated Transactions

This feature allows to generate pay token after the first transaction in order the customer doesn't need to fill in all card details for subsequent transactions with the same card (except for CVV/CVC code). 

For subsequent transactions a simple payment form will be showed that contains masked pan number and a field for CVV/CVC code.

For the first payment, the merchant can pass a special parameter (params.pay_token_flag) in request to the end-point https://pay.mercuryo.io/pay/, which means that this payment is the first one. In response the pay token will be sent back.

For subsequent transactions you should additionally add *pay_token* and *params.flag_get_url* to the request. Refer to [paragraph 3.1](Pay-API-EN.md#31-additional-parameters-optional-parameters)

### FAQ

#### Ways of integration
1. "iFrame".

For the payment the customer is being redirected to pay.mercuryo servers (either directly or within an iframe), where he inserts all the card data and the whole payment is being processed there. Merchant's server only receives a callback of a successful/failed transaction afterwards.
For that way merchant may customize the payment form design in the admin panel. Refer to [Paragraph 2 and 3](/Pay-API-EN.md#2-request-parameters)
*This way is viable for non-PCIDSS merchants.*

2. Host-to-host.

The customer remains on merchant's server at all times. The payment form is designed by merchant. Merchant's server only submits to pay.mercurio the raw transaction data. The whole payment flow is being executed on merchant's end.
For that way merchant is free to design the payment form. The form in the admin panel has nothing to do with this way of integration (therefore there's no way to download it via a js file, because it was not designed for that purpose). Refer to [Paragraph 6](/Pay-API-EN.md#6-formation-of-cardholder-data-on-the-merchant-side-pci-dss-certificate-required)
*This way is viable only for PCIDSS-compliant merchants.*

#### Payment form customization

In case of an 'iFrame' integration there are two ways of approaching the look of payment form:

1. Supply the admin-panel-generated webform with a CSS file of the merchant, which may slightly alter colours, elements positioning etc. Once the CSS file is ready, pay.mercuryo can bind it to merchant's form via mercuryo's backend admin panel. *This is a preferable variant.*

2. Merchant prepairs custom HTML layout via parsing the admin-panel-generated form and customizing it the way it likes. Then pay.mercuryo may upload it there on backend. Any customization via admin panel will become unavailable then.

It is not possible to customise the 3DS verification form veiw because it's generated on the bank issure side.

#### Transaction handling

There can be 2 modes of handling transactions: single stage and double stage.

1-stage (default) mode means that the transaction will go the following simple way:
- user enters payment data
- 3DS
- bank makes funds withdrawal
- user gets the success screen

2-stage mode means the flow will go other way:
- user enters payment data
- 3DS
- bank makes authorization of funds (hold)
- user gets the success screen
- if merchant decides to deliver the goods/services to the customer, merchant makes capture of funds via API request from merchant's end
- bank makes funds withdrawal
