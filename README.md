# EPC Costing Sheet - Knowledge Transfer Documentation

## Table of Contents
1. [Overview](#overview)
2. [Architecture](#architecture)
3. [Component Structure](#component-structure)
4. [Apex Controller Documentation](#apex-controller-documentation)
5. [LWC Component Documentation](#lwc-component-documentation)
6. [Data Flow](#data-flow)
7. [Key Features](#key-features)
8. [Dependencies](#dependencies)

---

## Overview

The EPC Costing Sheet system is a comprehensive Salesforce solution for managing costing data across multiple tabs and categories. It provides functionality for:
- Viewing and editing costing data in tabular format
- Uploading Excel files to bulk update data
- Downloading Excel templates
- Calculating coefficients and formulas
- Managing tab line items and footer rows

**Primary Components:**
- **Apex Controller**: `EpcCostingSheetControllerAbhijeet.cls` - Backend logic and data operations
- **Main LWC**: `epcCostingSheetAbhijeet` - Main UI component for viewing/editing costing sheets
- **Uploader LWC**: `epcCostingUploader` - Component for Excel upload/download functionality

---

## Architecture

### Component Hierarchy
```
epcCostingSheetAbhijeet (Main Component)
├── epcCostingSheetAbhijeetHelper.js (Business Logic)
├── epcCostingSheetAbhijeetSelector.js (Data Access Layer)
├── epcCostingSheetAbhijeetConstant.js (Constants)
└── epcCostingUploader (Child Component)
    └── Uses SheetJS library for Excel operations
```

### Data Objects
- **Costing_Sheet__c**: Main record containing costing sheet information
- **Template_Tab__c**: Template tabs defining structure
- **Template_Column__c**: Column definitions for each tab
- **Template_Row__c**: Footer row definitions with formulas
- **Tab__c**: Actual tab records linked to Costing Sheet
- **Tab_Line_Item__c**: Line item data for each tab
- **Default_Value__c**: Default/template data rows
- **Option__c**: Option records grouping tabs

---

## Component Structure

### 1. Apex Controller: `EpcCostingSheetControllerAbhijeet.cls`

**Location**: `force-app/main/default/classes/EpcCostingSheetControllerAbhijeet.cls`

#### Key Methods

##### `getTableDataForTabs` (Lines 12-307)
- **Objective**: Bulk fetch table data for multiple tabs in a single call
- **Purpose**: Pre-compute footer rows without forcing users to visit each tab
- **Returns**: `List<TableDataWrapper>` - One wrapper per tabId
- **Key Logic**:
  - Lines 21-36: Fetch Template_Tab__c records and initialize wrappers
  - Lines 44-112: Fetch Costing_Sheet__c data and attach to each wrapper
  - Lines 114-157: Collect required Default_Value__c fields from columns
  - Lines 159-178: Fetch Template_Row__c records for footer calculations
  - Lines 180-191: Dynamic query for Default_Value__c records
  - Lines 193-294: Fetch Tab_Line_Item__c records by matching Tab__c with Template_Tab__c
- **Linked Components**: Called by `epcCostingSheetAbhijeetHelper.js` for bulk data prefetch

##### `getCostingSheetDetails` (Lines 310-343)
- **Objective**: Fetch Costing Sheet record and associated Template Tabs
- **Returns**: `CostingSheetWrapper` containing costing sheet and tabs
- **Key Logic**:
  - Lines 315-320: Query Costing_Sheet__c record
  - Lines 322-335: Query Template_Tab__c records if Template_Master_Id__c exists
- **Linked Components**: Called via `@wire` in `epcCostingSheetAbhijeet.js` (line 74)

##### `getTableData` (Lines 346-531)
- **Objective**: Fetch table data for a single tab
- **Returns**: `TableDataWrapper` with columns, rows, and footer data
- **Key Logic**:
  - Lines 351-361: Fetch Template_Tab__c to get tab metadata
  - Lines 364-372: Fetch Template_Column__c records
  - Lines 375-388: Fetch Template_Row__c for footer rows
  - Lines 391-453: Fetch Costing_Sheet__c data for formula references
  - Lines 455-477: Dynamic query for Default_Value__c records
  - Lines 479-523: Fetch Tab_Line_Item__c records if Tab__c exists
- **Linked Components**: Called via `@wire` in `epcCostingSheetAbhijeet.js` (line 118)

##### `saveOptionAndTabs` (Lines 571-692)
- **Objective**: Create Option__c and Tab__c records for the costing sheet
- **Parameters**: `costingSheetId`, `List<TabWrapper> tabs`
- **Returns**: `SaveResultWrapper` with created Option ID and Tab IDs
- **Key Logic**:
  - Lines 580-590: Verify Costing Sheet exists
  - Lines 592-611: Check for existing Option__c or create new one
  - Lines 613-625: Get existing tabs to avoid duplicates
  - Lines 627-658: Create Tab__c records for each tab
  - Lines 660-682: Build result with tab name to ID mapping
- **Linked Components**: Called by `epcCostingUploader.js` (line 348) when creating tabs

##### `saveTabLineItems` (Lines 857-1096)
- **Objective**: Save/update Tab_Line_Item__c records with field value mapping
- **Parameters**: `List<LineItemWrapper> lineItems`
- **Returns**: `SaveLineItemsResultWrapper` with created/updated line item IDs
- **Key Logic**:
  - Lines 866-894: Validate Tab__c records exist
  - Lines 907-916: Get Schema describe for Tab_Line_Item__c to cache field metadata
  - Lines 918-959: Separate line items into insert/update lists, match by ID or Sr_No__c
  - Lines 961-1065: Map field values using `convertFieldValueByType` method
  - Lines 1067-1086: Perform DML operations and return results
- **Linked Components**: Called by `epcCostingUploader.js` (line 1143) after processing Excel file

##### `convertFieldValueByType` (Lines 719-846)
- **Objective**: Convert field values to appropriate types based on Schema definition
- **Parameters**: `fieldName`, `fieldValue`, `fieldDescribe`
- **Returns**: Converted value in appropriate type
- **Key Logic**:
  - Lines 731-740: Handle STRING, TEXTAREA, EMAIL, PHONE, URL, PICKLIST types
  - Lines 749-760: Handle INTEGER type
  - Lines 761-777: Handle DOUBLE, CURRENCY, PERCENT types
  - Lines 778-791: Handle BOOLEAN type
  - Lines 792-806: Handle DATE type
  - Lines 807-821: Handle DATETIME type
  - Lines 822-833: Handle ID, REFERENCE types
- **Linked Components**: Used by `saveTabLineItems` (line 1042) and `updateTabsFromTemplateRows` (line 1258)

##### `updateTabsFromTemplateRows` (Lines 1121-1289)
- **Objective**: Update Tab__c records with footer row values from Template_Row__c
- **Parameters**: `List<TabUpdateWrapper> tabUpdates`
- **Returns**: `UpdateTabsResultWrapper` with updated count
- **Key Logic**:
  - Lines 1129-1143: Build maps for efficient lookup
  - Lines 1152-1168: Fetch Template_Row__c where Object_Api_Name__c = 'Tab__c'
  - Lines 1170-1178: Get Schema describe for Tab__c
  - Lines 1196-1268: Map footer values to Tab__c fields and update
- **Linked Components**: Called by `epcCostingSheetAbhijeetHelper.js` during save operation

##### `updateCostPerDirectManHourCoEfficient` (Lines 1320-1518)
- **Objective**: Calculate and update Cost_Per_Direct_Man_Hour_Co_Efficient__c on Costing_Sheet__c
- **Calculation**: DIRECT_MANHOUR_COST_ROW_TOTAL_COST_DIRECT_MANPOWER / (SUM(Number_Of_Workers__c * Construction_Duration__c) * 304)
- **Key Logic**:
  - Lines 1327-1342: Find "Direct Manhour Cost" tab
  - Lines 1344-1360: Calculate sum of (Number_Of_Workers__c * Construction_Duration__c)
  - Lines 1365-1480: Get DIRECT_MANHOUR_COST_ROW_TOTAL_COST_DIRECT_MANPOWER from Tab__c
  - Lines 1489-1505: Calculate and update Costing_Sheet__c
- **Linked Components**: Called by `epcCostingSheetAbhijeetHelper.js` after saving footer values

##### `updateMaterialCoefficient` (Lines 1545-1632)
- **Objective**: Calculate and update Material_Co_Efficient__c on Costing_Sheet__c
- **Calculation**: SUM(Total_Cost__c from all Material tabs) / SUM(Total_Price_Material__c from all Material tabs)
- **Key Logic**:
  - Lines 1555-1561: Find all Material type tabs
  - Lines 1573-1588: Sum Total_Cost__c and Total_Price_Material__c
  - Lines 1594-1605: Calculate Material Coefficient
  - Lines 1607-1614: Update Costing_Sheet__c
- **Linked Components**: Called by `epcCostingSheetAbhijeetHelper.js` after saving footer values

##### `updateManpowerCoefficient` (Lines 1664-1799)
- **Objective**: Calculate and update Manpower_Co_Efficient__c on Costing_Sheet__c
- **Calculation**: SUM(non-Material costs) / SUM(Material Price_MH_Dhs__c)
- **Key Logic**:
  - Lines 1673-1692: Build dynamic query for non-Material tabs with optional fields
  - Lines 1695-1702: Find all Material tabs to get Price_MH_Dhs__c
  - Lines 1714-1743: Sum costs from non-Material tabs
  - Lines 1745-1755: Sum Price_MH_Dhs__c from Material tabs
  - Lines 1761-1772: Calculate Manpower Coefficient
  - Lines 1774-1781: Update Costing_Sheet__c
- **Linked Components**: Called by `epcCostingSheetAbhijeetHelper.js` after saving footer values

##### `updateCoefficientDependentFields` (Lines 1824-2117)
- **Objective**: Recalculate Tab_Line_Item__c fields that depend on coefficients
- **Key Logic**:
  - Lines 1834-1850: Fetch Costing Sheet with coefficient values
  - Lines 1857-1888: Query all Formula columns that might reference coefficients
  - Lines 1890-1902: Filter to columns that actually reference coefficients
  - Lines 1914-1932: Filter to columns mapping to Tab_Line_Item__c
  - Lines 1954-1994: Query Tab_Line_Item__c records with all required fields
  - Lines 2048-2095: Recalculate and update coefficient-dependent fields
- **Linked Components**: Called by `epcCostingSheetAbhijeetHelper.js` after updating coefficients

##### `evaluateCoefficientFormula` (Lines 2130-2218)
- **Objective**: Evaluate formula expressions for coefficient-dependent fields
- **Parameters**: `formula`, `lineItem`, `costingSheet`, `fieldApiNameMap`, `columnCodeMap`
- **Returns**: Calculated Decimal value
- **Key Logic**:
  - Lines 2143-2152: Replace {CS.FIELD_NAME} references with Costing Sheet values
  - Lines 2154-2163: Handle CS.FIELD_NAME without braces
  - Lines 2165-2195: Replace {COLUMN_CODE} references with line item field values
  - Lines 2197-2217: Evaluate mathematical expression using `evaluateSimpleMathExpression`
- **Linked Components**: Used by `updateCoefficientDependentFields` (line 2067)

##### `evaluateSimpleMathExpression` (Lines 2224-2245)
- **Objective**: Evaluate simple mathematical expressions (+, -, *, /, parentheses)
- **Returns**: Calculated Decimal value
- **Key Logic**:
  - Lines 2230-2236: Remove whitespace and validate expression
  - Line 2240: Use recursive descent parser
- **Linked Components**: Used by `evaluateCoefficientFormula` (line 2206)

##### `getTemplateColumnsForDownload` (Lines 2359-2422)
- **Objective**: Fetch template columns grouped by tabs for Excel template download
- **Returns**: `List<TemplateTabWithColumnsWrapper>`
- **Key Logic**:
  - Lines 2366-2371: Fetch Template_Tab__c records
  - Lines 2383-2391: Fetch Template_Column__c records
  - Lines 2393-2414: Group columns by tab and build wrapper list
- **Linked Components**: Called by `epcCostingUploader.js` (line 103) for template download

##### `getTabsByName` (Lines 2445-2470)
- **Objective**: Get Tab__c records by tab name for a given Costing Sheet
- **Returns**: `Map<String, Id>` - Map of Tab_Name__c to Tab__c Id
- **Key Logic**:
  - Lines 2451-2455: Query Tab__c records for the costing sheet
  - Lines 2457-2462: Build map of tab name to ID
- **Linked Components**: Called by `epcCostingUploader.js` (line 118) to match Excel sheets to tabs

##### `saveTabLineItemsAsync` (Lines 2474-2520)
- **Objective**: Asynchronous wrapper for saveTabLineItems to handle large datasets
- **Key Logic**:
  - Lines 2489-2500: For datasets >100 items, enqueue Queueable job
  - Lines 2501-2510: For smaller datasets, process synchronously
- **Linked Components**: Can be used as alternative to `saveTabLineItems` for bulk operations

##### `getAsyncJobStatus` (Lines 2526-2572)
- **Objective**: Get status of an async job
- **Returns**: `AsyncJobStatusResult` with job status information
- **Key Logic**:
  - Lines 2537-2550: Query AsyncApexJob record
  - Lines 2552-2563: Build status result
- **Linked Components**: Used to check status of async operations

---

### 2. Main LWC Component: `epcCostingSheetAbhijeet`

**Location**: `force-app/main/default/lwc/epcCostingSheetAbhijeet/`

#### File: `epcCostingSheetAbhijeet.js` (Lines 1-317)

**Architecture**: Facade pattern - delegates all business logic to Helper class

##### Key Sections:

**Lines 1-9**: Imports
- **Objective**: Import Lightning Web Components modules and Apex methods
- **Key Imports**:
  - `NavigationMixin` for navigation
  - `refreshApex` for refreshing wired data
  - `Helper` class for business logic
  - Apex methods from `EpcCostingSheetControllerAbhijeet`

**Lines 16-59**: Component Class and Properties
- **Line 16**: Extends `NavigationMixin` for navigation capabilities
- **Lines 17-54**: Tracked properties for component state
  - `costingSheet`: Costing sheet data
  - `tabs`: List of tabs
  - `activeTabId`: Currently active tab
  - `tableData`, `tableColumns`, `tableRows`: Table display data
  - `tabDataCache`: Cache for preserving user input when switching tabs
- **Line 58**: Initialize Helper instance in constructor

**Lines 65-68**: Page Reference Handler
- **Objective**: Handle URL parameters (e.g., c__recordId) for App Page usage
- **Linked Components**: Delegates to `Helper.handlePageReference`

**Lines 74-77**: Wired Costing Sheet Details
- **Objective**: Fetch Costing Sheet data when component loads
- **Linked Components**: 
  - Calls Apex: `EpcCostingSheetControllerAbhijeet.getCostingSheetDetails` (line 310)
  - Delegates processing to `Helper.handleWiredCostingSheet`

**Lines 85-88**: Type Tab Switch Handler
- **Objective**: Handle switching between top-level tab types (Material, Manpower, etc.)
- **Linked Components**: Delegates to `Helper.handleSwitchTypeTab`

**Lines 93-102**: Navigate to Record
- **Objective**: Navigate to Costing Sheet record page
- **Uses**: `NavigationMixin.Navigate`

**Lines 108-111**: Tab Switch Handler
- **Objective**: Handle switching between subtabs
- **Linked Components**: Delegates to `Helper.handleSwitchTab`

**Lines 118-122**: Wired Table Data
- **Objective**: Fetch table data when activeTabId changes
- **Linked Components**:
  - Calls Apex: `EpcCostingSheetControllerAbhijeet.getTableData` (line 346)
  - Delegates processing to `Helper.handleWiredTableData`

**Lines 129-171**: Getter Methods (UI State)
- **Lines 129-131**: `tabTypes` - Returns distinct tab types with styling
- **Lines 136-138**: `subTabsForActiveType` - Returns subtabs for active type
- **Lines 143-145**: `hasTableData` - Checks if table data is available
- **Lines 151-157**: `isSummaryTab` - Checks if active tab is Summary type
- **Lines 162-164**: `showDeleteColumn` - Determines if delete column should be shown
- **Lines 169-171**: `showAddRowButton` - Determines if Add Row button should be shown
- **Lines 177-186**: `showUploader` - Determines if uploader component should be shown
- **Linked Components**: All delegate to Helper class methods

**Lines 199-204**: Cell Value Change Handler
- **Objective**: Handle manual input field changes
- **Linked Components**: Delegates to `Helper.handleCellValueChange`

**Lines 210-214**: Footer Value Change Handler
- **Objective**: Handle manual footer value changes
- **Linked Components**: Delegates to `Helper.handleFooterValueChange`

**Lines 220-222**: Add Row Handler
- **Objective**: Handle adding new rows
- **Linked Components**: Delegates to `Helper.handleAddRow`

**Lines 228-231**: Delete Row Handler
- **Objective**: Handle deleting rows
- **Linked Components**: Delegates to `Helper.handleDeleteRow`

**Lines 237-239**: Save Handler
- **Objective**: Handle Save button click
- **Linked Components**: Delegates to `Helper.handleSave`

**Lines 245-249**: Update UI by Sno Handler
- **Objective**: Handle update UI event from uploader component
- **Purpose**: Matches rows by Sno and updates UI values before saving
- **Linked Components**: Delegates to `Helper.updateUIRowsBySno`

**Lines 255-316**: Upload Complete Handler
- **Objective**: Handle upload complete event from uploader component
- **Key Logic**:
  - Lines 259-266: Clear cache for current tab
  - Lines 272-281: Refresh wired data using `refreshApex`
  - Lines 285-289: Recalculate formulas after refresh
  - Lines 292-299: Show success toast
- **Linked Components**: 
  - Uses `refreshApex` to refresh `wiredTableDataResult`
  - Calls `Helper.recalculateAllFormulas`

#### File: `epcCostingSheetAbhijeet.html` (Lines 1-259)

**Architecture**: Template markup for component UI

##### Key Sections:

**Lines 3-10**: Save Loading Overlay
- **Objective**: Show full-screen loading overlay during save operation
- **Condition**: `isSaving` property

**Lines 12-40**: Header Section
- **Lines 14-26**: Header left with icon and title
- **Lines 28-39**: Header right with status and Save button
- **Line 23**: Link to navigate to Costing Sheet record

**Lines 42-56**: Top-level Type Tabs
- **Objective**: Display tab types (Material, Manpower, etc.)
- **Iterates**: Over `tabTypes` getter
- **Event**: `handleSwitchTypeTab` on click

**Lines 62-72**: Sub Tabs (Chrome-style)
- **Objective**: Display subtabs for selected type
- **Iterates**: Over `subTabsForActiveType` getter
- **Event**: `handleSwitchTab` on click

**Lines 84-93**: Uploader Component
- **Objective**: Display uploader component for Excel operations
- **Condition**: `showUploader` getter
- **Props**: `record-id`, `active-tab-id`, `table-columns`, `table-data`
- **Events**: `onuploadcomplete`, `onupdateuibysno`

**Lines 94-209**: Table Display
- **Lines 96-106**: Table header with column names
- **Lines 108-207**: Table body with rows and cells
  - **Lines 114-148**: Manual input fields (picklist, number, text)
  - **Lines 149-154**: Display values (Reference, Formula, Auto Increment)
  - **Lines 158-203**: Delete column with delete buttons
- **Events**: `handleCellValueChange` for cell edits

**Lines 210-220**: Add Row Button
- **Objective**: Display Add Row button
- **Condition**: `showAddRowButton` getter
- **Event**: `handleAddRow` on click

**Lines 221-244**: Footer Summary
- **Objective**: Display footer rows with calculated/formula values
- **Condition**: `hasFooterRows` getter
- **Lines 227-237**: Manual input fields for footer
- **Lines 238-240**: Display values for calculated footer rows

#### File: `epcCostingSheetAbhijeetHelper.js` (Lines 1-5884)

**Architecture**: Business logic layer - all complex logic resides here

##### Key Sections:

**Lines 15-24**: Constructor
- **Objective**: Initialize helper with component reference
- **Initializes**: `manualFooterValues` object for tracking footer edits

**Lines 29-52**: Loading/Saving State Management
- **Lines 29-31**: `startTabLoading()` - Set loading flag
- **Lines 36-38**: `finishTabLoading()` - Clear loading flag
- **Lines 43-45**: `startSaving()` - Set saving flag
- **Lines 50-52**: `finishSaving()` - Clear saving flag

**Lines 62-91**: Process Costing Sheet Data
- **Objective**: Process raw Costing Sheet data from Apex
- **Key Logic**:
  - Lines 63-70: Map costing sheet properties
  - Lines 74-82: Map tabs with styling classes
  - Lines 84-87: Initialize active type and select first tab

**Lines 97-107**: Set Active Tab
- **Objective**: Set active tab and update UI state
- **Updates**: Tab styling classes based on active state

**Lines 113-128**: Set Active Type
- **Objective**: Set active tab type and select first tab of that type
- **Updates**: `activeType` and selects first tab

**Lines 134-140**: Process Table Data (Initial)
- **Objective**: Process table data and calculate formula values
- **Returns**: Early if no data

**Lines 145-169**: Process Columns
- **Objective**: Map Template_Column__c to UI column objects
- **Key Properties**:
  - `isManual`: Column_Type__c === "Manual" OR Is_Editable__c === true
  - `isNumber`: Data_Type__c is Number/Currency/Percent

**Lines 171-200**: Merge Default_Value__c and Tab_Line_Item__c
- **Objective**: Merge template data with uploaded data
- **Key Logic**:
  - Lines 180-182: Start with Default_Value__c records
  - Lines 184-196: Find Auto Increment column for Sr_No__c matching
  - Lines 198-200: Merge Tab_Line_Item__c records by matching Sr_No__c

**Lines 200-5884**: Additional Helper Methods
- **Note**: File is 5884 lines - contains extensive business logic for:
  - Formula calculations
  - Row management (add/delete)
  - Footer row calculations
  - Save operations
  - Coefficient updates
  - UI state management

#### File: `epcCostingSheetAbhijeetSelector.js` (Lines 1-28)

**Architecture**: Data access layer - all Apex calls go through here

**Lines 15-17**: `getCostingSheetDetails`
- **Objective**: Imperative call to fetch costing sheet details
- **Linked Components**: Wraps Apex method `EpcCostingSheetControllerAbhijeet.getCostingSheetDetails`

**Lines 25-27**: `getTableData`
- **Objective**: Imperative call to fetch table data
- **Linked Components**: Wraps Apex method `EpcCostingSheetControllerAbhijeet.getTableData`

#### File: `epcCostingSheetAbhijeetConstant.js` (Lines 1-20)

**Architecture**: Constants definition

**Lines 6-20**: CONSTANTS Object
- **DEFAULT_OPTION_NAME**: '1'
- **TAB_CLASS**: CSS class for inactive tabs
- **TAB_ACTIVE_CLASS**: CSS class for active tabs
- **TAB_TYPES**: Object with tab type constants
- **ERROR_MESSAGES**: Error message constants

---

### 3. Uploader LWC Component: `epcCostingUploader`

**Location**: `force-app/main/default/lwc/epcCostingUploader/`

#### File: `epcCostingUploader.js` (Lines 1-1175)

**Architecture**: Standalone component for Excel operations, can be embedded in main component

##### Key Sections:

**Lines 1-9**: Imports
- **Objective**: Import required modules and Apex methods
- **Key Imports**:
  - `loadScript` for loading SheetJS library
  - Apex methods for template download and data upload

**Lines 15-30**: Component Properties
- **@api recordId**: Costing Sheet record ID
- **@api activeTabId**: Current active tab ID
- **@api tableColumns**: Table columns from parent
- **@api tableData**: Table data from parent
- **@track templateMasterId**: Template Master ID
- **@track isSheetJSLoaded**: Flag for SheetJS library status

**Lines 31-45**: Connected Callback
- **Objective**: Initialize component on load
- **Key Logic**:
  - Line 32: Load SheetJS library
  - Lines 35-44: Fetch costing sheet details if in standalone mode

**Lines 50-62**: Load SheetJS Library
- **Objective**: Load SheetJS library from static resource
- **Resource**: `@salesforce/resourceUrl/sheetjs`
- **Sets**: `isSheetJSLoaded` flag when complete

**Lines 67-92**: Fetch Costing Sheet Details
- **Objective**: Get Template Master ID from Costing Sheet
- **Linked Components**: 
  - Calls Apex: `EpcCostingSheetControllerAbhijeet.getCostingSheetDetails` (line 310)
  - Calls `fetchTemplateColumns` after getting template master ID

**Lines 97-107**: Fetch Template Columns
- **Objective**: Get template columns for download
- **Linked Components**: 
  - Calls Apex: `EpcCostingSheetControllerAbhijeet.getTemplateColumnsForDownload` (line 2359)

**Lines 112-129**: Fetch Tabs by Name
- **Objective**: Get Tab__c records mapped by name
- **Linked Components**: 
  - Calls Apex: `EpcCostingSheetControllerAbhijeet.getTabsByName` (line 2445)
  - Used for matching Excel sheet names to Tab__c records

**Lines 135-182**: Handle Download Template
- **Objective**: Create and download Excel template file
- **Key Logic**:
  - Lines 149-152: Wait for SheetJS to be available
  - Line 155: Create new workbook
  - Lines 158-169: Create sheet for each tab with column headers
  - Line 172: Generate Excel file as base64
  - Line 173: Trigger download

**Lines 189-221**: Create Sheet Data for Tab
- **Objective**: Build 2D array for Excel sheet
- **Key Logic**:
  - Lines 193-198: Header row with column names
  - Lines 200-211: Reference row with column codes/field names
  - Lines 214-218: Empty data rows for user input

**Lines 314-381**: Ensure Option and Tab Exist
- **Objective**: Create Option__c and Tab__c if they don't exist
- **Linked Components**: 
  - Calls Apex: `EpcCostingSheetControllerAbhijeet.saveOptionAndTabs` (line 571)
  - Used before uploading data to ensure Tab__c exists

**Lines 387-675**: Handle File Change
- **Objective**: Process uploaded Excel file
- **Key Logic**:
  - Lines 393-401: Validate file type (.xlsx, .xls, .csv)
  - Lines 410-420: Ensure Option__c and Tab__c exist (embedded mode)
  - Lines 422-424: Read file as ArrayBuffer
  - Lines 427-621: Process each sheet:
    - Lines 433-458: Find Tab__c for sheet name
    - Lines 462-491: Get template columns for mapping
    - Lines 494-506: Skip reference row if present
    - Lines 508-544: Build column mapping and find Sno column
    - Lines 547-620: Process data rows and build line items
  - Lines 624-632: Dispatch event to update UI (embedded mode)
  - Lines 635-648: Save line items and dispatch upload complete event

**Lines 677-700**: Monitor Loader Visibility
- **Objective**: Monitor keepLoaderVisible property
- **Purpose**: Hide loader when parent processing is complete

**Lines 707-714**: Read File as ArrayBuffer
- **Objective**: Read file using FileReader API
- **Returns**: Promise resolving to ArrayBuffer

**Lines 989-1084**: Build Column Mapping
- **Objective**: Map Excel column headers to Field API Names
- **Key Logic**:
  - Lines 993-1015: Build lookup maps (name, code, fieldApiName)
  - Lines 1018-1044: Match Excel headers to template columns
  - Lines 1046-1081: Validate and build mapping object
- **Returns**: Object mapping column index to field metadata

**Lines 1092-1135**: Convert Cell Value
- **Objective**: Convert Excel cell values based on data type
- **Handles**: String, Number, Boolean, Date conversions

**Lines 1141-1150**: Save Line Items
- **Objective**: Save line items to Salesforce
- **Linked Components**: 
  - Calls Apex: `EpcCostingSheetControllerAbhijeet.saveTabLineItems` (line 857)

**Lines 1156-1159**: Is Embedded Mode Getter
- **Objective**: Check if component is embedded in parent
- **Returns**: true if tableData or activeTabId is provided

**Lines 1165-1167**: Should Show Loader Getter
- **Objective**: Determine if loader should be visible
- **Returns**: true if isUploading OR keepLoaderVisible

---

## Data Flow

### 1. Initial Load Flow
```
User Opens Component
  ↓
epcCostingSheetAbhijeet.js (line 74)
  ↓
@wire(getCostingSheetDetails)
  ↓
EpcCostingSheetControllerAbhijeet.getCostingSheetDetails (line 310)
  ↓
Returns CostingSheetWrapper
  ↓
Helper.handleWiredCostingSheet (processes data)
  ↓
UI Updates with Tabs
```

### 2. Tab Switch Flow
```
User Clicks Tab
  ↓
epcCostingSheetAbhijeet.js (line 108)
  ↓
Helper.handleSwitchTab
  ↓
Sets activeTabId
  ↓
@wire(getTableData) (line 118)
  ↓
EpcCostingSheetControllerAbhijeet.getTableData (line 346)
  ↓
Returns TableDataWrapper
  ↓
Helper.handleWiredTableData
  ↓
Helper.processTableData
  ↓
UI Updates with Table
```

### 3. Save Flow
```
User Clicks Save
  ↓
epcCostingSheetAbhijeet.js (line 237)
  ↓
Helper.handleSave
  ↓
Collects changed data
  ↓
EpcCostingSheetControllerAbhijeet.saveTabLineItems (line 857)
  ↓
EpcCostingSheetControllerAbhijeet.updateTabsFromTemplateRows (line 1121)
  ↓
EpcCostingSheetControllerAbhijeet.updateCostPerDirectManHourCoEfficient (line 1320)
  ↓
EpcCostingSheetControllerAbhijeet.updateMaterialCoefficient (line 1545)
  ↓
EpcCostingSheetControllerAbhijeet.updateManpowerCoefficient (line 1664)
  ↓
EpcCostingSheetControllerAbhijeet.updateCoefficientDependentFields (line 1824)
  ↓
UI Updates with Success Message
```

### 4. Upload Flow
```
User Uploads Excel File
  ↓
epcCostingUploader.js (line 387)
  ↓
Read File as ArrayBuffer
  ↓
Parse Excel with SheetJS
  ↓
Map Columns to Fields
  ↓
Build Line Items
  ↓
Dispatch updateuibysno Event (embedded mode)
  ↓
EpcCostingSheetControllerAbhijeet.saveTabLineItems (line 857)
  ↓
Dispatch uploadcomplete Event
  ↓
epcCostingSheetAbhijeet.js (line 255)
  ↓
Refresh Table Data
  ↓
Recalculate Formulas
```

---

## Key Features

### 1. Tab Management
- **Type-based Tabs**: Tabs grouped by type (Material, Manpower, etc.)
- **Sub-tabs**: Multiple tabs within each type
- **Tab Switching**: Preserves user input when switching tabs using cache

### 2. Data Management
- **Default Values**: Template data from Default_Value__c
- **Line Items**: User-uploaded data in Tab_Line_Item__c
- **Merging**: Automatic merging of template and uploaded data by Sr_No__c

### 3. Formula Calculations
- **Column Formulas**: Calculated columns based on other columns
- **Footer Formulas**: Calculated footer rows based on Template_Row__c
- **Coefficient Formulas**: Fields that depend on Material/Manpower coefficients

### 4. Excel Operations
- **Template Download**: Download Excel templates with column structure
- **Data Upload**: Upload Excel files to bulk update data
- **Column Mapping**: Automatic mapping of Excel columns to Salesforce fields

### 5. Coefficient Calculations
- **Cost Per Direct Man Hour**: Calculated from Direct Manhour Cost tab
- **Material Coefficient**: Calculated from all Material tabs
- **Manpower Coefficient**: Calculated from non-Material and Material tabs

---

## Dependencies

### Apex Classes
- `EpcCostingSheetControllerAbhijeet` - Main controller class

### Static Resources
- `sheetjs` - SheetJS library for Excel operations

### Salesforce Objects
- `Costing_Sheet__c`
- `Template_Tab__c`
- `Template_Column__c`
- `Template_Row__c`
- `Tab__c`
- `Tab_Line_Item__c`
- `Default_Value__c`
- `Option__c`

### Lightning Web Components
- `lightning-input`
- `lightning-combobox`
- `lightning-button`
- `lightning-button-icon`
- `lightning-spinner`
- `lightning-icon`

---

## Notes for Knowledge Transfer

### Important Patterns

1. **Facade Pattern**: Main component (`epcCostingSheetAbhijeet.js`) acts as facade, delegating all logic to Helper
2. **Cache Pattern**: Tab data cached to preserve user input when switching tabs
3. **Wire Pattern**: Uses `@wire` for reactive data fetching
4. **Event-Driven**: Components communicate via custom events

### Common Issues and Solutions

1. **Formula Recalculation**: Formulas recalculated after data refresh (line 286-289 in main component)
2. **Tab Matching**: Tabs matched by Name and Type (not just ID)
3. **Field Type Conversion**: All field values converted using Schema describe (line 719-846 in Apex)
4. **Coefficient Dependencies**: Fields that depend on coefficients recalculated after coefficient updates

### Testing Considerations

1. Test with multiple tabs and types
2. Test formula calculations with various data combinations
3. Test Excel upload with different column mappings
4. Test coefficient calculations with edge cases (zero values, null values)
5. Test save operations with large datasets

---

## Contact and Support

For questions or issues related to this codebase, please refer to:
- Apex Class: `EpcCostingSheetControllerAbhijeet.cls`
- Main LWC: `epcCostingSheetAbhijeet`
- Uploader LWC: `epcCostingUploader`

---

**Document Version**: 1.0  
**Last Updated**: 2024  
**Author**: Knowledge Transfer Documentation
