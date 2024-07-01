# Design Input Requirements

## System Interfaces

| Tag | Prio | Attribute | Target Description | Verification Method (how to be measured) |
| --- | ---- | --------- | ------------------ | ---------------------------------------- |
| IF1a1 | 1 | Scanner-IF, codes | EAN-Coding (EAN-8, EAN-13, EAN-128), variable parameter/configurable | By inspection, if 100 items detected and transferred to the cash register system correctly. |
| IF1a2 | 1 | Scanner-IF, data transfer | ASCII-string, variable parameter/configurable | With IF1a1. |
| IF2 | | | | *Conveyor system is considered an independent system* |
| IF7a | 1 | Card Terminal, protocol | RS232, configurable | With IF7b. |
| IF7b | 1 | Card Terminal, payment transaction | Payment transaction process triggered by command. Result delivered by synchronous return value. Possible values: "successfully processed payment", "payment process cancelled". Transfer information about cancellation cause as character string. At least 10€. | Emulate Debit Card Terminal. Check payment processes: 1. Payment process successful, 2. Timeout, 3. Abort process because PIN-code is incorrect (only if PIN-code used), 4. Abort process because debit card is invalid/not legible, 5. Abort process because account balance is insufficient. |
| IF7c | 1 | Card Terminal, setup | Configuration of Debit Card Terminal through parameter file from cash register system | By inspection if Debit Card Terminal reinitializes at transfer of parameter file from cash register system. |
| IF8  | | | | *Credit and debit cards are processed as a single system.* |

## System Function

| Tag | Prio | Attribute | Target Description | Verification Method (how to be measured) |
| --- | ---- | --------- | ------------------ | ---------------------------------------- |
| F5b | 1 | Card payment, cash payout | At sales over 10€, a customer can withdraw up to 100€ in cash, when paying by debit card. The customer’s checking account will be debited with the total sum of sales and cash withdrawal. | By inspection, if cash withdrawal up to 100€ is processable and if customer’s checking account is debited with correct total sum. Verify by emulating cashless payment process. |
