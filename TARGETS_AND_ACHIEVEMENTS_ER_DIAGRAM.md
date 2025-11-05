# Targets and Achievements - ER Diagram

This document provides a comprehensive Entity-Relationship (ER) diagram for the Targets and Achievements module based on the user stories, acceptance criteria, and solutions.

## ER Diagram (Mermaid Format)

```mermaid
erDiagram
    %% Core Objects
    Target__c ||--o{ Achivement__c : "has many"
    Target__c }o--|| User : "belongs to"
    
    %% Opportunity Split Objects
    Opportunity ||--o{ OpportunitySplit : "has many"
    OpportunitySplit }o--|| User : "split owner"
    
    %% Achievement Relationships
    Achivement__c }o--|| Opportunity : "references"
    Achivement__c }o--|| Order : "linked to"
    Achivement__c }o--|| User : "attributed to"
    
    %% Order and Invoice Flow
    Opportunity ||--o{ Order : "generates"
    Order }o--o{ Invoice__c : "may have"
    Invoice__c ||--o{ Receipts__c : "has payments"
    
    %% Lead Conversion
    Lead }o--|| User : "owned by"
    Lead }o--o| Opportunity : "converts to"
    
    %% Account Relationships
    Opportunity }o--|| Account : "belongs to"
    Invoice__c }o--|| Account : "customer"
    
    %% Target Object Fields
    Target__c {
        Id Id PK
        string Name
        Id User__c FK "Lookup to User"
        string Target_type__c "Picklist: Direct Sales Order, Lead Conversion"
        string Cluster__c "Picklist"
        string Company__c "Picklist"
        string Business_Unit__c "Picklist"
        string Territory__c "Picklist"
        string Period_type__c "Picklist: Month/Quarter/Year"
        date Period_Start_Date__c
        currency Target_Amount__c
        currency Actual_Amount__c "Rollup from Achievements"
        number Lead_Conversion_Achieved_Count__c
        string CurrencyIsoCode "Default: AED"
        Id OwnerId "Creator/Manager"
        datetime CreatedDate
        datetime LastModifiedDate
    }
    
    %% Achievement Object Fields
    Achivement__c {
        Id Id PK
        Id Target__c FK "Master-Detail to Target__c"
        Id Opportunity__c FK "Lookup to Opportunity"
        Id Order__c FK "Lookup to Order"
        Id User__c FK "Lookup to User (Split Owner)"
        date Achieved_Date__c "Opportunity Close Date"
        currency Native_Amount__c "Original Opportunity Amount"
        currency Converted_Amount__c "Converted to Target Currency"
        number Split_Percentage__c "From Opportunity Split"
        currency Realized_Revenue_Original_Currency__c "Payment Amount (Original)"
        currency Realized_Revenue_AED__c "Payment Amount (AED)"
        currency Total_Payments_Applied__c "Rollup from Receipts"
        date Invoice_Posting_Date__c "GL Date from Invoice"
        datetime CreatedDate
        datetime LastModifiedDate
    }
    
    %% Opportunity Split Object Fields
    OpportunitySplit {
        Id Id PK
        Id OpportunityId FK "Master-Detail to Opportunity"
        Id SplitOwnerId FK "Lookup to User"
        Id SplitTypeId FK "Lookup to SplitType"
        number SplitPercentage "Must sum to 100%"
        currency SplitAmount "Calculated: Opp Amount × Percentage"
        string SplitNote "Optional notes"
        datetime CreatedDate
        datetime LastModifiedDate
    }
    
    %% Opportunity Object (Standard + Custom Fields)
    Opportunity {
        Id Id PK
        Id OwnerId FK "Lookup to User"
        Id AccountId FK "Lookup to Account"
        string Name
        string StageName "Picklist"
        date CloseDate
        currency Amount
        string CurrencyIsoCode
        string Business_Unit__c "Custom Field"
        datetime CreatedDate
        datetime LastModifiedDate
    }
    
    %% Order Object (Standard)
    Order {
        Id Id PK
        Id OpportunityId FK "Lookup to Opportunity"
        Id AccountId FK "Lookup to Account"
        string OrderNumber
        string Status
        datetime CreatedDate
        datetime LastModifiedDate
    }
    
    %% Invoice Object Fields
    Invoice__c {
        Id Id PK
        string Name "AutoNumber: INV-{0000}"
        Id CustomerId__c FK "Lookup to Account"
        currency InvoiceAmount__c
        string InvoiceCurrencyCode__c
        date InvoiceDate__c
        date InvoiceGlDate__c "Posting Date"
        string InvoiceStatus__c "Picklist"
        string InvoiceNumber__c
        datetime CreatedDate
        datetime LastModifiedDate
    }
    
    %% Receipts Object Fields
    Receipts__c {
        Id Id PK
        Id Invoice__c FK "Lookup to Invoice__c"
        currency Amount_Applied__c
        string Currency_Code__c
        date Receipt_Date__c
        string Invoice_Number__c
        datetime CreatedDate
        datetime LastModifiedDate
    }
    
    %% Lead Object (Standard)
    Lead {
        Id Id PK
        Id OwnerId FK "Lookup to User"
        Id ConvertedOpportunityId FK "Lookup to Opportunity"
        string FirstName
        string LastName
        string Company
        string Status
        datetime CreatedDate
        datetime LastModifiedDate
    }
    
    %% User Object (Standard)
    User {
        Id Id PK
        string Name
        string Email
        Id ManagerId FK "Lookup to User"
        string ProfileId FK
        string RoleId FK
        boolean IsActive
    }
    
    %% Account Object (Standard)
    Account {
        Id Id PK
        string Name
        string Type
        datetime CreatedDate
        datetime LastModifiedDate
    }
```

## Key Relationships Summary

### 1. Target → Achievement (Master-Detail)
- **Cardinality**: One-to-Many
- **Relationship**: `Achivement__c.Target__c` → `Target__c.Id`
- **Behavior**: 
  - Achievements roll up to Target via `Actual_Amount__c` field
  - Deletion of Target cascades to Achievements

### 2. Opportunity Split → Opportunity (Master-Detail)
- **Cardinality**: One-to-Many
- **Relationship**: `OpportunitySplit.OpportunityId` → `Opportunity.Id`
- **Behavior**:
  - Split percentages must sum to 100% (validation rule)
  - Splits are locked when Opportunity is Closed Won
  - Split owners are determined by Custom Label: `Achievement_Attribution_Roles` = "Sales Coordinator,Account Manager"

### 3. Achievement Attribution Logic
1. **Primary**: Opportunity Split records with eligible roles (from Custom Label)
2. **Secondary**: First Opportunity Team Member with eligible role (100% attribution)
3. **Fallback**: Opportunity Owner (100% attribution)

### 4. Realized Revenue Flow
```
Opportunity → Order → Invoice → Receipts → Achievement
```
- When Order is created, link to Achievement via `Order__c`
- When Payment (Receipts__c) is applied to Invoice, roll up to Achievement
- Multiple payments roll up to single Achievement record
- Currency conversion handled via `TargetCurrencyService`

### 5. Lead Conversion KPI
- Lead `OwnerId` → Target `User__c` match
- Increments `Lead_Conversion_Achieved_Count__c` on Target
- Separate from revenue achievements

## Field Details

### Target Object Key Fields
| Field API Name | Type | Required | Description |
|---------------|------|----------|-------------|
| `User__c` | Lookup(User) | Yes | Target assigned user |
| `Target_type__c` | Picklist | Yes | Direct Sales Order, Lead Conversion |
| `Cluster__c` | Picklist | Yes | Energy Cluster |
| `Company__c` | Picklist | Yes | Company within cluster |
| `Business_Unit__c` | Picklist | Yes | Business Unit |
| `Territory__c` | Picklist | No | Territory |
| `Period_type__c` | Picklist | Yes | Month/Quarter/Year |
| `Period_Start_Date__c` | Date | Yes | Period start date |
| `Target_Amount__c` | Currency | Yes | Target amount |
| `Actual_Amount__c` | Currency | Auto | Rollup sum of `Achivement__c.Converted_Amount__c` |
| `Lead_Conversion_Achieved_Count__c` | Number | No | Count of converted leads |
| `CurrencyIsoCode` | Currency | Yes | Default: AED |

### Achievement Object Key Fields
| Field API Name | Type | Required | Description |
|---------------|------|----------|-------------|
| `Target__c` | MasterDetail(Target) | Yes | Parent Target |
| `Opportunity__c` | Lookup(Opportunity) | Yes | Related Opportunity |
| `Order__c` | Lookup(Order) | No | Related Order (for realized revenue) |
| `User__c` | Lookup(User) | Yes | User attributed achievement |
| `Achieved_Date__c` | Date | Yes | Opportunity Close Date |
| `Native_Amount__c` | Currency | Yes | Original Opportunity Amount |
| `Converted_Amount__c` | Currency | Yes | Converted to Target Currency (rolls up to Target) |
| `Split_Percentage__c` | Number | No | Percentage from Opportunity Split |
| `Realized_Revenue_Original_Currency__c` | Currency | No | Total payments in original currency |
| `Realized_Revenue_AED__c` | Currency | No | Total payments in AED |
| `Total_Payments_Applied__c` | Currency | No | Rollup from Receipts (in Target currency) |
| `Invoice_Posting_Date__c` | Date | No | GL Date from Invoice |

### Opportunity Split Object Key Fields
| Field API Name | Type | Required | Description |
|---------------|------|----------|-------------|
| `OpportunityId` | MasterDetail(Opportunity) | Yes | Parent Opportunity |
| `SplitOwnerId` | Lookup(User) | Yes | User receiving split |
| `SplitPercentage` | Number | Yes | Must sum to 100% across all splits |
| `SplitAmount` | Currency | Yes | Calculated: Opportunity Amount × SplitPercentage |
| `SplitNote` | Text(255) | No | Optional notes |
| `SplitTypeId` | Lookup | No | Split type |

## Business Rules

### 1. Target Creation Rules
- Only Sales Directors and GMs can create Targets (via Permission Set)
- Periods must be non-overlapping for same User/BU/Year
- Target start date cannot be in the past (validation rule)
- Target Amount is editable only before period start
- Currency defaults to AED if not specified

### 2. Achievement Creation Rules
- Created automatically when Opportunity is Closed Won
- Target matching logic:
  - User__c matches Opportunity Split Owner or Team Member
  - Business_Unit__c matches Opportunity.Business_Unit__c
  - CloseDate falls within Target Period
- Achievement records are read-only for all users except System Admins

### 3. Opportunity Split Rules
- Total split percentage must equal 100% (hard validation)
- Splits are editable until Opportunity is Closed Won
- After Closed Won, splits are locked
- Split owners must have eligible roles (from Custom Label)

### 4. Realized Revenue Rules
- Multiple payments roll up to single Achievement record
- Currency conversion handled via `TargetCurrencyService`
- If invoice is cancelled/reversed, recalculate realized revenue
- Realized revenue never overwrites forecasted achievement

### 5. Lead Conversion Rules
- Only counts for Sales Coordinators (configurable via Target Type)
- Increments `Lead_Conversion_Achieved_Count__c` on Target
- Count-based rollup, independent of revenue targets

## Access Control & Sharing

### Target Sharing
- Users see only their own Targets (via Apex Sharing)
- Managers see roll-up views (User → Territory → BU → Company → Cluster)
- Access controlled via Permission Sets

### Achievement Sharing
- Read-only access for all users except System Admins
- Users see their own achievements
- Managers see team achievements via role hierarchy
- Follows role hierarchy: User → Proposal/Bid Manager → Sales Director → GM

## Integration Points

### 1. ERP Invoice Sync
- Invoices synced from ERP → Salesforce
- `RealizedRevenueService.linkOrderToAchievements()` links Orders to Achievements
- `RealizedRevenueService.updateAchievementsFromPayments()` updates realized revenue

### 2. Currency Conversion
- `TargetCurrencyService` handles all currency conversions
- Uses Costing Sheet JSON rates (if available) or Dated Conversion Rates
- Corporate currency (AED) used as intermediate conversion point

### 3. Opportunity Team Members
- Uses standard Opportunity Team Member object
- Roles checked against Custom Label: `Achievement_Attribution_Roles`

## Custom Labels

| Label API Name | Value | Purpose |
|---------------|-------|---------|
| `Achievement_Attribution_Roles` | "Sales Coordinator,Account Manager" | Configurable list of roles eligible for achievement attribution |

## Notes

- **Opportunity Share**: Standard Salesforce sharing mechanism used for Opportunity visibility
- **Opportunity Split**: Custom object with Master-Detail relationship to Opportunity
- **Split Attribution**: Based on Custom Label roles, not hardcoded
- **Realized Revenue**: Separate from Forecasted Achievement, both stored in Achievement object
- **Period Calculation**: Period end date calculated from `Period_Start_Date__c` and `Period_type__c`
- **Rollup Fields**: `Actual_Amount__c` on Target is auto-calculated via Apex trigger

---

**Document Version**: 1.0  
**Last Updated**: Based on Sprint 5 User Stories  
**Developer**: Abhijeet, Preethi, Ayush, Surdhika

