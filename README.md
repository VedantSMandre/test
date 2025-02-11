## This document outlines the necessary fields and their categorization for a payment system, based on the provided Figma landing page details and the underlying table schema (GCMS_FUNDS_TRANSFER_DETAIL). It includes both the frontend display fields and additional backend fields that, while not visible on the navigation page, are critical for processing and tracking payments.

## 1. Frontend Display Fields
These fields are expected to be shown on the navigation page based on the Figma design.

## 1.1 Payment Identification
FUNDS_TRANSFER_ID (Primary Key) (Unique identifier for each funds transfer record.)
TRANSACTION_ID (Transaction reference that links to other systems.)
TRANSACTION_REFERENCE_NUMBER (Displayed to users as a reference.)
## 1.2 Beneficiary Information
BENEF_CUST_NAME (May need to be parsed into first and last name if stored as a single string.)
BENEF_CUST_ID (Unique identifier for the beneficiary in the system.)
## 1.3 Payment Type / Tags
  TRANSACTION_CODE (Defines transaction type, e.g., deposit, withdrawal.)
  FUNDS_TYPE (e.g., ACH, WIRE, etc.)—important for routing.
  CATEGORY_PURPOSE (Categorizes the purpose of the payment, useful for compliance reporting.)
## 1.4 Amount Details
  AMOUNT (Transaction amount.)
  CURRENCY (Currency in which the transaction is processed.)
  USD_AMOUNT (Standardized amount for reporting and cross-currency comparisons.)
## 1.5 Status Information
TRANSACTION_STATUS_ID (Tracks payment progress, e.g., pending, completed, failed.)
CREATION_DATE (Used as the submission date for frontend display.)
## 2. Critical Backend Fields (Not Displayed on Navigation Page)
These fields are necessary for transaction processing, auditing, and backend operations.

## 2.1 Transaction Processing
VALUE_DATE (Date the transaction is considered effective for processing.)
EXCHANGE_RATE (Rate applied if currency conversion is involved.)
BASE_RATE (Base rate before applying margins or fees.)
FX_EXCHANGE_RATE (Foreign exchange rate if applicable.)
FX_ONLINE_INDICATOR (Indicates if the exchange rate was fetched in real-time.)
TRACKING_STATUS_ID (Status ID used for internal tracking of transaction.)
TRACKING_DETAIL_ID (Detailed tracking record linked to status changes.)
## 2.2 Extended Beneficiary Details
BENEF_CUST_ADDRESS_LINE1 (Primary address line.)
BENEF_CUST_ADDRESS_LINE2 (Optional second address line.)
BENEF_CUST_ADDRESS_LINE3 (Optional third address line.)
BENEF_CUST_COUNTRY_CODE (Country of the beneficiary—important for cross-border payments but may be less relevant for domestic transfers.)
BENEF_BANK_FUNDS_TYPE (Specifies how funds are handled at the beneficiary bank.)
BENEF_INSTIT_ID (Unique ID of the beneficiary’s financial institution.)
BENEF_INSTIT_NAME (Name of the beneficiary’s financial institution.)
## 2.3 Source / Routing Information
SOURCE_SYSTEM (System from which the transaction originated.)
APPLICATION_ID (Identifier for the application processing the payment.)
IBAN (International Bank Account Number—may not always be needed for domestic transactions.)
BANK_SWIFT_ID (SWIFT code for routing—also may not be necessary for domestic payments.)
UETR (Unique End-to-End Transaction Reference—helps in tracking payments across networks.)
## 2.4 Audit / Security Fields
CREATED_BY (User or system that created the transaction record.)
CREATION_DATE (Timestamp of when the transaction was initiated.)
LAST_UPDATED_BY (User or system that last modified the record.)
LAST_UPDATED_DATE (Timestamp of last modification.)
USER_SESSION_ID (Session ID associated with the transaction, useful for tracing user activity.)
VERSION (Used for concurrency control and audit purposes.)
## 2.5 Payment Details
DETAILS_OF_PAYMENT_LINE1 (Free-text description of payment, e.g., invoice number.)
DETAILS_OF_PAYMENT_LINE2 (Additional details if needed.)
DETAILS_OF_PAYMENT_LINE3 (Additional details if needed.)
DETAILS_OF_PAYMENT_LINE4 (Additional details if needed.)
DETAILS_OF_CHARGES (Specifies who bears transaction charges, e.g., sender, receiver, shared.)
PURPOSE_CODE (Classifies payment purpose for regulatory purposes.)
BUSINESS_CATEGORY (Categorizes the business area related to the transaction.)
## 2.6 Account Information
DEBIT_ACCOUNT_NUMBER (Account from which funds are debited.)
DEBIT_ACCOUNT_CURRENCY_CODE (Currency of the debited account.)
ACCOUNT_BALANCE (Balance before or after transaction—depends on processing logic.)
ORIG_DEBIT_ACCOUNT_NUMBER (Original account before any transformations, if applicable.)
## 3. Key Relationships and Structure
Understanding the relationships between the fields is crucial for proper data integration and processing.

## 3.1 Transaction Flow
FUNDS_TRANSFER_ID → TRANSACTION_ID (Establishes a unique relationship between funds transfer and transaction identifier.)
TRANSACTION_STATUS_ID (Used for tracking the status of the payment.)
TRACKING_STATUS_ID → TRACKING_DETAIL_ID (Establishes the tracking mechanism for the payment process.)
## 3.2 Financial Flow
DEBIT_ACCOUNT_NUMBER → AMOUNT → EXCHANGE_RATE (Links account number to the payment amount and associated exchange rate.)
CURRENCY → USD_AMOUNT (Used for standardization of the payment amount.)
## 3.3 Audit Trail
CREATED_BY → CREATION_DATE (Captures who created the record and when.)
LAST_UPDATED_BY → LAST_UPDATED_DATE (Tracks who last updated the record and when.)
VERSION (Used for concurrency control and audit purposes.)
## 4. Additional Recommendations
## 4.1 Indexing
To improve query performance, consider indexing the following fields:

TRANSACTION_STATUS_ID (Optimizes filtering by status.)
CREATION_DATE (Improves performance for date-based queries.)
BENEF_CUST_ID (Speeds up beneficiary lookups.)
FUNDS_TYPE (Useful if filtering transactions by payment type.)
## 4.2 Caching Strategies
Implement caching where necessary for:

Status Lookups (To avoid frequent DB queries.)
Exchange Rates (To ensure real-time yet efficient rate application.)
Customer Details (If customer details are static for the session, caching reduces DB hits.)
