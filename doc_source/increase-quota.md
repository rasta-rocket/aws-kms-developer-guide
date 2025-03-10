# Requesting an AWS KMS quota change<a name="increase-quota"></a>

All AWS KMS quotas are adjustable, except for the [key policy document size resource quota](resource-limits.md#key-policy-limit) and the [custom key stores resource quota](resource-limits.md#cks-resource-quota)\. You can request an increase or decrease from your current quota\.

You might request an increase if you get frequent throttling exceptions, especially if you notice prolonged periods of throttling, and not just spikes\. You might request a quota decrease if you have a known bottleneck, such as a [custom key store](custom-key-store-overview.md) associated with a backing key store that cannot handle the current request rate\.

To request a quota increase, see [Requesting a quota increase](https://docs.aws.amazon.com/servicequotas/latest/userguide/request-increase.html) in the *Service Quotas User Guide*\. To request a quota decrease, to change a quota that is not listed in Service Quotas, or to change a quota in an AWS Region where Service Quotas for AWS KMS is not available, please visit [AWS Support Center](https://console.aws.amazon.com/support/home) and create a case\. 

**Note**  
AWS KMS custom key store quotas do not appear in the Service Quotas console\. You cannot view or manage these quotas by using Service Quotas API operations\.

## Using the Service Quotas console<a name="quota-increase-console"></a>

To request an increase in an AWS KMS quota, you can use the [Service Quotas console](https://console.aws.amazon.com/servicequotas)\. For instructions, see [Requesting a quota increase](https://docs.aws.amazon.com/servicequotas/latest/userguide/request-quota-increase.html) in the *Service Quotas User Guide*\.

You cannot use the Service Quotas console to request a quota decrease\. Instead, contact [AWS Support Center](https://console.aws.amazon.com/support/home) and create a case\.

1. For service name, choose **AWS Key Management Service \(AWS KMS\)**\.

1. Choose the quota name of the quota you want to change\. Use the detailed information about the quota to confirm that you have chosen the correct quota\. 

   You can search for the quota name in the Service Quotas console\. There are several pages of AWS KMS quotas\. You can also find the quota names and descriptions of AWS KMS quotas in the [resource quota](resource-limits.md) and [request quota](requests-per-second.md) tables\.

   For example, to request an increase to the `Cryptographic operations (symmetric) request rate` quota for cryptographic operations on symmetric encryption KMS keys and HMAC KMS keys, choose **Cryptographic operations \(symmetric\) request rate**\.

1. Choose **Request quota increase**\.

1. In the **Change quota value** box, type your desired quota value\. It must be greater than the **Applied quota value**\.

1. Choose **Request**\.

## Using the Service Quotas API<a name="quota-increase-api"></a>

To request an increase in an AWS KMS quota, you can use the [Service Quotas API](https://docs.aws.amazon.com/servicequotas/2019-06-24/apireference/)\. You cannot use the Service Quotas console to request a quota decrease\. Instead, contact [AWS Support Center](https://console.aws.amazon.com/support/home) and create a case\.

The `RequestServiceQuotaIncrease` operation, which submits the request, requires the quota code for the quota\. So begin by getting the quota code\.

1. Find the quota name of the quota you want to increase\. You can find the quota names and descriptions of AWS KMS quotas in the [resource quota](resource-limits.md) and [request quota](requests-per-second.md) tables\. 

1. To get the quota code for an AWS KMS quota, use the [ListServiceQuotas](https://docs.aws.amazon.com/servicequotas/2019-06-24/apireference/API_ListServiceQuotas.html) operation\.

   Set `ServiceCode` to `kms`\.

   The response includes the `QuotaName` and `QuotaCode` for each quota\. 

   For example, to get only the quota information for the `Cryptographic operations (RSA) request rate` quota, use a command like the following one\. It uses the `query` parameter in the AWS Command Line Interface \(AWS CLI\) to get only the quota with the specified quota name\.

   ```
   $ aws service-quotas list-service-quotas \
           --service-code kms \
           --query 'Quotas[?QuotaName==`Cryptographic operations (RSA) request rate`]'
   
   {
       "Quotas": [
           {
               "ServiceCode": "kms",
               "ServiceName": "AWS Key Management Service (AWS KMS)",
               "QuotaArn": "arn:aws:servicequotas:us-east-2:111122223333:kms/L-2AC98190",
               "QuotaCode": "L-2AC98190",
               "QuotaName": "Cryptographic operations (RSA) request rate",
               "Value": 500,
               "Unit": "None",
               "Adjustable": true,
               "GlobalQuota": false
           }
       ]
   }
   ```

1. To request an increase for an AWS KMS quota, use the [RequestServiceQuotaIncrease](https://docs.aws.amazon.com/servicequotas/2019-06-24/apireference/API_RequestServiceQuotaIncrease.html) operation\. To identify the quota, use the quota code\.

   For example, the following command requests an increase in the `Cryptographic operations (RSA) request rate` quota to `700` requests per second\. It uses the required quota code, `L-2AC98190`, to identify the quota\.

   If the command completes successfully, the `Status` field displays the current status of the request\. To get the updated status of the request, use the [GetRequestedServiceQuotaChange](https://docs.aws.amazon.com/servicequotas/2019-06-24/apireference/API_GetRequestedServiceQuotaChange.html), [ListRequestedServiceQuotaChangeHistory](https://docs.aws.amazon.com/servicequotas/2019-06-24/apireference/API_ListRequestedServiceQuotaChangeHistory.html) or [ListRequestedServiceQuotaChangeHistoryByQuota](https://docs.aws.amazon.com/servicequotas/2019-06-24/apireference/API_ListRequestedServiceQuotaChangeHistoryByQuota.html) operations\.

   ```
   $ aws service-quotas request-service-quota-increase \
           --service-code kms \
           --quota-code L-2AC98190 \
           --desired-value 700
   
   {
       "RequestedQuota": {
           "Id": "a12345",
           "ServiceCode": "kms",
           "ServiceName": "AWS Key Management Service (AWS KMS)",
           "QuotaCode": "L-2AC98190",
           "QuotaName": "Cryptographic operations (RSA) request rate",
           "DesiredValue": 700,
           "Status": "PENDING",
           "Created": 1580446904.067,
           "Requester": "{\"accountId\":\"111122223333\",\"callerArn\":\"arn:aws:iam::111122223333:root\"}",
           "QuotaArn": "arn:aws:servicequotas:us-east-2:111122223333:kms/L-2AC98190",
           "GlobalQuota": false,
           "Unit": "None"
       }
   }
   ```