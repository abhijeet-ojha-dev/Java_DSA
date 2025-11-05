# Targets and Achievements - Visual ER Diagram

Copy and paste this Mermaid diagram into any Mermaid-compatible viewer (e.g., GitHub, Mermaid Live Editor, VS Code with Mermaid extension).

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
        string Target_type__c "Picklist"
        string Cluster__c "Picklist"
        string Company__c "Picklist"
        string Business_Unit__c "Picklist"
        string Territory__c "Picklist"
        string Period_type__c "Picklist"
        date Period_Start_Date__c
        currency Target_Amount__c
        currency Actual_Amount__c "Rollup"
        number Lead_Conversion_Achieved_Count__c
        string CurrencyIsoCode "Default AED"
        Id OwnerId "Creator"
    }
    
    %% Achievement Object Fields
    Achivement__c {
        Id Id PK
        Id Target__c FK "Master-Detail"
        Id Opportunity__c FK "Lookup"
        Id Order__c FK "Lookup"
        Id User__c FK "Lookup"
        date Achieved_Date__c
        currency Native_Amount__c
        currency Converted_Amount__c "Rolls up"
        number Split_Percentage__c
        currency Realized_Revenue_Original_Currency__c
        currency Realized_Revenue_AED__c
        currency Total_Payments_Applied__c "Rollup"
        date Invoice_Posting_Date__c
    }
    
    %% Opportunity Split Object Fields
    OpportunitySplit {
        Id Id PK
        Id OpportunityId FK "Master-Detail"
        Id SplitOwnerId FK "Lookup User"
        Id SplitTypeId FK "Lookup"
        number SplitPercentage "Sum=100%"
        currency SplitAmount "Calculated"
        string SplitNote
    }
    
    %% Opportunity Object
    Opportunity {
        Id Id PK
        Id OwnerId FK "Lookup User"
        Id AccountId FK "Lookup Account"
        string Name
        string StageName
        date CloseDate
        currency Amount
        string CurrencyIsoCode
        string Business_Unit__c "Custom"
    }
    
    %% Order Object
    Order {
        Id Id PK
        Id OpportunityId FK "Lookup"
        Id AccountId FK "Lookup"
        string OrderNumber
        string Status
    }
    
    %% Invoice Object Fields
    Invoice__c {
        Id Id PK
        string Name "AutoNumber"
        Id CustomerId__c FK "Lookup Account"
        currency InvoiceAmount__c
        string InvoiceCurrencyCode__c
        date InvoiceDate__c
        date InvoiceGlDate__c "Posting Date"
        string InvoiceStatus__c "Picklist"
    }
    
    %% Receipts Object Fields
    Receipts__c {
        Id Id PK
        Id Invoice__c FK "Lookup"
        currency Amount_Applied__c
        string Currency_Code__c
        date Receipt_Date__c
    }
    
    %% Lead Object
    Lead {
        Id Id PK
        Id OwnerId FK "Lookup User"
        Id ConvertedOpportunityId FK "Lookup"
        string FirstName
        string LastName
        string Company
        string Status
    }
    
    %% User Object
    User {
        Id Id PK
        string Name
        string Email
        Id ManagerId FK "Lookup User"
        string ProfileId FK
        string RoleId FK
        boolean IsActive
    }
    
    %% Account Object
    Account {
        Id Id PK
        string Name
        string Type
    }
```

## Quick Reference: Key Relationships

1. **Target → Achievement**: Master-Detail (1:Many)
   - Achievements roll up `Converted_Amount__c` to `Target.Actual_Amount__c`

2. **Opportunity → OpportunitySplit**: Master-Detail (1:Many)
   - Split percentages must sum to 100%
   - Split owners filtered by Custom Label roles

3. **Opportunity → Order → Invoice → Receipts → Achievement**
   - Realized revenue flow
   - Multiple payments roll up to single Achievement

4. **Lead → Opportunity → Achievement**
   - Lead conversion KPI tracking
   - Attributes to Lead Owner's Target

## Custom Label Usage

- **Achievement_Attribution_Roles**: "Sales Coordinator,Account Manager"
  - Used to determine eligible users for achievement attribution
  - Applied to Opportunity Split and Opportunity Team Member filtering

