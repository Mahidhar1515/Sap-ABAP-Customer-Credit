# 💳 ZMVM_CUST_CREDIT — SAP ABAP Customer Credit Report

![ABAP](https://img.shields.io/badge/Language-ABAP-blue?style=for-the-badge)
![SAP](https://img.shields.io/badge/Platform-SAP%20ERP-lightgrey?style=for-the-badge)
![Status](https://img.shields.io/badge/Status-Active-success?style=for-the-badge)

---

## 📘 Overview
**ZMVM_CUST_CREDIT** is an SAP ABAP program designed to retrieve and display customer credit information from standard SAP tables — **KNA1**, **KNB1**, and **KNKK**.  
The program enables filtering based on **Company Code** and **Customer Number Range**, then classifies customers into different **credit risk levels** according to their credit limits.

---

## ⚙️ Program Structure

This project follows a **modular include structure** for better readability and maintenance.

### 🧩 1. Main Program — `Zmvm_CUST_CREDIT`
Defines the selection screen and controls the execution flow.

```abap
REPORT Zmvm_CUST_CREDIT.

INCLUDE Zmvm_CUST_CREDIT_TOP.   " Global Data Definitions
INCLUDE Zmvm_CUST_CREDIT_FORM.  " Subroutines for Logic

SELECTION-SCREEN BEGIN OF BLOCK s1 WITH FRAME TITLE TEXT-s01.
  PARAMETERS: p_bukrs TYPE knb1-bukrs.   " Company Code
  SELECT-OPTIONS: s_kunnr FOR kna1-kunnr.  " Customer Number Range
SELECTION-SCREEN END OF BLOCK s1.

START-OF-SELECTION.
  PERFORM fetch_customer_data USING p_bukrs.
  PERFORM display_customer_data.

```
### 2. Top Include — Zmvm_CUST_CREDIT_TOP

Defines the global structure, internal table, and work area.
```abap
TABLES: kna1, knb1, knkk.

TYPES: BEGIN OF ty_cust_data,
         kunnr  TYPE kna1-kunnr,  " Customer Number
         name1  TYPE kna1-name1,  " Name
         ort01  TYPE kna1-ort01,  " City
         land1  TYPE kna1-land1,  " Country
         bukrs  TYPE knb1-bukrs,  " Company Code
         klimk  TYPE knkk-klimk,  " Credit Limit
         kredit TYPE string,      " Credit Risk Status
       END OF ty_cust_data.

DATA: it_cust_data TYPE STANDARD TABLE OF ty_cust_data,
      wa_cust_data TYPE ty_cust_data.
```
### 3. Form Include — Zmvm_CUST_CREDIT_FORM

Contains two subroutines: one for data fetching & processing, another for displaying results.

Subroutine 1: Fetch and Process Data

Joins KNA1, KNB1, and KNKK tables, applies filters, and classifies customers by credit limit.

```abap
FORM fetch_customer_data USING p_bukrs TYPE knb1-bukrs.

  SELECT a~kunnr,
         a~name1,
         a~ort01,
         a~land1,
         b~bukrs,
         c~klimk
    INTO CORRESPONDING FIELDS OF TABLE @it_cust_data
    FROM kna1 AS a
    INNER JOIN knb1 AS b ON a~kunnr = b~kunnr
    LEFT OUTER JOIN knkk AS c ON a~kunnr = c~kunnr
    WHERE b~bukrs = @p_bukrs
      AND a~kunnr IN @s_kunnr.

  IF it_cust_data IS INITIAL.
    WRITE: / 'No Customer Data found matching the selection criteria.'.
    RETURN.
  ENDIF.

  LOOP AT it_cust_data INTO wa_cust_data.
    IF wa_cust_data-klimk > 100000.
      wa_cust_data-kredit = 'High Credit Risk'.
    ELSEIF wa_cust_data-klimk > 50000.
      wa_cust_data-kredit = 'Standard Credit'.
    ELSE.
      wa_cust_data-kredit = 'Low Credit Limit'.
    ENDIF.
    MODIFY it_cust_data FROM wa_cust_data.
  ENDLOOP.

ENDFORM.

```
Subroutine 2: Display Customer Data

Displays formatted output using WRITE statements.

```abap
FORM display_customer_data.

  IF it_cust_data IS INITIAL.
    WRITE: / 'No Customer Data found.'.
    EXIT.
  ENDIF.

  WRITE: / 'CustNo', 15 'Name', 60 'City', 80 'Country',
           95 'Company', 105 'CreditLimit', 120 'CreditStatus'.
  ULINE.

  LOOP AT it_cust_data INTO wa_cust_data.
    WRITE: / wa_cust_data-kunnr  UNDER 'CustNo',
             wa_cust_data-name1  UNDER 'Name',
             wa_cust_data-ort01  UNDER 'City',
             wa_cust_data-land1  UNDER 'Country',
             wa_cust_data-bukrs  UNDER 'Company',
             wa_cust_data-klimk  UNDER 'CreditLimit',
             wa_cust_data-kredit UNDER 'CreditStatus'.
  ENDLOOP.

ENDFORM.
