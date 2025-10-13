# BRN#026 - Payment History Analysis Test Cases
## Assigned to: Abhijeet | Sprint 4

---

## Overview

This document contains UI-focused test cases for BRN#026 - Account Scorecard Payment History Analysis features. These test cases are designed for manual testing by QA testers through the Salesforce user interface.

**Module:** Account Management  
**Sub-Module:** Account ScoreCard - Payment History Analysis  
**Developer:** Abhijeet  
**Sprint:** Sprint 4  
**Status:** In Progress

---

## Test Case 1: Payment History Analysis - Existing Customer Eligibility

### Scenario / Test Description
Verify that the system automatically determines if an existing customer's payment history needs to be analyzed when submitting a binding quote for approval.

### Preconditions
1. User logged in as Bid Manager or Account Manager
2. Account exists with:
   - Onboarding Status = "Onboarded Successfully"
   - Account Status = "Active"
   - Customer Type = "Private Companies" (or other eligible type)
   - ERP ID populated (indicating existing customer)
3. Opportunity created and linked to the account
4. Invoice records synced from ERP for the last 12 months

### Test Steps

1. Navigate to the Account record
2. Verify the following fields on Account:
   - Onboarding Status = "Onboarded Successfully"
   - Account Status = "Active"
   - ERP_ID__c field has a value
   - Customer Type = "Private Companies"
3. Create a new Costing Sheet for this account
4. Select Quote Type = "Firm (Binding)"
5. Fill all required fields for the costing sheet
6. Click "Submit for Approval" button
7. Observe that payment history re-evaluation is triggered
8. Check the Account record for these fields:
   - Payment_History_Check_Required__c = TRUE (checkbox)
   - Payment_History_Checked_Date__c = TODAY (auto-populated)
9. Navigate to "Payment History Analysis" related list on Account
10. Verify a new Payment History Analysis record is created
11. Open the Payment History Analysis record
12. Verify it contains calculated metrics from last 12 months of invoices

### Expected Result
- System should automatically determine that payment history analysis is required for existing customers with eligible Customer Types
- Payment history check should be triggered when binding costing sheet is submitted for approval
- A new Payment History Analysis child record should be created under the account
- The check should NOT be triggered for Non-Binding (Budgetary) quotes
- Customer Types requiring check: Private Companies, Government Related Companies, UAE Governments, Other governments, Other related parties
- Customer Types exempt: Group Companies, Individuals, Employees

---

## Test Case 2: Payment History Analysis - Bypass for Non-Binding Quotes

### Scenario / Test Description
Verify that payment history analysis is NOT required when creating/submitting a Non-Binding (Budgetary) quote.

### Preconditions
1. User logged in as Bid Manager
2. Existing customer account (same setup as Test Case 1)
3. Opportunity created and linked to the account

### Test Steps

1. Navigate to the Opportunity record
2. Click "Create Costing Sheet" button
3. Select Quote Type = "Non-Binding (Budgetary)"
4. Fill all required costing sheet fields
5. Save the costing sheet
6. Click "Submit for Approval"
7. Verify submission succeeds without payment history check
8. Navigate to Account record
9. Check Payment_History_Check_Required__c checkbox
10. Verify it is NOT checked or is FALSE
11. Verify no new Payment History Analysis record created for this submission
12. Verify approval process proceeds normally without payment history validation

### Expected Result
- Non-binding quotes should proceed without payment history analysis
- No Payment History Analysis record should be created
- Payment_History_Check_Required__c should remain FALSE
- Approval process should not require payment history validation
- This allows for quick budgetary quotes without full credit analysis

---

## Test Case 3: Average Payment Delay Calculation and Scoring

### Scenario / Test Description
Verify that the system automatically calculates the average payment delay from the last 12 months of invoice data and assigns the correct score based on the defined range.

### Preconditions
1. Account exists as an existing customer (ERP ID populated)
2. At least 10 invoice records synced from ERP for the last 12 months
3. Each invoice has:
   - Payment Due Date populated
   - Payment Actual Received Date populated
   - Linked to the Account via Order relationship
4. User logged in as Bid Manager or Account Manager
5. Binding costing sheet ready to submit for approval

### Test Steps

1. Navigate to the Account record
2. Scroll to "Invoices" or "Orders" related list
3. Click "View All" to see invoice records
4. Note several invoices with payment dates
5. Manually calculate average delay:
   - Invoice 1: Due 1-Jan, Paid 10-Jan = 9 days delay
   - Invoice 2: Due 15-Jan, Paid 18-Jan = 3 days delay
   - Invoice 3: Due 1-Feb, Paid 25-Feb = 24 days delay
   - Average = (9+3+24)/3 = 12 days
6. Navigate back to Account record
7. Navigate to Opportunity linked to this account
8. Create/Open Costing Sheet with Type = "Firm (Binding)"
9. Click "Submit for Approval"
10. System triggers payment history re-evaluation
11. Navigate to Account record
12. Go to "Payment History Analysis" related list
13. Open the latest Payment History Analysis record
14. Locate the field "Average_Payment_Delay__c"
15. Verify value matches your calculation (approximately 12 days)
16. Locate the field "Average_Payment_Delay_Score__c"
17. Based on the range, verify score assigned:
    - 0-5 days = Score 5
    - 6-15 days = Score 4 (12 days falls here)
    - 16-30 days = Score 3
    - 31-60 days = Score 2
    - >60 days = Score 1
18. Verify score = 4 (since 12 days is in 6-15 range)
19. Check the calculation is stored in the Payment History Analysis record
20. Verify Last_Assessment_Date__c = TODAY

### Expected Result
- System should automatically calculate average payment delay from invoice data in last 12 months
- Calculation: Sum of (Payment Actual Date - Payment Due Date) for all invoices / Number of invoices
- Score should be assigned automatically based on range:
  - 0-5 days → Score 5
  - 6-15 days → Score 4
  - 16-30 days → Score 3
  - 31-60 days → Score 2
  - >60 days → Score 1
- Calculation and score should be stored in Payment History Analysis child object
- Should be triggered only when binding quote is submitted for approval
- Should be read-only for users (auto-calculated)

---

## Test Case 4: Percentage of Overdue Invoices (Volume) Calculation

### Scenario / Test Description
Verify that the system calculates the percentage of overdue invoices by volume and assigns the correct score.

### Preconditions
1. Account with at least 20 invoices in last 12 months
2. Mix of on-time and overdue invoices:
   - 15 invoices paid on time
   - 5 invoices were overdue (now paid or still pending)
3. User logged in with access to trigger payment history check

### Test Steps

1. Navigate to Account record
2. Review invoice related list
3. Manually count:
   - Total invoices (last 12 months) = 20
   - Overdue invoices (payment date > due date) = 5
   - Calculate percentage = (5/20) * 100 = 25%
4. Navigate to Opportunity for this account
5. Create Costing Sheet with Type = "Firm (Binding)"
6. Click "Submit for Approval"
7. System triggers payment history analysis
8. Navigate to Account → Payment History Analysis related list
9. Open latest Payment History Analysis record
10. Locate field "Percentage_Overdue_Invoices_Volume__c"
11. Verify value = 25% (or close to your manual calculation)
12. Locate field "Percentage_Overdue_Volume_Score__c"
13. Based on scoring matrix, verify:
    - 0-10% = Score 5
    - 11-25% = Score 4 (25% falls here)
    - 26-50% = Score 3
    - 51-75% = Score 2
    - >75% = Score 1
14. Verify score = 4
15. Check Payment History Analysis record shows both percentage and score

### Expected Result
- System should calculate: (Number of overdue invoices / Total invoices in 12 months) * 100
- Score assigned automatically based on percentage range
- Only invoices from last 12 months should be considered
- Calculation should consider invoices that were overdue (even if later paid)
- Score should be stored on Payment History Analysis record
- Should be read-only and auto-calculated

---

## Test Case 5: Percentage of Total Outstanding Amount Overdue (Value)

### Scenario / Test Description
Verify calculation of the percentage of total outstanding amount that is overdue by value.

### Preconditions
1. Account with multiple invoices in last 12 months
2. Some invoices have outstanding amounts:
   - Invoice 1: Total = 10,000 AED, Outstanding = 0 (fully paid)
   - Invoice 2: Total = 5,000 AED, Outstanding = 5,000 (fully overdue)
   - Invoice 3: Total = 20,000 AED, Outstanding = 10,000 (partially overdue)
   - Invoice 4: Total = 15,000 AED, Outstanding = 0 (fully paid)
3. Total outstanding across all invoices = 15,000 AED
4. Overdue outstanding = 15,000 AED

### Test Steps

1. Navigate to Account record
2. Review invoice/order related list or invoices tab
3. Manually calculate:
   - Total outstanding amount = Sum of all outstanding amounts = 15,000 AED
   - Total overdue outstanding = Outstanding amounts where payment date > due date = 15,000 AED
   - Percentage = (15,000 / 15,000) * 100 = 100%
4. Trigger payment history check by submitting binding costing for approval
5. Navigate to Payment History Analysis record
6. Locate field "Percentage_Outstanding_Amount_Overdue__c"
7. Verify value = 100% (or matches calculation)
8. Locate field "Percentage_Outstanding_Amount_Score__c"
9. Verify score based on matrix:
   - 0-10% = Score 5
   - 11-25% = Score 4
   - 26-50% = Score 3
   - 51-75% = Score 2
   - >75% = Score 1 (100% falls here)
10. Verify score = 1
11. Check that this indicates high risk
12. Verify approver can see this score during costing approval

### Expected Result
- System calculates: (Total overdue outstanding amount / Total outstanding amount) * 100
- Considers only invoices from last 12 months
- Score assigned automatically:
  - 0-10% → Score 5
  - 11-25% → Score 4
  - 26-50% → Score 3
  - 51-75% → Score 2
  - >75% → Score 1
- High overdue percentage should result in low score (high risk)
- Score should be visible to approvers during binding quote approval
- Stored on Payment History Analysis record

---

## Test Case 6: Number of Short Payments and Disputes - Manual Entry

### Scenario / Test Description
Verify that Bid Manager can manually enter the number of short payments/disputes raised by customer and the system auto-calculates the score.

### Preconditions
1. Account with invoice transaction history
2. User logged in as Bid Manager or Account Manager
3. Payment History Analysis record exists for the account

### Test Steps

1. Navigate to Account record
2. Scroll to "Invoices" or "Invoice Transactions" related list
3. Review invoices to identify short payments or disputes
4. Count manually: e.g., 4 invoices had short payments/disputes
5. On the Account record page, locate the field "Number_of_Short_Payments_Disputes__c"
6. Click Edit (pencil icon) on the Account
7. Enter value = 4 in "Number_of_Short_Payments_Disputes__c" field
8. Save the Account
9. Trigger payment history re-evaluation:
   - Navigate to related Opportunity
   - Submit binding costing for approval (or click "Recalculate Payment History" button if available)
10. Navigate back to Account record
11. Go to Payment History Analysis related list
12. Open the latest record (or refresh existing)
13. Locate field "Number_Short_Payments_Disputes__c" (from account)
14. Verify value = 4
15. Locate field "Short_Payments_Disputes_Score__c"
16. Verify score based on matrix:
    - 0 disputes = Score 5
    - 1-2 disputes = Score 4
    - 3-4 disputes = Score 3 (4 falls here)
    - 5-6 disputes = Score 2
    - >6 disputes = Score 1
17. Verify score = 3
18. Verify score is read-only (cannot be manually changed)
19. Verify this score contributes to overall Payment History Score

### Expected Result
- Account should have a manual entry field for number of short payments/disputes
- Bid Manager/Account Manager can edit this field
- When payment history analysis runs, it should read this value
- System should auto-assign score based on the number entered
- Score should be 1-5 based on scoring matrix
- Score should be read-only and system-calculated
- Each payment history evaluation should create a snapshot in Payment History Analysis object
- Old values should be preserved in previous analysis records (history tracking)

---

## Test Case 7: Number of Customer Escalations for Collections - Manual Entry

### Scenario / Test Description
Verify manual entry of customer escalations for collections and automatic score calculation.

### Preconditions
1. Account with collection history
2. User has permission to edit Account fields
3. User logged in as Bid Manager or authorized user

### Test Steps

1. Navigate to Account record
2. Review account notes or activity history to identify collection escalations
3. Count escalations in last 12 months: e.g., 2 escalations
4. On Account record, click Edit
5. Locate field "Number_of_Customer_Escalations__c"
6. Enter value = 2
7. Save Account
8. Trigger payment history analysis (submit binding costing or click recalculate button)
9. Navigate to Payment History Analysis related list
10. Open latest Payment History Analysis record
11. Locate "Number_Customer_Escalations__c" field
12. Verify value = 2 (copied from Account)
13. Locate "Customer_Escalations_Score__c" field
14. Verify score based on matrix:
    - 0 escalations = Score 5
    - 1-2 escalations = Score 4 (2 falls here)
    - 3-4 escalations = Score 3
    - 5-6 escalations = Score 2
    - >6 escalations = Score 1
15. Verify score = 4
16. Check that score is read-only (calculated by system)
17. Verify audit history shows who entered the number and when

### Expected Result
- Account should have field for manual entry of escalation count
- Only authorized users can edit (Bid Manager, Account Manager, Collections team)
- System should auto-calculate score when payment history analysis runs
- Score range: 1-5 based on escalation count
- Score should be read-only
- Audit trail should capture: invoice reference, escalations entered, user, timestamp
- If no escalations in last 12 months, score defaults to 5
- Scoring logic resets to only last 12 months even if older data exists

---

## Test Case 8: Payment History Final Score Calculation Using Weightage

### Scenario / Test Description
Verify that the system calculates the final Payment History Score by multiplying each metric score by its assigned weight and summing the results.

### Preconditions
1. Account with all 5 payment history metrics calculated:
   - Average Payment Delay Score = 4
   - % Overdue Invoices Score = 3
   - % Outstanding Amount Overdue Score = 3
   - Short Payments/Disputes Score = 5
   - Customer Escalations Score = 4
2. Weightage configured in metadata:
   - Average Payment Delay: 30%
   - % Overdue Invoices: 25%
   - % Outstanding Amount: 20%
   - Short Payments/Disputes: 15%
   - Customer Escalations: 10%

### Test Steps

1. Navigate to Account record
2. Navigate to Payment History Analysis related list
3. Open the latest Payment History Analysis record
4. Verify all 5 parameter scores are populated:
   - Average Payment Delay Score = 4
   - Percentage Overdue Invoices Volume Score = 3
   - Percentage Outstanding Amount Overdue Score = 3
   - Short Payments Disputes Score = 5
   - Customer Escalations Score = 4
5. Navigate to Account record
6. Locate the "Calculate Account Score" button
7. Click "Calculate Account Score" button
8. Wait for calculation to complete
9. Verify success message appears
10. On Account record, locate field "Payment_History_Analysis_Score__c"
11. Manually verify calculation:
    - (4 × 0.30) + (3 × 0.25) + (3 × 0.20) + (5 × 0.15) + (4 × 0.10)
    - = 1.2 + 0.75 + 0.6 + 0.75 + 0.4
    - = 3.7
12. Verify the displayed score matches (rounded to nearest whole number if needed)
13. The field should show "4" (if rounded) or "3.7" (if decimals allowed)
14. Verify this is a read-only field (users cannot edit)
15. Navigate to Payment History Analysis record
16. Verify the final score is also stored there
17. Check timestamp of calculation
18. Verify "Calculated_By__c" field shows current user
19. Check that score is locked and cannot be manually changed

### Expected Result
- System should calculate weighted average using formula:
  - (Score1 × Weight1) + (Score2 × Weight2) + ... (Score5 × Weight5)
- Weights should come from Custom Metadata configuration
- Final score should be rounded to nearest whole number
- Score should be displayed on Account record in "Payment_History_Analysis_Score__c" field
- Score should be read-only for all users
- Score should recalculate automatically when new transaction is created or invoice is released
- Calculation should be logged with user and timestamp
- Each calculation should create an audit entry in logs object

---

## Test Case 9: Payment History Score Interpretation and Risk Level Assignment

### Scenario / Test Description
Verify that the system automatically assigns a Risk Level and Interpretation text based on the final Payment History Score.

### Preconditions
1. Account with Payment History Analysis Score calculated
2. User viewing the Account record

### Test Steps

1. Navigate to Account record where Payment History Score has been calculated
2. Locate field "Payment_History_Analysis_Score__c"
3. Note the score value (e.g., Score = 2)
4. Locate field "Payment_History_Risk_Level__c"
5. Locate field "Payment_History_Risk_Interpretation__c"
6. Verify the system has automatically assigned risk level and interpretation based on score:

   **If Score = 5:**
   - Risk Level = "Very Low Risk"
   - Interpretation = "No material issues"
   
   **If Score = 4:**
   - Risk Level = "Low Risk"
   - Interpretation = "Few isolated issues"
   
   **If Score = 3:**
   - Risk Level = "Moderate Risk"
   - Interpretation = "Some recurring issues"
   
   **If Score = 2:**
   - Risk Level = "High Risk"
   - Interpretation = "Regular, significant issues"
   
   **If Score = 1:**
   - Risk Level = "Very High Risk"
   - Interpretation = "Frequent, severe issues"

7. For Score = 2, verify:
   - Payment_History_Risk_Level__c = "High Risk"
   - Payment_History_Risk_Interpretation__c = "Regular, significant issues"
8. Verify these fields are read-only (formula fields or auto-populated)
9. Navigate to Opportunity approval screen
10. Verify approver can see Risk Level and Interpretation
11. Verify these fields help approver make informed decision on binding quote

### Expected Result
- Risk Level and Interpretation should auto-populate based on Payment History Score
- Mapping should follow exact matrix as specified
- Fields should be read-only (users cannot override)
- Should be prominently displayed on Account record
- Should be visible to approvers during binding quote approval
- Color coding or visual indicators may enhance display (red for high risk, green for low risk)

---

## Test Case 10: Payment History Analysis - Data Snapshot and History Tracking

### Scenario / Test Description
Verify that each payment history evaluation creates a separate record in Payment History Analysis object for audit and history tracking.

### Preconditions
1. Account with some payment history
2. User creates multiple binding quotes over time

### Test Steps

1. Navigate to Account record
2. Create first binding costing and submit for approval (Time T1)
3. Payment history analysis runs
4. Navigate to "Payment History Analysis" related list on Account
5. Verify one record created with:
   - Name/Record ID
   - Analysis_Date__c = T1
   - All 5 parameter scores
   - Final Payment History Score
6. Wait a few weeks
7. New invoices are synced from ERP
8. Create second binding costing and submit (Time T2)
9. Payment history re-evaluation runs
10. Navigate to Payment History Analysis related list
11. Verify TWO records now present:
    - Record 1: Analysis_Date__c = T1 (older)
    - Record 2: Analysis_Date__c = T2 (newer)
12. Open Record 1 (older)
13. Verify it shows historical data (scores from T1)
14. Open Record 2 (newer)
15. Verify it shows current data (scores from T2)
16. Compare the two records
17. Verify different scores if payment behavior changed
18. On Account record, verify "Payment_History_Analysis_Score__c" shows the LATEST score (from T2)
19. Check that older records are retained for audit
20. Verify cannot delete Payment History Analysis records (audit protection)

### Expected Result
- Each payment history evaluation should create a NEW Payment History Analysis record
- Old records should be preserved (not updated in place)
- This creates a complete audit trail of payment behavior over time
- Account should display the most recent score
- Historical records should be accessible in related list
- Reports should be able to show score trends over time
- Records should be read-only after creation (no edits allowed)
- Snapshot should capture: All parameter scores, final score, analysis date, calculated by user, invoice count, date range analyzed

---

## Test Case 11: Payment History Analysis - Record Locking Until Next Assessment

### Scenario / Test Description
Verify that Payment History Score is locked and cannot be modified until the next scheduled assessment date or adhoc check.

### Preconditions
1. Account with Payment History Analysis completed
2. Credit_Evaluation_Date__c = 1-Jan-2025
3. Next_Credit_Evaluation_Date__c = 1-Jan-2026
4. Current date = 15-Jun-2025 (between assessment dates)

### Test Steps

1. Navigate to Account record
2. Verify "Payment_History_Analysis_Score__c" displays a score (e.g., 4)
3. Try to edit the Account record
4. Check if Payment_History_Analysis_Score__c field is editable
5. Verify field is read-only (grayed out or no edit pencil icon)
6. Try to manually change the score using Developer Console or Data Loader
7. Verify system prevents update with error message:
   - "Credit Worthiness Score cannot be modified until the next assessment date or adhoc check is required"
8. Navigate to Payment History Analysis related list
9. Try to edit the existing Payment History Analysis record
10. Verify record is read-only
11. Check Account record for lock indicator
12. Verify "Score_Locked__c" = TRUE (or similar field)
13. Verify "Next_Credit_Evaluation_Date__c" displays future date
14. Test adhoc check (if available):
    - Click "Request Adhoc Credit Check" button
    - Credit team approves adhoc check
    - Verify score unlocks temporarily
    - New payment history analysis runs
    - New score is calculated and stored
    - Lock re-applies after calculation
15. Wait until Next_Credit_Evaluation_Date__c arrives (or change system date for testing)
16. Verify score unlocks on assessment due date
17. New payment history analysis can be triggered
18. Score recalculated with latest data
19. Lock re-applies with new next assessment date

### Expected Result
- Payment History Score should be locked between Credit_Evaluation_Date and Next_Credit_Evaluation_Date
- No users (including admins) should be able to manually modify the score
- Only system calculation via "Calculate Account Score" process can update
- Lock should automatically release on Next_Credit_Evaluation_Date
- Adhoc assessment (if approved by credit team) can trigger recalculation before scheduled date
- All lock/unlock events should be logged in audit history
- Score_Locked__c field should indicate lock status
- Date_of_Last_Assessment__c should show when score was last calculated
- Validation rule should prevent manual updates during lock period

---

## Test Case 12: Trigger Payment History Re-evaluation on New Transaction

### Scenario / Test Description
Verify that payment history score is recalculated when a new order is created or invoice is released.

### Preconditions
1. Account with Payment History Score = 4, calculated 30 days ago
2. New invoices synced from ERP since last calculation
3. User creating new opportunity/order

### Test Steps

1. Navigate to Account record
2. Note current "Payment_History_Analysis_Score__c" = 4
3. Note "Last_Assessment_Date__c" = 30 days ago
4. Create new Opportunity for this account
5. Create new Order from the opportunity
6. Save the Order
7. Check if payment history re-evaluation triggers automatically
8. Navigate to Account record
9. Verify "Payment_History_Check_Required__c" = TRUE (triggered by new order)
10. Create a binding Costing Sheet
11. Submit for approval
12. Verify payment history re-evaluation runs
13. Navigate to Payment History Analysis related list
14. Verify a NEW record is created with today's date
15. Open new Payment History Analysis record
16. Verify it includes data from newly released invoices
17. Check if score has changed based on new data
18. On Account, verify "Payment_History_Analysis_Score__c" updated with new score
19. Verify "Last_Assessment_Date__c" = TODAY
20. Check that approver sees updated score during approval

### Expected Result
- Payment history should recalculate when:
  - New order is created
  - New invoice is released/synced from ERP
  - Binding costing submitted for approval
- Should always use last 12 months of data (rolling window)
- New Payment History Analysis record created (not update existing)
- Account score field updated with latest score
- Assessment date updated
- Approvers always see most recent score
- Automatic trigger ensures score is always current for decision-making

---

## Test Case 13: Payment History Analysis for Different Customer Types

### Scenario / Test Description
Verify that payment history analysis is applied only to eligible customer types and bypassed for exempt types.

### Preconditions
1. Multiple accounts with different Customer Types
2. User creating binding quotes for each

### Test Steps

1. **Test Scenario A - Private Companies (Eligible)**
   - Navigate to Account with Customer Type = "Private Companies"
   - Trigger binding costing approval
   - Verify payment history analysis runs
   - Verify Payment History Analysis record created

2. **Test Scenario B - Government Related (Eligible)**
   - Account with Customer Type = "Government Related Companies"
   - Trigger binding costing approval
   - Verify payment history analysis runs

3. **Test Scenario C - UAE Government (Eligible)**
   - Account with Customer Type = "UAE government"
   - Trigger binding costing approval
   - Verify payment history analysis runs

4. **Test Scenario D - Group Companies (Exempt)**
   - Account with Customer Type = "Group Companies"
   - Trigger binding costing approval
   - Verify payment history analysis DOES NOT run
   - Verify no Payment History Analysis record created
   - Verify binding quote approval proceeds without this check

5. **Test Scenario E - Individuals (Exempt)**
   - Account with Customer Type = "Individuals"
   - Verify payment history bypassed

6. **Test Scenario F - Employees (Exempt)**
   - Account with Customer Type = "Employees"
   - Verify payment history bypassed

7. For each eligible type, verify all 5 parameters calculated
8. For each exempt type, verify:
   - Payment_History_Analysis_Score__c = Not Applicable or NULL
   - No validation on payment history for binding quotes
   - Can proceed without payment history data

### Expected Result
- System should check Customer Type field before triggering analysis
- Eligible types (Private, Govt Related, UAE Govt, Other Govts, Other Related Parties) → Analysis required
- Exempt types (Group Companies, Individuals, Employees) → Analysis bypassed
- Metadata configuration should control which types require analysis
- Binding quote approval should check Customer Type
- Non-binding quotes should always bypass regardless of type
- UI should show "Not Applicable" or hide payment history section for exempt types

---

## Test Case 14: View Payment History Details During Costing Approval

### Scenario / Test Description
Verify that approvers can view detailed payment history score and parameter breakdown when approving a binding costing sheet.

### Preconditions
1. Binding costing sheet submitted for approval
2. Payment history analysis completed for the account
3. User logged in as approver (Finance Manager, Credit Manager, or designated approver)

### Test Steps

1. Login as approver user
2. Navigate to Home → Approvals or Approval Requests
3. Locate pending costing sheet approval
4. Click on the approval request
5. Review approval details page
6. Locate "Account Information" section
7. Verify can see link to Account record
8. Click link to view Account
9. On Account record, verify can see:
   - Payment_History_Analysis_Score__c (e.g., Score = 3)
   - Payment_History_Risk_Level__c (e.g., "Moderate Risk")
   - Payment_History_Risk_Interpretation__c (e.g., "Some recurring issues")
10. Click on Payment History Analysis related list
11. Open latest Payment History Analysis record
12. Verify can see all 5 parameter details:
    - Average Payment Delay: X days → Score Y
    - % Overdue Invoices: X% → Score Y
    - % Outstanding Amount: X% → Score Y
    - Short Payments/Disputes: X count → Score Y
    - Customer Escalations: X count → Score Y
13. Verify can see final weighted score
14. Return to approval request
15. Based on payment history score, make approval decision
16. If score is 1-2 (High/Very High Risk), consider rejecting
17. If score is 4-5 (Low Risk), approve
18. Add comments referencing payment history score
19. Submit approval/rejection
20. Verify decision is recorded with payment score context

### Expected Result
- Approvers should have clear visibility of payment history score during approval
- Account should display: Final score, Risk level, Interpretation text
- Detailed parameter breakdown should be accessible via related list
- Score should inform approval decision
- Approvers can drill down into Payment History Analysis record for details
- System should present this information in a user-friendly format
- High-risk accounts should have visual indicators (red flag, warning icon)
- Approval comments can reference the payment score
- Decision history should be linked to account score

---

## Test Case 15: Payment History Analysis - Integration with Invoice Sync

### Scenario / Test Description
Verify that invoices and payment records synced from ERP via OIC integration are used for payment history calculations.

### Preconditions
1. ERP system has invoice and payment data for test account
2. OIC integration configured for daily sync
3. Account in Salesforce linked to ERP customer (ERP_ID__c populated)

### Test Steps

1. **Verify Initial State**
   - Navigate to Account in Salesforce
   - Check "Invoices" or "Orders" related list
   - Note count of existing invoice records

2. **Simulate/Wait for ERP Sync**
   - If OIC scheduled job not running, trigger manually (if possible)
   - Or wait for next scheduled sync (typically daily cron job)
   - Check integration logs for sync completion

3. **Verify Invoice Sync**
   - Navigate to Account → Invoices/Orders related list
   - Click "View All"
   - Verify new invoice records appeared
   - Open an invoice record
   - Verify fields populated:
     - Invoice_Number__c
     - Invoice_Date__c
     - Invoice_Amount__c
     - Payment_Due_Date__c
     - Payment_Actual_Received_Date__c
     - Outstanding_Amount__c
     - Payment_Status__c
     - Linked to Order__c
     - Order linked to Account__c

4. **Trigger Payment History Analysis**
   - Create Opportunity for the account
   - Create binding Costing Sheet
   - Submit for approval
   - Payment history analysis triggers

5. **Verify Analysis Uses Synced Data**
   - Navigate to Payment History Analysis record
   - Verify "Invoice_Count_Analyzed__c" includes the newly synced invoices
   - Verify "Date_Range_Analyzed__c" shows last 12 months
   - Verify calculations use synced invoice data:
     - Average delay calculated from Payment_Actual_Received_Date - Payment_Due_Date
     - Overdue invoices counted from synced data
     - Outstanding amounts summed from synced data

6. **Verify Data Integrity**
   - Cross-check one invoice calculation manually:
     - Find invoice in Salesforce
     - Note Payment_Due_Date = 1-Mar-2025
     - Note Payment_Actual_Received_Date = 15-Mar-2025
     - Calculate delay = 14 days
     - Verify this delay is included in average calculation
   - Repeat for 2-3 invoices to ensure accuracy

### Expected Result
- OIC integration should sync invoice and payment data daily from ERP
- Invoices should link to Orders, which link to Accounts
- Payment history analysis should query these synced invoice records
- Only invoices from last 12 months should be included
- Calculations should be accurate based on synced data
- If no synced data available, system should handle gracefully (show warning or score = N/A)
- Integration failures should be logged in Error_Logs__c
- Manual entries (disputes, escalations) should supplement synced data
- Final score combines automated calculations and manual inputs

---

## Test Case 16: Payment History Analysis - Threshold-Based Requirement

### Scenario / Test Description
Verify that payment history analysis is required only for binding quotes above a certain value threshold.

### Preconditions
1. Threshold configured in metadata: 3,000 AED
2. Account: Existing customer, eligible Customer Type
3. User creating costing sheets

### Test Steps

1. **Test Below Threshold**
   - Navigate to Opportunity with Amount = 2,500 AED (below 3,000 threshold)
   - Create Costing Sheet with Type = "Firm (Binding)"
   - Fill all fields
   - Submit for approval
   - Verify payment history analysis is NOT triggered
   - Verify approval proceeds without payment history check
   - Verify no validation error

2. **Test Above Threshold**
   - Navigate to Opportunity with Amount = 5,000 AED (above 3,000 threshold)
   - Create Costing Sheet with Type = "Firm (Binding)"
   - Fill all fields
   - Submit for approval
   - Verify payment history analysis IS triggered
   - Verify system checks payment history score
   - Verify approver can see payment history details

3. **Test Exactly at Threshold**
   - Opportunity Amount = 3,000 AED (exactly at threshold)
   - Create binding costing
   - Submit for approval
   - Verify payment history analysis IS triggered (threshold inclusive)

4. **Verify Metadata Configuration**
   - Navigate to Setup → Custom Metadata Types
   - Find metadata for payment history configuration
   - Verify "Threshold_Value__c" = 3000
   - Verify "Is_Required__c" = TRUE for eligible customer types

### Expected Result
- Payment history analysis should be threshold-based
- For existing customers with eligible Customer Types:
  - Value < 3,000 AED → Payment history check bypassed
  - Value >= 3,000 AED → Payment history check required
- Threshold should be configurable via Custom Metadata
- For NEW customers:
  - Value < 3,000 AED → No credit check, no payment history (can onboard without check)
  - Value >= 3,000 AED → Credit worthiness check required, payment history N/A (no history yet)
- System should check opportunity/order amount against threshold
- Different thresholds can be configured per Customer Classification if needed

---

## Test Case 17: Calculate Account Score Button - Manual Trigger

### Scenario / Test Description
Verify that the "Calculate Account Score" button on Account record triggers score calculation for both Payment History and Credit Worthiness.

### Preconditions
1. Account with:
   - ERP_ID__c populated (existing customer)
   - Last 12 months invoice data synced
   - Manual fields filled (disputes, escalations)
   - Credit worthiness parameters evaluated by credit team
2. User logged in as Account Manager or Credit Control Team member
3. Button visible on Account record page

### Test Steps

1. Navigate to Account record
2. Verify account is existing customer (ERP_ID__c has value)
3. Locate "Calculate Account Score" button on the page
4. Before clicking, note current scores:
   - Payment_History_Analysis_Score__c (may be old or blank)
   - Credit_Worthiness_Score__c
   - Account_Score__c (average of both)
5. Click "Calculate Account Score" button
6. Verify loading spinner or processing message appears
7. Wait for calculation to complete
8. Verify success message: "Account Score calculated successfully" (or similar)
9. Refresh the page
10. Verify updated scores:
   - Payment_History_Analysis_Score__c = new calculated value
   - Credit_Worthiness_Score__c = calculated value
   - Account_Score__c = (Payment_History_Score + Credit_Worthiness_Score) / 2
11. Verify all scores are rounded to nearest whole number
12. Check "Last_Calculation_Date__c" = TODAY
13. Check "Calculated_By__c" = Current User
14. Navigate to Payment History Analysis related list
15. Verify new Payment History Analysis record created with today's date
16. Navigate to Logs related list (if available)
17. Verify audit log created with:
    - Old score values (JSON)
    - New score values (JSON)
    - Changed fields list
    - User who triggered calculation
    - Timestamp
18. For NEW customers (no ERP ID):
    - Verify Account_Score__c = Credit_Worthiness_Score__c only
    - Payment history not applicable

### Expected Result
- Button should be visible to authorized users (Account Managers, Credit Control Team)
- Clicking button should trigger Apex controller method
- Apex should:
  1. Check if existing customer (ERP_ID__c exists)
  2. If existing: Calculate both Payment History and Credit Worthiness scores
  3. If new: Calculate only Credit Worthiness score
  4. Calculate final Account Score (average or credit only)
  5. Create Payment History Analysis record
  6. Create audit log entry
  7. Update account fields
  8. Set Last_Calculation_Date and Next_Evaluation_Date
- All calculations should use weightage from Custom Metadata
- Scores should be stored on Account and child objects
- Button should show processing state (disabled during calculation)
- Error handling should display user-friendly messages if calculation fails

---

## Test Case 18: Payment History Calculation - Edge Cases

### Scenario / Test Description
Test payment history calculation with edge cases and boundary conditions.

### Preconditions
1. Test accounts with various data scenarios
2. User with access to trigger payment history analysis

### Test Steps

1. **Edge Case A: No Invoices in Last 12 Months**
   - Account with ERP_ID but no invoices in last 12 months
   - Trigger payment history analysis
   - Expected: System handles gracefully, shows "Insufficient Data" or defaults to middle score

2. **Edge Case B: All Invoices Paid Early**
   - All invoices paid before due date (negative delay)
   - Calculate average delay
   - Expected: Average delay = 0 or negative, Score = 5

3. **Edge Case C: Mix of Very Early and Very Late Payments**
   - 5 invoices paid 30 days early (delay = -30)
   - 5 invoices paid 30 days late (delay = +30)
   - Average delay = 0
   - Expected: Score based on average (Score = 5 for 0 days)

4. **Edge Case D: Exactly 12 Months of Data**
   - Invoices from exactly 365 days ago to today
   - Expected: All invoices included, calculations accurate

5. **Edge Case E: More Than 12 Months of Data**
   - Invoices from 18 months ago to today
   - Expected: Only last 12 months included in calculation

6. **Edge Case F: All Disputes, No Escalations**
   - Number_of_Short_Payments_Disputes__c = 10
   - Number_of_Customer_Escalations__c = 0
   - Expected: Disputes score = 1, Escalations score = 5

7. **Edge Case G: Zero Overdue Invoices**
   - All 20 invoices paid on time
   - Expected: Overdue volume % = 0%, Score = 5

8. **Edge Case H: All Invoices Overdue**
   - All 15 invoices were/are overdue
   - Expected: Overdue volume % = 100%, Score = 1

9. For each edge case, verify:
   - System handles without errors
   - Appropriate score assigned
   - Calculations logged
   - Approver can still make informed decision

### Expected Result
- System should handle all edge cases without crashing
- When insufficient data: Show warning, allow manual override by credit team, or default to conservative score
- Negative delays (early payments) should be treated as 0 days delay (positive for customer)
- Rolling 12-month window should be strictly enforced
- Boundary conditions (0%, 100%, exactly at threshold) should be handled correctly
- Error handling should be graceful with clear messages
- Edge cases should be logged for review
- Credit team can manually intervene if automated calculation is not appropriate

---

## Test Case 19: Payment History Report - Monitoring Dashboard

### Scenario / Test Description
Verify that credit control team can monitor payment history scores and upcoming re-evaluations via dashboard.

### Preconditions
1. Multiple accounts with payment history scores
2. Some accounts have Next_Credit_Evaluation_Date__c approaching
3. User logged in as Credit Control Team member
4. Dashboard configured for payment history monitoring

### Test Steps

1. Login as Credit Control Team member
2. Navigate to Home or Reports tab
3. Locate "Credit Control Dashboard" or "Payment History Monitoring Dashboard"
4. Verify dashboard components display:

5. **Component 1: Accounts by Payment History Risk Level**
   - Chart showing count by risk level
   - Very High Risk (Score 1): X accounts
   - High Risk (Score 2): X accounts
   - Moderate Risk (Score 3): X accounts
   - Low Risk (Score 4): X accounts
   - Very Low Risk (Score 5): X accounts

6. **Component 2: Payment History Assessments Expiring Soon**
   - List view showing accounts where Next_Credit_Evaluation_Date__c <= 60 days from today
   - Columns: Account Name, Current Score, Risk Level, Expiry Date, Days Until Expiry, Account Manager

7. **Component 3: Overdue Payment History Assessments**
   - Accounts where Next_Credit_Evaluation_Date__c < TODAY
   - Shows expired assessment date and days overdue
   - Highlighted in red for visibility

8. **Component 4: Pending Payment History Assessments**
   - Accounts requiring assessment (new customers or expired)
   - Status: Pending, In Progress, Completed
   - Filter by Cluster, BU, Account Manager

9. Click on each component to drill down
10. From "Expiring Soon" list, click on an account
11. Verify can initiate re-assessment directly
12. Apply filters:
    - Filter by Business Unit
    - Filter by Cluster Company
    - Filter by Account Manager
    - Filter by Risk Level
13. Export data to Excel for offline analysis
14. Schedule dashboard email subscription (if available)

### Expected Result
- Dashboard should provide complete visibility of payment history status across all accounts
- Should identify accounts needing attention (expiring soon, expired, high risk)
- Should allow filtering and drill-down
- Should be accessible only to Credit Control Team
- Should refresh data automatically
- Should highlight overdue assessments in red for priority
- Should link directly to account records for action
- Should support export for reporting to management

---

## Test Case 20: Payment History Analysis - Error Handling and Logging

### Scenario / Test Description
Verify proper error handling when payment history calculation fails or encounters issues.

### Preconditions
1. Account with potentially problematic data
2. User triggering payment history analysis

### Test Steps

1. **Scenario A: No Invoice Data Synced from ERP**
   - Account with ERP_ID but no invoice records in Salesforce
   - Trigger payment history analysis
   - Expected: System shows user-friendly error or warning
   - Message: "Insufficient invoice data for payment history analysis. Please ensure invoices are synced from ERP."
   - Allows user to proceed with manual review or cancel

2. **Scenario B: Invoice Missing Required Fields**
   - Invoice record with Payment_Due_Date__c = NULL
   - Trigger analysis
   - Expected: System skips incomplete invoices, logs warning
   - Calculation proceeds with valid invoices only
   - Warning shown to user about incomplete data

3. **Scenario C: Calculation Timeout**
   - Account with 1000+ invoices
   - Trigger calculation
   - Expected: If calculation takes too long, handled via batch/queueable
   - User sees: "Calculation in progress, you will be notified when complete"

4. **Scenario D: API/Integration Failure**
   - ERP integration is down
   - User tries to trigger analysis
   - Expected: Error message displayed
   - "Unable to complete analysis due to integration issue. Please try again later."
   - Error logged in Error_Logs__c object

5. For each error scenario:
   - Navigate to Setup → Error Logs (or custom Error_Logs__c object)
   - Verify error logged with:
     - Class_Name__c
     - Method_Name__c
     - Log_Message__c
     - Stack_Trace__c
     - Related_Record_ID__c (Account ID)
     - Timestamp
   - Verify user sees friendly error message (not technical stack trace)
   - Verify can retry after error resolved
   - Verify partial failures don't corrupt existing data

### Expected Result
- All errors should be caught and handled gracefully
- User should never see raw Apex exception messages
- Error messages should be actionable ("Please do X" or "Contact Y")
- All errors should be logged in Error_Logs__c for troubleshooting
- System should not calculate incorrect scores on error (better to show N/A than wrong score)
- Partial calculations should roll back (all-or-nothing)
- Users should be able to retry failed calculations
- Admin should be able to review error logs and fix issues
- Critical errors should alert support team

---

## Test Execution Checklist for Abhijeet's Features

Before marking BRN#026 (Payment History) as complete, verify:

- [ ] Test Case 1: Eligibility determination works correctly
- [ ] Test Case 2: Non-binding quotes bypass analysis
- [ ] Test Case 3: Average payment delay calculation accurate
- [ ] Test Case 4: % Overdue invoices (volume) calculation accurate
- [ ] Test Case 5: % Outstanding amount overdue calculation accurate
- [ ] Test Case 6: Manual entry of disputes tracked
- [ ] Test Case 7: Manual entry of escalations tracked
- [ ] Test Case 8: Final weighted score calculation correct
- [ ] Test Case 9: Risk level and interpretation assigned correctly
- [ ] Test Case 10: Payment History Analysis records created (not updated)
- [ ] Test Case 11: Score locking between assessment dates works
- [ ] Test Case 12: Re-evaluation triggered on new transactions
- [ ] Test Case 13: Customer type eligibility enforced
- [ ] Test Case 14: Approvers can view scores during approval
- [ ] Test Case 15: ERP invoice sync integration working
- [ ] Test Case 16: Threshold-based requirements enforced
- [ ] Test Case 17: Calculate button triggers all calculations
- [ ] Test Case 18: Error handling works for all scenarios
- [ ] Test Case 19: Monitoring dashboard functional
- [ ] Test Case 20: Audit logging complete

**Apex Test Coverage:** Verify test class for Payment History helper achieves >75% coverage

---

## Known Issues / Items to Verify

Based on Sprint 4 status, verify:

1. **Invoice Sync from ERP**
   - Daily cron job configured in OIC
   - Invoice records linking to Orders → Accounts correctly
   - All required fields populated (Payment_Due_Date, Payment_Actual_Received_Date, Outstanding_Amount)

2. **Custom Metadata Configuration**
   - Payment_History_Parameter_Weightage__mdt configured with all 5 parameters
   - Payment_History_Eligibility__mdt configured with Customer Types
   - Threshold values configured per Customer Classification

3. **Payment History Analysis Object**
   - Object created with all required fields
   - Master-detail or lookup relationship to Account
   - Rollup formulas for calculations
   - Page layout configured
   - Related list on Account page layout

4. **Account Fields Created**
   - Payment_History_Analysis_Score__c
   - Credit_Worthiness_Score__c
   - Account_Score__c
   - Payment_History_Risk_Level__c
   - Payment_History_Risk_Interpretation__c
   - Number_of_Short_Payments_Disputes__c
   - Number_of_Customer_Escalations__c
   - Credit_Evaluation_Date__c
   - Next_Credit_Evaluation_Date__c
   - Score_Locked__c
   - Last_Calculation_Date__c

5. **Button Created**
   - "Calculate Account Score" button on Account page layout
   - Calls Apex controller
   - Proper error handling

---

## Test Data Requirements

To execute these test cases, set up:

### Accounts
- Account 1: Existing customer, Private Company, 20 invoices (mix of on-time and late)
- Account 2: Existing customer, Group Company (exempt)
- Account 3: New customer, no ERP ID, no invoices
- Account 4: Existing customer, all invoices paid early
- Account 5: Existing customer, all invoices severely late (>60 days)

### Invoice Data
- Create/sync at least 100 invoice records
- Spread across last 18 months (test 12-month filter)
- Mix of:
  - On-time payments (delay = 0)
  - Slightly late (5-15 days)
  - Moderately late (15-30 days)
  - Severely late (>60 days)
  - Some still outstanding (no payment date)

### Manual Field Data
- Set disputes count: 0, 1, 3, 5, 8 across different accounts
- Set escalations count: 0, 2, 4, 7 across accounts

### Opportunities and Costing Sheets
- Create opportunities with amounts: 1,000 AED, 3,000 AED, 10,000 AED, 100,000 AED
- Create both Binding and Non-Binding costing sheets

---

## Defect Logging - Specific to BRN#026

When logging defects for payment history features:

**Template:**
```
Defect ID: BRN026-DEF-XXX
Module: Account Management - Payment History Analysis
Feature: [Specific feature, e.g., "Average Payment Delay Calculation"]
Developer: Abhijeet

Description: [Clear description of the issue]

Steps to Reproduce:
1. [Step 1]
2. [Step 2]
...

Expected Result: [What should happen]
Actual Result: [What actually happened]

Data Used:
- Account ID: [ID]
- Invoice IDs: [IDs]
- Manual calculation: [Show your math]
- System calculation: [What system showed]

Screenshot: [Attach]
Severity: [Critical/High/Medium/Low]
```

---

## Tester Notes

### Tips for Testing Payment History

1. **Verify Calculations Manually**
   - Always calculate expected values manually first
   - Compare with system calculations
   - Small differences (<1%) may be due to rounding

2. **Check Last 12 Months Window**
   - Filter invoices by date
   - Count only invoices in range
   - Older invoices should be excluded

3. **Understand Weightages**
   - Average Payment Delay: 30%
   - % Overdue Volume: 25%
   - % Outstanding Amount: 20%
   - Disputes: 15%
   - Escalations: 10%
   - Total: 100%

4. **Check Audit Trail**
   - Every calculation should be logged
   - Compare old vs new scores
   - Verify user and timestamp

5. **Test with Real-ish Data**
   - Use realistic invoice amounts
   - Use realistic payment delays
   - Don't use extreme edge cases only

---

## Success Criteria

BRN#026 (Payment History Analysis by Abhijeet) is complete when:

- ✅ All 20 test cases pass
- ✅ Calculations are accurate (manual verification matches system)
- ✅ All 5 parameters calculate correctly
- ✅ Weighted score calculation is correct
- ✅ Risk levels assigned appropriately
- ✅ Locking mechanism works
- ✅ Triggers at correct times (binding quote submission)
- ✅ Bypasses appropriately (non-binding, exempt customer types, below threshold)
- ✅ Integration with ERP invoice sync working
- ✅ Error handling covers all scenarios
- ✅ Audit logging complete
- ✅ Dashboard displays correctly
- ✅ Approvers can view scores
- ✅ No critical or high severity defects open
- ✅ Apex test coverage >75%

---

**Testing Coordinator:** QA Lead  
**Expected Test Duration:** 2-3 days (full manual testing)  
**Automation Potential:** Medium (some UI tests, mostly backend calculations)  
**Risk Level:** High (impacts credit decisions - requires thorough testing)

---

**Document Version:** 1.0  
**Created:** October 13, 2025  
**Status:** Ready for Test Execution (when Sprint 4 features are deployed)

