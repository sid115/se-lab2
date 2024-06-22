# Design Input Requirements

## System Interfaces

| Tag | Prio | Attribute | Target Description | Verification Method (how to be measured) |
| --- | ---- | --------- | ------------------ | ---------------------------------------- |
| IF1a1 | 1 | Scanner-IF, codes | EAN-Coding (EAN-8, EAN-13, EAN-128), variable parameter/configurable | By inspection, if 100 items detected and transferred to the cash register system correctly. |
| IF1a2 | 1 | Scanner-IF, data transfer | ASCII-string, variable parameter/configurable | With IF1a1. |
| IF1b | 1 | Scanner-IF, log | RS232, variable parameter/configurable | With IF1a1. |
| IF1c | 1 | Scanner-IF, setup | Scanner parameterization through parameter file of cash register system | By inspection if scanner reinitializes at transfer of parameter file from cash register system. |
| IF2 | | | | Conveyor system is considered an independent system |
| IF3a | 1 | ERP- IF, invoice | Transfer invoice of each payment transaction to ERP (invoice number, invoice items, item amounts, total amount) | By inspection if invoice is issued for each purchase and transferred completely and semantically correct to ERP-System. |
| IF3b | 1 | ERP- IF, payment | Transfer payment receipt for each invoice to ERP (invoice number, total amount, customer identification) | By inspection if payment receipt is issued for each purchase and if it is transferred completely and semantically correct to ERP-System. |
| IF3c | 1 | ERP- IF, price list | Receive price list of products from ERP (for each item in price list: product-ID, product description, product price, product valid through, other information optional). List consists of a minimum of 0 and a maximum of 9999 items. | By inspection if price lists of 0, 1, 2, 10, 1000, 9999 products are received completely and semantically correct from ERP-System. By inspection if invalid product data is detected and rejected selectively. |
| IF3d | 1 | ERP- IF, transfer protocol | TCP/IP-protocol, asynchronous message passing | By inspection if messages can be correctly sent and received asynchronously. |
| IF4 | 1 | Local Inventory Management (LIM) Interface | Send packing slip of recently sold products to LIM (any number of product items, anytime). Data: product identification, quantity, date of sale. Protocol: TCP/IP, asynchronous message passing | By inspection if packing slip fully covers all items of recently sold products and sends them to LIM semantically correct. |
| IF5a | 1 | Weighing device interface, protocol | TCP/IP-protocol, synchronous message passing | By inspection if messages can be correctly sent and received synchronously |
| IF5b | 1 | Weighing device interface, weighing command | Weighing process triggered by command. Synchronous return value delivers mass measure: measured value, unit and calibration status. | By inspection if simulation of cash register system operation provides correct results of 10 successive weighing processes. |
| IF6a | 1 | RF-Reader interface, configurable codings | EPC-Coding (EPC-64, EPC-96, EPC-128) or EAN/GTIN-Coding (EAN-8, EAN-13) | By inspection if RFID detects and transfers 100 item-IDs to cash register system correctly |
| IF6b | 1 | RF-Reader interface, data transfer | JSON-File. Variable item number n for each file (1<= n <= 999), i.e., generate JSON-File with 1 item-ID upon reading 1 RFID-Tag and generate JSON-File with numerous item-IDs upon reading the shopping cart. | By inspection if various item-ID lists (including 1, 5, 9, 27, 99, 276, 999 items) are transferred from RFID-Tag/Shopping Cart correctly and if correctly structured JSON-Files are transferred to cash register system. |
| IF6c | 1 | RF-Reader interface, protocol | TCP/IP protocol, asynchronous message passing | With IF6a, IF6b. |
| IF6d | 1 | RF-Reader interface, setup | RF-Reader parameter setup through parameter file from cash register system | By inspection if RF-Reader reinitializes by transfer of parameter file from cash register system. |
| IF7a | 1 | Card Terminal, protocol | RS232, configurable | With IF7b. |
| IF7b | 1 | Card Terminal, payment transaction | Payment transaction process triggered by command. Result delivered by synchronous return value. Possible values: "successfully processed payment", "payment process cancelled". Transfer information about cancellation cause as character string. At least 10€. | Emulate Debit Card Terminal. Check payment processes: 1. Payment process successful, 2. Timeout, 3. Abort process because PIN-code is incorrect (only if PIN-code used), 4. Abort process because debit card is invalid/not legible, 5. Abort process because account balance is insufficient. |
| IF7c | 1 | Card Terminal, setup | Configuration of Debit Card Terminal through parameter file from cash register system | By inspection if Debit Card Terminal reinitializes at transfer of parameter file from cash register system. |
| IF8a | | | | Credit and debit cards are processed as a single system. |
| IF9a | 1 | Programming device | RS232, configurable | With IF9b |
| IF9b | 1 | Programming device terminal | Fully access on cash register system as an administrator via programming device | By inspection if programming device adjusts cash register system functions and if upload/download of parameter files is possible |

## System Function

| Tag | Prio | Attribute | Target Description | Verification Method (how to be measured) |
| --- | ---- | --------- | ------------------ | ---------------------------------------- |
| F1a | | | | See IF2 |
| F2a | 1 | Invoice calculation | Priority rule (if prices are distinct): price on product item, price on shelf, prices in cash register system. Always apply most favorable price and consider discounts. Error rate < 1% | Test with at least 100 product items. |
| F2b | 1 | Invoice calculation | Distinguish product lot prices and/or product specimen prices by EPC | By inspection, if ERP system distinguishes different product items per product lot and/or product specimen and if both kinds of product items are detectable through RFID and/or bar code. |
| F3a | 1 | Inventory Management | Frequently generate packing slips of all sold product items and send them to the local inventory management system. Keep unsent packing slips in the cash register system until receiving an acknowledgement from the inventory management system. | Inject list of virtually sold product items into cash register system. Generate and transfer packing slip (1) triggered manually, (2) triggered by timer. Shut down and reboot cash register system. Check, if unsent packing slips are transferred before system shut down. |
| F3b | 1 | Inventory Management | Generate packing slip. On manual or automatic trigger (internal event or time event) all unreported product items are summarized and sent to the inventory management system stating the date of their sale. | Inject list of virtually sold product items into the cash register system. Trigger packing slip transfer manually. Check, if all product items are reported to the inventory management system with correct indication of each item’s quantity. |
| F4 | 1 | Invoicing (invoice calculation process) | Cash register system executes invoicings sequentially. Each cash register system can manage two invoicings simultaneously. | Start invoicing and check, if a second process can be executed successfully before the first one has finished. |
| F5a | 1 | Cash drawer, automatic opening | Cash drawer shall open at the end of invoicing, only if the customer pays in cash, has to be given change or if a customer signed payment receipt (credit card, debit card without using PIN-code) has to be stored in the cash drawer. | By inspection, if cash drawer only opens under the defined target conditions and does not open randomly. |
| F5b | 1 | Card payment, cash payout | At sales over 10€, a customer can withdraw up to 100€ in cash, when paying by debit card. The customer’s checking account will be debited with the total sum of sales and cash withdrawal. | By inspection, if cash withdrawal up to 100€ is processable and if customer’s checking account is debited with correct total sum. Verify by emulating cashless payment process. |
| F6a | 1 | Exchanging goods | Identify and accept returned products, return money, generate packing slip item (entry). | By inspection, if 10 different products (randomly selected from product portfolio) can be returned. Check, if products are simultaneously returnable in groups of 1, 2, 3 and 5 products. Check, if generated packing slip items are correct. |
| F6b | 1 | Returning vessels with refundable deposits | As in exchanging goods (F6a), but with product category "empty vessels". Type of empty vessel defines refundable amount. | By inspection, if cash register system supplies product-IDs for all valid vessel types. Check refunding of 5 different vessel types. |
| F7 | 1 | Calculating cash holdings ("account statement") | Upon manual request or change of cashier, an up-to-date account statement of cash holdings is calculated based on the initial cash holdings and all executed cash handlings. Initial cash holdings are defined by preset parameters. | Inject data file of virtual sales into the cash register system. Request and print account statement and check for correct balance. |
| F8 | 2 | Processing cash equivalents (e.g. coupons) | Accept coupons with Auto-ID (Barcode, RFID) as cash equivalent. | By inspection, if the coupons’ values are detected correctly. Check at least 2 coupons for each valid coupon type. |

## Usability

| Tag | Prio | Target Value | Target Description | Verification Method (how to be measured) |
| --- | ---- | ------------ | ------------------ | ---------------------------------------- |
| B1a | 1 | User authentication | Any user must log-in after plugging in the cash drawer in the cash register system in order to use it and log-out in order to remove the cash drawer of the cash register system after finishing. | By inspection, if a successful log-in is mandatory before usage and if cash drawer removal is only possible after log-out. |
| B1b | 1 | Free choice of cash register system | Cashiers can log-in to any cash register system at the supermarket. | See above. Run test on at least 3 different cash register systems. |
| B2 | 1 | User interface ergonomics | Legibility and usability of user interface according to colors, contrast, density and grouping of information as well as mechanical-electrical stability support the system usage. | By inspection, if cash register systems comply with relevant standards on ergonomics of working places / workstations. |
| B3 | 1 | Language setting | Cash register system supports German, English, French, Spanish, and Italian as user interface languages. | By inspection, if a complete and accurate translation of each German user interface text entry exists in any of the predefined languages. |
| B4 | 1 | Online Help | Cash register system contains a national language version for online help. All relevant user functions are explained and are comprehensible and understandable for trained staff. | By inspection, if all cashiers routine is explained comprehensively and understandable. Routines are all functions used by a typical cashier at least once a month. |

## System : Startup (boot), Stop (shutdown), Resume

| Tag | Prio | Target Value | Target Description | Verification Method (how to be measured) |
| --- | ---- | ------------ | ------------------ | ---------------------------------------- |
| S1a | 1 | POST | Boot sequence includes Power On Self Test (POST), which checks all relevant functions and overall integrity of cash register system. | Boot cash register system. Check, if POST exits and performs correctly, by specifically disconnecting or disabling external systems. Check, if external systems are reinitialized. |
| S1b | 1 | POST | Skipable by user action. | By inspection, if user action is possible and effective at POST. |
| S2 | 1 | Maximum duration of boot / shutdown sequences | Average duration up to 20s and maximum 60s in exceptional cases, when all external systems are completely reinitialized and unsent data files are transferred (packing slips etc.). | Check at least 10 scenarios: generate different error conditions and measure startup time of boot sequence. Measure shutdown time, especially if unsent data files exist. |
| S3 | 1 | Reaction in case of unexpected blackout | Behavior of cash register system at reboot after blackout must not damage unsent data files, internal system components or external systems. | Simulate blackout at different system operating states. Verify, that system is only able to return into normal mode (online) if no damages have occurred. |

## Safety / Security (data security)

| Tag | Prio | Target Value | Target Description | Verification Method (how to be measured) |
| --- | ---- | ------------ | ------------------ | ---------------------------------------- |
| D1 | 1 | Data recovery after fatal system error or blackout | Cash register system must secure all incomplete transaction data (such as incomplete invoicings, incomplete or unsent packing slips, unsent invoices, etc.), so that all the data can be restored error-free after system reboot and all incomplete/active invoicings can be completed. | Simulate blackout. Check, if all buffered transaction data is still existent at system restart after blackout and if all incomplete transaction data can be resumed and completed. |
| D2 | 1 | Wrong data | Log incorrect input data from scanner or other input devices incl. timestamp and type of error. | Simulate different input data errors and check, if error log contains timestamp and type of error. |

## Reliability

| Tag | Prio | Target Value | Target Description | Verification Method (how to be measured) |
| --- | ---- | ------------ | ------------------ | ---------------------------------------- |
| R1 | 1 | Availability | 98% on 6 business days per week with 14h of continuous system usage. | Calculate based on total downtime of 10 cash register systems within the first 3 months of system operating. |
| R2 | 1 | Preventive Maintenance | Maximum 2 maintenance assignments per year. | By inspecting system reliability through statistical extrapolation. |
| R3a | 1 | Mean time between unscheduled remote maintenance events (any error requiring contact to customer service) | At least 70 days of continuous operation with a total throughput of 10000 products per day. Reliability level = 90% | Evaluate operation data and calculate statistical extrapolation, e.g. after an operation period of at least half a year and a total throughput of 3 million products. |
| R3b | 1 | Mean time between unscheduled maintenance on-site (any error requiring on-site maintenance of the cash register system) | At least 200 days of continuous operation with a total throughput of 10000 products per day. Reliability level = 90% | Evaluate operation data and calculate statistical extrapolation, e.g. after an operation period of at least one year and a total throughput of 10 million products. |
