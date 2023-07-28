<p align="center">
  <img src="https://raw.githubusercontent.com/jatin-uengage/api_documetation/main/header_logo.png" alt="Prism Logo" width="600">
	
</p>

<h1 align="center">Uengage Prism CRM API Integration Doc</h1>

## Base URL
https://crm.uengage.in/

## Authentication

Include the authentication token in the request header to access the API.

## Authentication

Include the authentication token in the request header to access the API.


### Request Headers

| Header          | Value                        |
| --------------- | ---------------------------- |
| `Authorization` | `Your_Authentication_Token`  |
| `Content-Type`  | `application/json`           |


## 1. Order Push

This API allows you to push orders to the uEngage CRM system for processing and tracking.


## Request JSON

### Endpoint

POST /api/ingestion/orderPush

### Parameters

| Parameter       | Data Type | Required | Definition                                     |
| --------------- | --------- | -------- | ---------------------------------------------- |
| `outlet_code`   | String    | Yes      | Unique Merchant Mapping Code provided by the POS Partner |
| `order_payload` | Array     | Yes      | An array containing the order details from all sources and aggregators |

#### `order_payload` Object

| Parameter       | Data Type | Required | Definition                                       |
| --------------- | --------- | -------- | ------------------------------------------------ |
| `orderid`       | Integer   | Yes      | Unique order ID                                  |
| `orderDate`     | String    | Yes      | Date of the order (format: DD/MM/YYYYTHH:mm:ss)  |
| `customer_details` | Object  | Yes       | Customer details object  |
| `order_details` | Object    |Yes      | Order details object  |
| `product_details`  | Array     | Yes      | Array of item details  |

#### `customer_details` Object

| Parameter       | Data Type | Required | Definition                                       |
| --------------- | --------- | -------- | ------------------------------------------------ |
| `name`          | String    | Yes     | Customer's name                                  |
| `mobile_no`     | String    | Yes     | Customer's mobile number                        |
| `email`         | String    | No       | Customer's email address                        |
| `dob`           | String    | No       | Customer's date of birth (format: DD/MM/YYYY)   |
| `anniversary`   | String    | No       | Customer's anniversary date (format: DD/MM/YYYY) |


#### `order_details` Object

| Parameter                 | Data Type | Required | Definition                                           |
| ------------------------- | --------- | -------- | ---------------------------------------------------- |
| `orderType`               | String    | Yes
| Order type (DineIn/Takeaway/Delivery)               |
| `paymentMode`             | String    | Yes      | Payment mode (Online/Cash)                          |
| `subTotal`                | Number    | No       | Subtotal of the order                               |
| `taxes`                   | Array     | No       | Array of tax objects (name and value)               |
| `discount`                | Number    | No       | Discount applied to the order                       |
| `offerUsed`               | String    | No       | Name of the offer used in the order                 |
| `delivery_charges`        | Number    | No       | Delivery charges for the order                      |
| `delivery_charges_tax`    | Number    | No       | Tax applied on delivery charges                     |
| `packaging_charges`       | Number    | No       | Packaging charges for the order                     |
| `packaging_charges_tax`   | Number    | No       | Tax applied on packaging charges                    |
| `total`                   | Number    | Yes       | Total amount of the order                           |
| `advance_order`           | String    | No       | Date and time (format: DD/MM/YYYYTHH:mm:ss)         |

#### `product_details` Array

| Parameter                 | Data Type | Required | Definition                                           |
| ------------------------- | --------- | -------- | ---------------------------------------------------- |
| `productName`             | String    | Yes      | Name of the item                                    |
| `qty`                     | Number    | Yes      | Quantity of the item                                |
| `productPrice`      	    | Number    | Yes      | Price of the item                                   |
| `taxes`                   | Number    | No       | Tax applied on the item                             |
| `discount`                | Number    | No       | Discount applied to the item                        |
| `total`                   | Number    | Yes       | Total amount for the item                           |

### Example Request JSON

```json
{
  "outlet_code": "unique outlet code",
  "order_payload": {
    "orderid": 123456,
    "orderDate": "11/11/2000T18:30:00",
    "customer_details": {
      "name": "Anand Kapoor",
      "mobile_no": "8979184484",
      "email": "anand@gmail.com",
      "dob": "11/11/2000",
      "anniversary": "11/11/2000"
    },
    "order_details": {
      "orderType": "DineIn/Takeaway/Delivery",
      "paymentMode": "Online/Cash",
      "subTotal": 100.00,
      "taxes": [
        {
          "name": "GST",
          "value": 5.00
        }
      ],
      "discount": 50.00,
      "offerUsed": "Offer Name",
      "delivery_charges": 50,
      "delivery_charges_tax": 9,
      "packaging_charges": 10,
      "packaging_charges_tax": 1.8,
      "total": 175.80,
      "advance_order": "null or 11/11/2000T18:30:00"
    },
    "product_details": [
      {
        "productName": "Pizza",
        "qty": 1,
        "productProduct": 145.00,
        "taxes": 5.00,
        "discount": 50.00,
        "total": 175.80
      }
    ]
  }
}
```

### Response JSON (Success)
```json
{
	"status": 1,
	"msg": "Success"
}
```

### Response JSON (Error)
```json
{
	"status": 0,
	"error_code": "uE_101",
	"error_message": "Authentication Failed"
}
```
**Key**
| Data Type | Definition             |
| --------- | ---------------------- |
| `status`  | Integer - Return status |
| `msg`     | String - Return message |
| `error_code` | String - Error code   |
| `error_message` | String - Error message |


**Failure Reason**

| Error Code | Reason                  |
| ---------- | ----------------------- |
| `uE_101`   | Authentication Failed   |
| `uE_102`   | Invalid Outlet Code     |
| `uE_103`   | Error While Parsing Menu|


## 2. Check Loyalty Rewards

This API allows you to check loyalty rewards for a customer based on their mobile number, outlet code.

### Endpoint

POST /api/checkLoyaltyRewards

### Parameters

| Parameter          | Data Type | Required | Definition                                     |
| ------------------ | --------- | -------- | ---------------------------------------------- |
| `outlet_code`      | String    | Yes      | Unique Merchant Mapping Code provided by the POS Partner |
| `customer_mobileNo` | String    | Yes      | Customer's mobile number                      |
| `order_total`       | Number    | Yes      | Total order amount                            |

### Example Request JSON for Check Loyalty Rewards

```json
{
	"outlet_code": "unique outlet code",
	"customer_mobileNo": "xxxxxxxxxx",
	"order_total": 190
}
```

### Response JSON (Success)  

```json
{
	"status": 1,
	"msg": "Success",
	"total_points" : 89,
	"redeemable_points" : 60,
	"redeemable_offers" : [{
        "offerId" : 2,
        "offerName" : "70% Off on online Payment"
    },
    {
        "offerId" : 7,
        "offerName" : "20% Off on Cakes"
    }
    ],
	"loyalty_rules" : "40% of Rs. 89 can be used"
}

```


### Response JSON (Error)

```json
{
	"status": 0,
	"error_code": "uE_101",
	"error_message": "Authentication Failed"
}
```

**Key**
| Data Type | Definition             |
| --------- | ---------------------- |
| `status`  | Integer - Return status |
| `msg`     | String - Return message |
| `error_code` | String - Error code   |
| `error_message` | String - Error message |

**Failure Reason**

| Error Code | Reason                  |
| ---------- | ----------------------- |
| `uE_101`   | Authentication Failed   |
| `uE_102`   | Invalid Outlet Code     |
| `uE_103`   | Error While Parsing Menu|

## 3. Redeem Loyalty Points

This API allows you to redeem loyalty points or offers for a customer based on their mobile number, outlet code, and order details.

### Endpoint

POST /api/redeemLoyaltyRewards

### Parameters

| Parameter          | Data Type | Required | Definition                                     |
| ------------------ | --------- | -------- | ---------------------------------------------- |
| `outlet_code`      | String    | Yes      | Unique Merchant Mapping Code provided by the POS Partner |
| `redeemType`        | String    | Yes      | Type of loyalty reward (points/offers)         |
| `redeemOfferId`        | String    | Yes*      | *Required in case of redeemType offers*     |
| `customer_mobileNo` | String    | Yes      | Customer's mobile number                      |
| `order_total`       | Number    | Yes      | Order total on which rules will be applicable |

### Example Request JSON for Redeem Loyalty Points

```json
{
	"outlet_code": "unique outlet code",
	"redeemType" : "points/offers",
	"redeemOfferId" : "12309",
	"customer_mobileNo": "xxxxxxxxxx",
	"order_total": 80
}
```

### Response JSON (Success)

```json
{
	"status": 1,
	"msg": "Redeemed"
}
```

### Response JSON (Error)

```json
{
	"status": 0,
	"error_code": "uE_101"
}
```

**Key**
| Data Type | Definition             |
| --------- | ---------------------- |
| `status`  | Integer - Return status |
| `msg`     | String - Return message |
| `error_code` | String - Error code   |

**Failure Reason**

| Error Code | Reason                  |
| ---------- | ----------------------- |
| `uE_101`   | Authentication Failed   |
| `uE_102`   | Invalid Outlet Code     |
| `uE_108`   | Invalid Customer Mobile No |
| `uE_109`   | Invalid Offer Id      |
