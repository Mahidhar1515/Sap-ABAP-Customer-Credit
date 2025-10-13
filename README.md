I built an ABAP report named Z_CUST_CREDIT! The main job of this report is to check a customer's basic information (like their name and city) and their credit limit, and then show it all in a cool, clean, professional table.

This project uses all the major ABAP fundamentals 

1. Talking to the Database (The Hardest Part!)

Tables Used: 
KNA1 (Customer Name, Address)
KNB1 (Customer Financial/Credit Info)

Used an INNER JOIN command to connect KNA1 and KNB1 together using the Customer Number (KUNNR).

Filtering: I added input boxes (PARAMETERS and SELECT-OPTIONS) so users can easily search by Company Code and a range of Customer Numbers.

2. Processing the Data 
Internal Tables: Took the data from the database and stored it in an Internal Table (it_cust_data) and a Work Area (wa_cust_data) to process it.

Custom Logic (The IF/ELSE): I wrote simple IF/ELSE logic inside a LOOP to automatically check the customer's Credit Limit (KLIMG) and assign a simple status (like "High Credit Risk").

3. Organizing and Displaying the Code
   
Organizing: Used Subroutines (FORM...ENDFORM) to keep the code clean and organized.  kept the data fetching separate from the display

The Fancy Output: To show the results in the interactive SAP table (the ALV Grid)
