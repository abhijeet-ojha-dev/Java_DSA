# GhoBash Salesforce Implementation - Test Cases Documentation

## Table of Contents
- [Lead Management Test Cases](#lead-management-test-cases)
- [Opportunity Management Test Cases](#opportunity-management-test-cases)
- [Account Management Test Cases](#account-management-test-cases)
- [Contact Management Test Cases](#contact-management-test-cases)

---

## Lead Management Test Cases

| BRN | Module | Sub-Module | Company | CR | USR No | Title | User | Test Scenario | Test Steps | Expected Result |
|-----|--------|------------|---------|----|----|-------|------|---------------|------------|-----------------|
| BRN#003 | Lead Management | Lead Creation_Manual | EEIC & GCGE- All BUs | No | USR#005 | Manual Lead Creation | Sales Coordinator | **TC001**: Verify mandatory field validation on lead creation | 1. Login as Sales Coordinator<br>2. Navigate to Leads tab<br>3. Click New Lead button<br>4. Leave mandatory fields empty<br>5. Try to save | System should prevent saving and display error message for missing required fields: Company Name, Lead Source, Lead Type, Lead Sub-type, Primary Contact Name, Primary Contact Phone, Primary Contact Email, Scope of Work, Bid Bond Required, Business Unit, Existing Customer, Customer Classification, Bid Submission Date |
| BRN#003 | Lead Management | Lead Creation_Manual | EEIC & GCGE- All BUs | No | USR#005 | Manual Lead Creation | Sales Coordinator | **TC002**: Successfully create a lead with all mandatory fields | 1. Login as Sales Coordinator<br>2. Navigate to Leads tab<br>3. Click New Lead button<br>4. Fill all mandatory fields<br>5. Save the record | Lead should be created successfully with Status = "New" and all field values saved correctly |
| BRN#003 | Lead Management | Lead Creation_Manual | EEIC & GCGE- All BUs | No | USR#005 | Manual Lead Creation | Sales Coordinator | **TC003**: Validate dynamic form behavior for Existing Customer = Yes | 1. Create new lead<br>2. Select Business Unit<br>3. Set Existing Customer = "Yes"<br>4. Observe Account lookup field | Account lookup field should become visible and mandatory |
| BRN#003 | Lead Management | Lead Creation_Manual | EEIC & GCGE- All BUs | No | USR#006 | Lead Source Picklist | Sales Coordinator | **TC004**: Verify Lead Source is restricted picklist | 1. Create new lead<br>2. Click on Lead Source field<br>3. Attempt to type custom value | System should only allow selection from predefined values (Walk-in, Phone Call, Webform, Campaign, Referral, Trade Event). Manual entry should not be possible |
| BRN#003 | Lead Management | Lead Creation_Manual | EEIC & GCGE- All BUs | No | USR#007 | Document Attachment | Sales Coordinator | **TC005**: Upload documents to lead record | 1. Create a lead<br>2. Navigate to Upload Documents component<br>3. Select document type (Scope of Work)<br>4. Upload PDF file<br>5. Verify document appears | Document should be uploaded successfully and visible in the documents section with correct document type tag |
| BRN#003 | Lead Management | Lead Creation_Manual | EEIC & GCGE- All BUs | No | USR#008 | Vertical Head Auto-population | Sales Coordinator | **TC006**: Verify Vertical Head auto-populates based on Business Unit | 1. Create new lead<br>2. Select Business Unit = "Automation"<br>3. Save lead | Vertical Head field should auto-populate with the mapped user for Automation BU based on Business Unit Mapping Rule |
| BRN#003 | Lead Management | Lead Creation_Manual | EEIC & GCGE- All BUs | No | USR#008 | Vertical Head Auto-population | Sales Coordinator | **TC007**: Verify Vertical Head is read-only | 1. Create a lead with BU selected<br>2. Wait for Vertical Head to auto-populate<br>3. Try to manually edit Vertical Head field | Field should be read-only and not editable by user |
| BRN#003 | Lead Management | Lead Creation_Manual | EEIC & GCGE- All BUs | No | USR#009 | Account Lookup for Existing Customer | Sales Coordinator | **TC008**: Account lookup visibility based on Existing Customer | 1. Create new lead<br>2. Set Existing Customer = "No"<br>3. Observe form<br>4. Change to "Yes"<br>5. Observe form | Account lookup should be hidden when "No" and visible + mandatory when "Yes" |
| BRN#003 | Lead Management | Lead Creation_Manual | EEIC & GCGE- All BUs | No | USR#009 | Account Lookup Validation | Sales Coordinator | **TC009**: Search and link existing account | 1. Create lead with Existing Customer = "Yes"<br>2. Click Account lookup<br>3. Search for existing account "Acme Corp"<br>4. Select account from results<br>5. Save lead | Account should be successfully linked to the lead record |
| BRN#003 | Lead Management | Lead Creation_Manual | EEIC & GCGE- All BUs | No | USR#010 | Data Loader Upload | Admin | **TC010**: Bulk lead upload validation | 1. Login as Admin<br>2. Navigate to Leads<br>3. Use Data Import Wizard<br>4. Upload CSV with multiple leads<br>5. Verify import | All valid leads should be imported successfully. Import wizard should be accessible |
| BRN#007 | Lead Management | Lead Assignment - Manual | EEIC & GCGE- All BUs | No | USR#019 | Manual Lead Ownership Assignment | Bid Manager | **TC011**: Transfer lead ownership | 1. Login as Bid Manager<br>2. Open a lead record<br>3. Click Change Owner<br>4. Select new owner<br>5. Save | Lead ownership should transfer successfully with updated Owner field and timestamp |
| BRN#007 | Lead Management | Lead Assignment - Manual | EEIC & GCGE- All BUs | No | USR#020 | Lead Assignment Notification | Sales Coordinator | **TC012**: Verify bell notification on lead assignment | 1. User A assigns lead to User B<br>2. User B checks notifications<br>3. Click on notification | User B should receive Salesforce bell notification: "You've been added as a Collaborator on Lead [Lead ID]" with link to lead record |
| BRN#007 | Lead Management | Lead Assignment - Manual | EEIC & GCGE- All BUs | No | USR#020 | Queue Assignment Notification | Sales Coordinator | **TC013**: Verify notification when lead assigned to queue | 1. Assign lead to a queue<br>2. All queue members check notifications | All active users in the queue should receive bell notification |
| BRN#008 | Lead Management | Lead Lifecycle - Stages | EEIC & GCGE- All BUs | No | USR#021 | Lead Status: New | Sales Coordinator | **TC014**: Verify default status on lead creation | 1. Create new lead via any method (manual/webform/email)<br>2. Save<br>3. Check Status field | Status should automatically be set to "New" |
| BRN#008 | Lead Management | Lead Lifecycle - Stages | EEIC & GCGE- All BUs | No | USR#022 | Assigned Status | Sales Coordinator | **TC015**: Auto-update to Assigned on ownership change | 1. Create lead with Status = "New"<br>2. Change owner (manual or automatic)<br>3. Observe Status field | Status should auto-update to "Assigned" via flow |
| BRN#008 | Lead Management | Lead Lifecycle - Stages | EEIC & GCGE- All BUs | No | USR#023 | Work In Progress | Sales Coordinator | **TC016**: Manually update status to Work In Progress | 1. Open lead with Status = "Assigned"<br>2. Update any field or upload document<br>3. Change Status to "Work In Progress"<br>4. Save | Status should update successfully to "Work In Progress" |
| BRN#008 | Lead Management | Lead Lifecycle - Stages | EEIC & GCGE- All BUs | No | USR#024 | Document Upload with Validation | Sales Coordinator | **TC017**: Upload Scope of Work with document type tagging | 1. Open lead in Work In Progress status<br>2. Click Upload Documents<br>3. Select Document Type = "Scope of Work"<br>4. Upload file (max 5MB)<br>5. Verify checklist | Document should upload, Document Type should be tagged, corresponding checkbox should be marked |
| BRN#008 | Lead Management | Lead Lifecycle - Stages | EEIC & GCGE- All BUs | No | USR#024 | Document Validation Before Approval | Sales Coordinator | **TC018**: Prevent submission without required documents | 1. Create lead with mandatory documents<br>2. Do not upload documents<br>3. Try to submit for approval | System should display error: "Please upload required documents and confirm completion before submitting the Lead" |
| BRN#008 | Lead Management | Lead Lifecycle - Stages | EEIC & GCGE- All BUs | No | USR#025 | Pending Bid Manager Review | Sales Coordinator | **TC019**: Status change on approval submission | 1. Complete all lead requirements<br>2. Submit for Bid Manager approval<br>3. Check Status field | Status should automatically change to "Pending Bid Manager Review" |
| BRN#008 | Lead Management | Lead Lifecycle - Stages | EEIC & GCGE- All BUs | No | USR#026 | Pending General Manager Review | Sales Coordinator | **TC020**: Two-level approval flow | 1. Bid Manager approves lead<br>2. System routes to GM<br>3. Check Status | Status should change to "Pending General Manager Review" |
| BRN#008 | Lead Management | Lead Lifecycle - Stages | EEIC & GCGE- All BUs | No | USR#027 | Approved Status | Bid Manager/GM | **TC021**: Final approval updates status | 1. Lead in GM approval stage<br>2. GM approves<br>3. Check Status and timestamp | Status should change to "Approved" with timestamp captured |
| BRN#008 | Lead Management | Lead Lifecycle - Stages | EEIC & GCGE- All BUs | No | USR#028 | Disqualified/Rejected | Bid Manager/GM | **TC022**: Reject lead with mandatory reason | 1. Lead in approval process<br>2. Approver clicks Reject<br>3. Try to submit without reason<br>4. Enter rejection reason<br>5. Submit | System should require rejection reason and update Status to "Disqualified/Rejected" with reason logged |
| BRN#008 | Lead Management | Lead Lifecycle - Stages | EEIC & GCGE- All BUs | No | USR#030 | Lead Conversion | Sales Coordinator | **TC023**: Convert approved lead | 1. Lead Status = "Approved"<br>2. Click Convert button<br>3. Complete conversion | System should create Account (if new), Contact, and Opportunity. Status updates to "Converted" |
| BRN#008 | Lead Management | Lead Lifecycle - Stages | EEIC & GCGE- All BUs | No | USR#031 | Auditing and Reporting | Sales Coordinator | **TC024**: Verify history tracking | 1. Create lead<br>2. Change status multiple times<br>3. Assign to different users<br>4. View History related list | All key actions should be timestamped with user and date in Lead History |
| BRN#008 | Lead Management | Lead Lifecycle - Stages | EEIC & GCGE- All BUs | No | USR#032 | Approval Audit Trail | Sales Coordinator | **TC025**: Verify approval history logging | 1. Submit lead for approval<br>2. Bid Manager approves<br>3. GM approves<br>4. Check Approval History | All approval decisions should be logged with approver name, date, time, and comments |
| BRN#009 | Lead Management | Tagging customer account in lead | EEIC & GCGE- All BUs | No | USR#033 | Tagging Existing Customer | Sales Coordinator | **TC026**: Validate existing customer tagging | 1. Set Existing Customer = "Yes"<br>2. Leave Account field blank<br>3. Try to save | System should display validation error for mandatory Account field |
| BRN#009 | Lead Management | Tagging customer account in lead | EEIC & GCGE- All BUs | No | USR#034 | New Customer VAT/TRN Validation | Sales Coordinator | **TC027**: Validate VAT/TRN for new customers | 1. Set Existing Customer = "No"<br>2. Enter VAT/TRN<br>3. Try to convert lead | System should check for duplicates based on VAT/TRN and show duplicate account if found |
| BRN#009 | Lead Management | Tagging customer account in lead | EEIC & GCGE- All BUs | No | USR#034 | Duplicate Detection | Sales Coordinator | **TC028**: Duplicate account detection | 1. Create account with VAT = "123456"<br>2. Create lead with same VAT<br>3. Set Existing Customer = "No"<br>4. Try to convert | System should throw duplication error showing existing account |
| BRN#010 | Lead Management | Lead Activities, tasks and Events | EEIC & GCGE- All BUs | No | USR#035 | Activity Logging | Sales Coordinator | **TC029**: Log activities on lead | 1. Open lead record<br>2. Click Log a Call<br>3. Fill details and save<br>4. Create Task<br>5. Schedule Event | All activities should appear in Activity Timeline with timestamps |
| BRN#011 | Lead Management | Lead Team Members | EEIC & GCGE- All BUs | No | USR#036 | Adding Collaborators | Sales Coordinator | **TC030**: Add team members to lead | 1. Open lead as owner<br>2. Navigate to Lead Team Members section<br>3. Click Add Team Member<br>4. Search and select user<br>5. Set permission (Read/Edit)<br>6. Save | Team member should be added with specified permissions |
| BRN#011 | Lead Management | Lead Team Members | EEIC & GCGE- All BUs | No | USR#037 | Team Member Permissions | Sales Coordinator | **TC031**: Validate read-only vs read-write access | 1. Add team member with Read-Only access<br>2. Login as that user<br>3. Try to edit lead fields<br>4. Add another member with Read-Write<br>5. Login as that user<br>6. Edit fields | Read-Only user cannot edit. Read-Write user can edit |
| BRN#011 | Lead Management | Lead Team Members | EEIC & GCGE- All BUs | No | USR#038 | Lead Owner Control | Sales Coordinator | **TC032**: Only owner can manage team members | 1. Login as non-owner user<br>2. Open lead record<br>3. Check for Add/Remove Team Member buttons | Buttons should be hidden for non-owners using dynamic forms |
| BRN#011 | Lead Management | Lead Team Members | EEIC & GCGE- All BUs | No | USR#039 | Team Member Notifications | Sales Coordinator | **TC033**: Notification on team member addition | 1. Lead owner adds new team member<br>2. Team member checks notifications | Team member should receive bell notification with lead name and owner |
| BRN#011 | Lead Management | Lead Team Members | EEIC & GCGE- All BUs | No | USR#039 | Team Member Removal Notification | Sales Coordinator | **TC034**: Notification on team member removal | 1. Lead owner removes team member<br>2. Removed user checks notifications | User should receive bell notification about removal |
| BRN#014 | Lead Management | Lead Approval | EEIC & GCGE- All BUs | No | USR#052 | Checklist Validation | Sales Coordinator | **TC035**: Prevent approval without checklist completion | 1. Create lead<br>2. Do not complete all checklists<br>3. Click Submit for Approval | System should show error: "Please update the missing fields to submit for approval" |
| BRN#014 | Lead Management | Lead Approval | EEIC & GCGE- All BUs | Yes | USR#053 | Approval Process Routing | Sales Coordinator | **TC036**: Test single-level approval (low score, low value) | 1. Create lead with qualification score < threshold<br>2. Set project value < high threshold<br>3. Submit for approval | Lead should route only to Bid Manager |
| BRN#014 | Lead Management | Lead Approval | EEIC & GCGE- All BUs | Yes | USR#053 | Two-Level Approval | Sales Coordinator | **TC037**: Test two-level approval (high score or high value) | 1. Create lead with qualification score >= threshold OR value >= high threshold<br>2. Submit for approval<br>3. BM approves | Lead should route to BM, then to GM after BM approval |
| BRN#014 | Lead Management | Lead Approval | EEIC & GCGE- All BUs | No | USR#056 | Submit for Approval Button | Sales Coordinator | **TC038**: Submit button functionality | 1. Complete lead requirements<br>2. Click "Submit for Approval" button<br>3. Verify approval process initiated | Approval process should start and status should change to "Pending Bid Manager Review" |
| BRN#017 | Lead Management | Lead Disqualified/Closed | EEIC & GCGE- All BUs | No | USR#061 | Lead Rejection with Reason | Bid Manager/GM | **TC039**: Mandatory rejection reason | 1. Lead in approval<br>2. Approver clicks Reject<br>3. Try to submit without selecting reason<br>4. Select rejection reason<br>5. Submit | Rejection reason should be mandatory. Status should update to "Lead Disqualified" |
| BRN#019 | Lead Management | Reports | EEIC & GCGE- All BUs | No | USR#064 | Lead Reports | Sales Coordinator | **TC040**: Verify lead reports accessible | 1. Login as Sales Coordinator<br>2. Navigate to Reports<br>3. Find Lead Management folder<br>4. Run reports for: My Created Leads, Pending Qualification, Awaiting Inspection, Disqualified Leads, Converted Leads, Lead Aging | Reports should display correct data with appropriate filters |
| BRN#004 | Lead Creation | Lead Unique Identifier | EEIC & GCGE- All BUs | Yes | USR#011 | Unique ID Generation | Sales Coordinator | **TC041**: Auto-generate unique lead identifier | 1. Create lead for GCGE company<br>2. Save lead<br>3. Check Lead Unique ID field<br>4. Create another lead<br>5. Check ID | Format should be G-NNNN-DD.MM.YYYY for GCGE, E-NNNN-DD.MM.YYYY for EEIC. ID should be unique and sequential |
| BRN#004 | Lead Creation | Lead Unique Identifier | EEIC & GCGE- All BUs | Yes | USR#012 | ID Persistence Through Lifecycle | Sales Coordinator | **TC042**: Verify Lead ID carries to Opportunity | 1. Create and convert lead<br>2. Check opportunity record<br>3. Check Quote record<br>4. Verify formula fields | Lead Unique ID should appear on Opportunity and all related records via formula fields |
| BRN#004 | Lead Creation | Lead Unique Identifier | EEIC & GCGE- All BUs | Yes | USR#013 | Searchability and Traceability | Sales Coordinator | **TC043**: Search by Lead Unique ID | 1. Note a Lead Unique ID<br>2. Use Global Search<br>3. Search by Lead ID<br>4. Check results | Should find Lead, Opportunity, and related records |
| FBSP1BRN#014-USR#054 | Lead Management | Lead Approval | EEIC & GCGE- All BUs | No | - | Bid Manager Approval | Bid Manager | **TC044**: BM approval with mandatory reason on rejection | 1. Login as Bid Manager<br>2. Navigate to Approvals<br>3. Open pending lead approval<br>4. Click Reject without reason<br>5. Enter reason and reject | Rejection reason should be mandatory. Lead should route to GM even if BM rejects |
| FBSP1BRN#014-USR#055 | Lead Management | Lead Approval | EEIC & GCGE- All BUs | No | - | GM Approval | General Manager | **TC045**: GM final approval decision | 1. Login as GM<br>2. Open lead approved/rejected by BM<br>3. Review and Approve | Lead Status should update to "Approved". Approval history should log GM decision |
| FBSP1BRN#014-USR#055 | Lead Management | Lead Approval | GCGE- All BUs | No | - | Lead Disqualification Reasons | GM | **TC046**: Select from predefined disqualification reasons | 1. GM rejects lead<br>2. Check disqualification reason picklist | Should contain: Not within our business Core, OEM/Supplier on ADNOC AVL, Estimated value doesn't justify efforts, Cannot be competitive |
| FBSP1BRN#016-USR#060 | Lead Management | Document Upload per Customer Type | EEIC & GCGE- All BUs | Yes | - | UAE Private Companies Documents | Sales Coordinator | **TC047**: Validate UAE Private Company mandatory documents | 1. Create lead from UAE<br>2. Set Customer Type = "Private Companies"<br>3. Try to convert without documents<br>4. Upload: Trade License, VAT Certificate, KYC (if value > threshold)<br>5. Convert | System should prevent conversion without required documents. Should allow after upload |
| FBSP1BRN#016-USR#060 | Lead Management | Document Upload per Customer Type | EEIC & GCGE- All BUs | Yes | - | UAE Government - No Docs Required | Sales Coordinator | **TC048**: UAE Government entity conversion | 1. Create lead from UAE<br>2. Set Customer Type = "UAE government"<br>3. Convert lead without uploading documents | Conversion should succeed without document requirements |
| FBSP1BRN#016-USR#060 | Lead Management | Document Upload per Customer Type | EEIC & GCGE- All BUs | Yes | - | Outside UAE Private Companies | Sales Coordinator | **TC049**: Non-UAE Private Company documents | 1. Create lead from outside UAE<br>2. Set Customer Type = "Private Companies"<br>3. Upload: Certificate of Incorporation, VAT Certificate Equivalent, KYC (if applicable)<br>4. Convert | Documents should be mandatory per metadata configuration |
| FBSP1BRN#016-USR#060 | Lead Management | Document Upload per Customer Type | EEIC & GCGE- All BUs | Yes | - | Lead Value < 3000 AED Exemption | Sales Coordinator | **TC050**: Low-value lead exemption from onboarding | 1. Create lead with value < 3000 AED<br>2. Convert lead | Account should be created but onboarding process should be skipped |
| FBSP1BRN#003 | Lead Management | Customer Type | EEIC & GCGE- All BUs | Yes | - | Customer Type Picklist | Sales Coordinator | **TC051**: Verify customer type picklist values | 1. Create new lead<br>2. Click Customer Type field | Should show: Private Companies, Government Related Companies (with sub-type), UAE government, Other governments, Group Companies, Other related parties, Individuals, Employees |
| FBSP1BRN#08 | Lead Management | Sensitive Country Approval | EEIC & GCGE- All BUs | Yes | - | Sensitive Country Stage | Sales Coordinator | **TC052**: Sensitive country requires additional approval | 1. Create lead from sensitive country<br>2. Country lookup auto-populates sensitive flag<br>3. Submit for approval | Lead should go through "Sensitive Country Approved" stage before BM approval |
| FBSP1BRN#08 | Lead Management | Country Information | EEIC & GCGE- All BUs | Yes | - | Country Auto-population | Sales Coordinator | **TC053**: Country object lookup and auto-population | 1. Create lead<br>2. Select Country from lookup<br>3. Observe fields | Country Classification, Sensitive flag, ISO Codes should auto-populate as read-only |

---

## Opportunity Management Test Cases

| BRN | Module | Sub-Module | Company | CR | USR No | Title | User | Test Scenario | Test Steps | Expected Result |
|-----|--------|------------|---------|----|----|-------|------|---------------|------------|-----------------|
| BRN#051 | Opportunity Management | Ownership | EEIC & GCGE- All BUs | No | USR#036 | Default Ownership | Bid manager | **TC054**: Creator becomes default owner | 1. Login as Bid Manager<br>2. Create new opportunity<br>3. Save<br>4. Check Owner field | Creator should be automatically assigned as owner with history tracking enabled |
| BRN#051 | Opportunity Management | Ownership Transfer | EEIC & GCGE- All BUs | No | USR#036 | Transfer Ownership | Bid manager | **TC055**: Transfer opportunity ownership | 1. Open opportunity<br>2. Click Change Owner<br>3. Select new owner<br>4. Save<br>5. Check history | Ownership should transfer successfully with change logged in history |
| BRN#052 | Opportunity Management | Lead Conversion Ownership | EEIC & GCGE- All BUs | No | USR#037 | Auto Ownership from BU Matrix | Sales Coordinator | **TC056**: Opportunity owner assigned from BU mapping | 1. Convert lead with specific BU<br>2. Check opportunity owner<br>3. Verify against BU allocation matrix | Owner should be assigned based on Business Unit Mapping Rule. Audit trail should log the change |
| BRN#053 | Opportunity Management | Team Members | EEIC & GCGE- All BUs | No | USR#038 | Add Opportunity Team Members | Bid manager | **TC057**: Manually add team members with roles | 1. Open opportunity<br>2. Add team member<br>3. Assign role (Presales/Finance/Engineering)<br>4. Set access (Read-Only/Read-Write)<br>5. Save | Team member should be added with correct role and access level |
| BRN#053 | Opportunity Management | Team Members | EEIC & GCGE- All BUs | No | USR#038 | Only Owner Can Manage | Bid manager | **TC058**: Non-owner cannot add team members | 1. Login as non-owner<br>2. Open opportunity<br>3. Check for team member management options | Add/Remove options should not be visible to non-owners |
| BRN#054 | Opportunity Management | Auto Team Member Assignment | EEIC & GCGE- All BUs | Yes | USR#039 | Auto-assign Bid Manager | Bid manager | **TC059**: Bid Manager auto-added per BU | 1. Create opportunity in Energy BU (e.g., EEIC Projects)<br>2. Save<br>3. Check Opportunity Team Members | Correct Bid Manager (e.g., Ramiz for EEIC Projects) should be auto-added based on Business Unit Mapping Rule |
| BRN#055 | Opportunity Management | Parent-Child Opportunity | EEIC & GCGE- All BUs | Yes | USR#040 | Create Child Opportunity | Bid manager | **TC060**: Link child to parent opportunity | 1. Create parent opportunity<br>2. Create child opportunity<br>3. Set Parent Opportunity lookup to parent<br>4. Save | Child should link to parent with independent fields for costing and approval |
| BRN#055 | Opportunity Management | Parent-Child Opportunity | EEIC & GCGE- All BUs | Yes | USR#041 | Consolidated Parent View | Bid manager | **TC061**: Parent shows rollup of child values | 1. Create parent opportunity<br>2. Create 2 child opportunities<br>3. Set amounts on children<br>4. Close children as Won<br>5. Check parent | Parent should show total value using DLRS package rollup |
| BRN#056 | Opportunity Management | Lifecycle | EEIC & GCGE- All BUs | No | USR#043 | Discovery Analysis Default Stage | Bid manager | **TC062**: New opportunity starts in Discovery Analysis | 1. Create new opportunity<br>2. Check StageName field | Default stage should be "Discovery Analysis" |
| BRN#056 | Opportunity Management | Lifecycle | EEIC & GCGE- All BUs | No | USR#043 | Opportunity Type Field | Bid manager | **TC063**: Classify opportunity type | 1. Create opportunity<br>2. Check Type picklist<br>3. Select value | Should offer: Standard, Non-Standard, Both |
| BRN#057 | Opportunity Management | Activities | EEIC & GCGE- All BUs | No | USR#051 | Activity Logging | Bid manager | **TC064**: Log activities on opportunity | 1. Open opportunity<br>2. Log call, meeting, task<br>3. Check Activity Timeline | All activities should be logged with timestamps and visible to team members |
| BRN#076 | Opportunity Management | Closed Won | EEIC & GCGE- All BUs | No | USR#052 | PO Date Mandatory | Bid manager | **TC065**: Prevent Closed Won without PO Date | 1. Change opportunity stage to Closed Won<br>2. Leave PO Date blank<br>3. Try to save | Validation should prevent save: "PO Date is mandatory for Closed Won stage" |
| BRN#076 | Opportunity Management | Closed Won | EEIC & GCGE- All BUs | No | USR#052 | PO Date with Closed Won | Bid manager | **TC066**: Close opportunity as won with PO Date | 1. Enter PO Date<br>2. Enter PO Number<br>3. Upload PO document<br>4. Change stage to Closed Won<br>5. Save | Opportunity should close successfully |
| BRN#077 | Opportunity Management | Closed Lost | EEIC & GCGE- All BUs | No | USR#054 | Lost Reason Mandatory | Bid manager | **TC067**: Mandatory reason for Closed Lost | 1. Change stage to Closed Lost<br>2. Leave Lost Reason blank<br>3. Try to save<br>4. Select reason<br>5. Save | Lost Reason should be mandatory. Should save after reason provided |
| BRN#078 | Opportunity Management | Regret Letter | EEIC & GCGE- All BUs | No | USR#055-056 | Send Regret Letter | Bid manager | **TC068**: Send regret letter to customer | 1. Open lost opportunity<br>2. Click Send Email<br>3. Select regret letter template<br>4. Verify merge fields populated<br>5. Edit if needed<br>6. Send | Email should send with template populated. Activity should be logged |
| BRN#050 | Opportunity Management | Direct Creation | GCGE- BU2 | No | USR#035 | Create from Account | Sales Coordinator | **TC069**: Create opportunity from account | 1. Open account record<br>2. Create New Opportunity<br>3. Fill mandatory fields: Name, BU, Currency, Close Date, Stage, Type, Sub-type, Amount, Cluster Company, Estimated Closing Quarter/Year, Probability, RFT Number<br>4. Save | Opportunity should be created and linked to account |
| BRN#013USR#045 | Opportunity Management | Site Inspection | EEIC & GCGE- All BUs | No | - | Site Inspection Upload | Bid Manager | **TC070**: Upload site inspection on opportunity | 1. Open opportunity<br>2. Set Site Inspection Required = "Yes"<br>3. Upload site inspection document<br>4. Verify checkbox | Site inspection file should upload and corresponding checkbox should update |
| BRN#016 | Opportunity Management | Data Transfer on Conversion | EEIC & GCGE- All BUs | Yes | USR#060 | Lead Data Transfer | Sales Coordinator | **TC071**: Verify data transfer on lead conversion | 1. Create lead with activities, notes, team members<br>2. Upload non-site inspection documents<br>3. Upload site inspection<br>4. Convert lead<br>5. Check opportunity and account | Activities should transfer to Opportunity. Non-site inspection docs should link to Account. Site inspection should link to Opportunity |
| BRN#020 | Lead Management | Reports | EEIC & GCGE- All BUs | Yes | USR#065-069 | Various Reports | Multiple Users | **TC072**: Role-specific report access | 1. Login as different roles (BM, GM, SC, Technical PM)<br>2. Navigate to Reports<br>3. Check accessible reports<br>4. Run reports<br>5. Apply filters | Each role should see appropriate reports with correct data filtering |

---

## Account Management Test Cases

| BRN | Module | Sub-Module | Company | CR | USR No | Title | User | Test Scenario | Test Steps | Expected Result |
|-----|--------|------------|---------|----|----|-------|------|---------------|------------|-----------------|
| BRN#021 | Account Management | Account Creation | EEIC & GCGE- All BUs | No | USR#070 | Manual Account Creation Fields | Sales Coordinator | **TC073**: Create account with all mandatory fields | 1. Navigate to Accounts (should not have New button for non-admin)<br>2. Accounts should be created via lead conversion<br>3. Convert lead<br>4. Verify account created with: Company Name, TRN/VAT, Trade License Number, Trade License Expiry, Phone, Address, Country, Email | Account should be created from lead conversion only (not manual) with all fields populated |
| BRN#021 | Account Management | Account Creation | EEIC & GCGE- All BUs | No | USR#071 | Auto Account Creation on Lead Conversion | Sales Coordinator | **TC074**: Account creation for new customer | 1. Create lead with Existing Customer = "No"<br>2. Fill all account details on lead<br>3. Convert lead | New account should be auto-created with data from lead |
| BRN#021 | Account Management | Account Creation | EEIC & GCGE- All BUs | No | USR#071 | Link to Existing Account | Sales Coordinator | **TC075**: Link to existing account on conversion | 1. Create lead with Existing Customer = "Yes"<br>2. Select existing account<br>3. Convert lead | Should link to existing account without creating duplicate |
| BRN#021 | Account Management | Account Creation | EEIC & GCGE- All BUs | No | USR#072 | Existing Account Search | Sales Coordinator | **TC076**: Search and select existing account | 1. On lead, enter VAT/TRN<br>2. View potential duplicates<br>3. Select from list | Should show existing accounts matching VAT/TRN/Trade License for selection |
| BRN#022 | Account Management | Parent Account | EEIC & GCGE- All BUs | No | USR#073 | Account Hierarchy | Sales Coordinator | **TC077**: Create parent-child account relationship | 1. Create/open child account<br>2. Set Parent Account lookup<br>3. Save<br>4. Click View Hierarchy | Should show account hierarchy with parent-child relationship. Both should operate independently |
| BRN#023 | Account Management | Duplicate Prevention | EEIC & GCGE- All BUs | No | USR#074 | Duplicate Check on Creation | Sales Coordinator | **TC078**: Prevent duplicate account creation | 1. Create account with TRN = "123456"<br>2. Try to create another with same TRN<br>3. For Enterprise UAE: match on TRN + Trade License<br>4. For Government UAE: match on VAT only | System should block creation showing duplicate warning with suggested matches |
| BRN#024 | Account Management | Onboarding | EEIC & GCGE- All BUs | No | USR#076 | Onboarding Checklist | Sales Coordinator | **TC079**: Complete account onboarding checklist | 1. Navigate to account from binding quote process<br>2. View checklist<br>3. Enter: Bank Details, Payment Terms, Credit Terms<br>4. Upload: Trade License, Passport, POA<br>5. Add minimum 2 trade references<br>6. For non-govt: verify Etihad Bureau credit score fetch | Checklist should track progress. All items must be complete before triggering onboarding |
| BRN#024 | Account Management | Onboarding | EEIC & GCGE- All BUs | No | USR#077 | Trigger Onboarding Process | Sales Coordinator | **TC080**: Initiate onboarding after checklist | 1. Complete all checklist items<br>2. Click trigger onboarding<br>3. Try to trigger again | Should move account to next stage. Should prevent re-initiation |
| BRN#028 | Account Management | Notifications | EEIC & GCGE- All BUs | No | USR#084 | Customer Onboarding Email | Sales Coordinator | **TC081**: Customer receives onboarding confirmation | 1. Complete account onboarding<br>2. Mark status as "Onboarded"<br>3. Check customer email | Customer should receive email: "Your Account with [Company] is Now Active" with template content |
| BRN#028 | Account Management | Notifications | EEIC & GCGE- All BUs | No | USR#085 | Internal Stakeholder Notification | Sales Coordinator | **TC082**: GM and FM receive bell and email notifications | 1. Mark account as "Onboarded"<br>2. Check GM and FM notifications and emails | Should receive bell notification and email with account details: Name, ERP ID, Industry, Onboarding Date |
| BRN#029 | Account Management | Trade License Tracking | EEIC & GCGE- All BUs | No | USR#086 | Capture Expiry Date | Sales Coordinator | **TC083**: Store trade license expiry date | 1. Create account<br>2. Enter Trade License Expiry Date<br>3. Save | Date should be stored on account record |
| BRN#029 | Account Management | Trade License Tracking | EEIC & GCGE- All BUs | No | USR#087 | Automated Expiry Reminders | Sales Coordinator | **TC084**: Scheduler sends reminders | 1. Create account with expiry 30 days from today<br>2. Run TradeLicenseExpiryScheduler<br>3. Check notifications and emails<br>4. Repeat for 15, 5 days before | SC and customer should receive bell notification and email at 30, 15, 5 days before expiry |
| BRN#029 | Account Management | Trade License Tracking | EEIC & GCGE- All BUs | No | USR#087 | Post-Expiry Reminders | Sales Coordinator | **TC085**: Reminders after expiry | 1. Account with expiry 1 day ago<br>2. Run scheduler<br>3. Check notifications | Should send reminders at 1, 15, 30 days after expiry if not renewed |
| BRN#029 | Account Management | Trade License Tracking | EEIC & GCGE- All BUs | No | USR#088 | Post-Expiry Restriction | Sales Coordinator | **TC086**: Block binding quotes after threshold | 1. Account expired > threshold days<br>2. Scheduler sets flag<br>3. Try to create binding quote | Flag should be set. Binding quote creation should be blocked |
| BRN#029 | Account Management | Trade License Tracking | EEIC & GCGE- All BUs | No | USR#089 | License Renewal | Sales Coordinator | **TC087**: Update renewed license | 1. Open expired account<br>2. Update Trade License Number and Expiry Date<br>3. Save | Flag should be cleared. Restrictions should be lifted. History should track changes |
| BRN#033 | Account Management | KYC and Document Storage | EEIC & GCGE- All BUs | Yes | - | Document Storage on Conversion | Sales Coordinator | **TC088**: Documents route to account on lead conversion | 1. Upload documents on lead (Trade License, VAT Cert)<br>2. Upload site inspection<br>3. Convert lead<br>4. Check Account Files<br>5. Check Opportunity Files | Non-site inspection docs should link to Account. Site inspection should link to Opportunity. No duplicates |
| BRN#035 | Account Management | Vendor Management | EEIC & GCGE- All BUs | Yes | - | Mark Account as Vendor | Sales Coordinator | **TC089**: Vendor checkbox and lookup | 1. Open account<br>2. Check "Is Vendor" checkbox<br>3. Observe Vendor Details lookup<br>4. Search existing or create new vendor<br>5. Save | Vendor lookup should become visible and required. Should link to Vendor Details object |
| BRN#036 | Account Management | Intercompany | All | Yes | USR#104 | Intercompany Account | Sales Coordinator | **TC090**: Create group company account | 1. Create account for Ghobash subsidiary<br>2. Set Parent Company = Ghobash Group<br>3. Set Relationship Type<br>4. Save<br>5. View hierarchy | Should create with hierarchy linkage visible in Account Hierarchy view |
| BRN#038 | Account Management | Reports | EEIC & GCGE- All BUs | Yes | USR#106 | Standard Account Reports | Sales Manager | **TC091**: Access account management reports | 1. Login as Sales Manager<br>2. Navigate to Reports<br>3. Open Account Management folder | Should access: Accounts by Owner, Accounts without Activities, Accounts with Activities, Assessment Status, Expiring Trade Licenses |
| FBSP2BRN#076 | Opportunity Management | Qualification Stage | EEIC & GCGE- All BUs | Yes | - | UAE Customer Details for Binding Quote | Bid Manager | **TC092**: Validate UAE customer mandatory fields before binding quote | 1. Create opportunity for UAE customer<br>2. Try to create Firm (Binding) Quote<br>3. Missing mandatory account fields<br>4. Fill: Legal Entity Name, Trade Name, Business Type, Legal Address, Operating Address, Trade License Number/Expiry, Tax Registration, Business Segment | Should show validation error listing missing fields. Should allow binding quote after completion |
| FBSP2BRN#076 | Opportunity Management | Qualification Stage | EEIC & GCGE- All BUs | Yes | - | Non-UAE Customer Details | Bid Manager | **TC093**: Non-UAE customer fields for binding quote | 1. Create opportunity for non-UAE customer<br>2. Try to create Firm Quote<br>3. Fill mandatory: Legal Entity Name, Company Registration Number, Trade Name, Business Type, Legal/Operating Address, Tax Registration, Business Segment | Validation should enforce non-UAE specific fields |
| BRN#076 | Opportunity Management | Closed Won | EEIC & GCGE- All BUs | Yes | USR#053 | Post-Closure Information Collection | Bid manager | **TC094**: Collect information after Closed Won | 1. Close opportunity as Won<br>2. Navigate to related account<br>3. Validate fields on account: Primary Contact Name/Details, Banking Information (for credit customers >3000 AED), Tax & Invoice Information, Product/Service Categories | System should require these fields on account before allowing final closure |
| BRN#079 | Opportunity Management | Reports | EEIC & GCGE- All BUs | Yes | USR#057-058 | Opportunity Analytics | Sales Manager/Bid Manager | **TC095**: Access opportunity reports and dashboards | 1. Navigate to Reports<br>2. Open Opportunity Reports folder<br>3. Run: Pipeline by Stage, Aging Report, Forecast Report, Conversion Rate, Won/Lost Analysis, Source Performance | Reports should display with filters: owner, stage, region, period. Dashboards should show visualizations |

---

## Contact Management Test Cases

| BRN | Module | Sub-Module | Company | CR | USR No | Title | User | Test Scenario | Test Steps | Expected Result |
|-----|--------|------------|---------|----|----|-------|------|---------------|------------|-----------------|
| BRN#039 | Contact Management | Contact Creation | EEIC & GCGE- All BUs | No | USR#107 | Standalone Contact Creation | Sales Coordinator | **TC096**: Create contact independently | 1. Navigate to Contacts<br>2. Click New<br>3. Fill mandatory fields: First Name, Last Name, Email, Phone, Company Name (Account lookup), Role<br>4. Save | Contact should be created with all mandatory fields |
| BRN#039 | Contact Management | Contact Creation | EEIC & GCGE- All BUs | No | USR#107 | Duplicate Prevention | Sales Coordinator | **TC097**: Prevent duplicate contacts | 1. Create contact with Email = "test@example.com" and Phone = "1234567890"<br>2. Try to create another with same Email+Phone combination | Duplicate rule should prevent creation showing warning |
| BRN#039 | Contact Management | Contact Creation | EEIC & GCGE- All BUs | No | USR#108 | Conversion-based Creation | Sales Coordinator | **TC098**: Auto-create contact on lead conversion | 1. Create lead with contact details<br>2. Convert lead<br>3. Check contact record | Contact should be created from lead with all fields populated |
| BRN#040 | Contact Management | Contact Update | EEIC & GCGE- All BUs | No | USR#109 | Update Contact Information | Sales Coordinator | **TC099**: Update contact fields with validation | 1. Open contact<br>2. Update: Designation, Phone, Email, Department<br>3. Enter invalid email format<br>4. Try to save<br>5. Correct email<br>6. Save | Should validate email/phone format. Changes should be tracked in field history |
| BRN#041 | Contact Management | Contact Hierarchy | EEIC & GCGE- All BUs | No | USR#110 | Contact Roles | Sales Coordinator | **TC100**: Assign contact roles and reporting | 1. Open contact<br>2. Mark as Primary Contact<br>3. Set Role (Primary Decision Maker/Approver/Technical Evaluator/Finance Contact)<br>4. Set Reports To (using managed package)<br>5. View hierarchy | Should allow only one primary contact per account. Hierarchy should visualize reporting structure |
| BRN#042 | Contact Management | Multi-Account Association | EEIC & GCGE- All BUs | No | USR#111 | Multi-Account Contact | Sales Coordinator | **TC101**: Associate contact with multiple accounts | 1. Create contact<br>2. Link to Account 1<br>3. From contact, add Account 2<br>4. Set one as primary<br>5. View contact record | Contact should display all associated accounts with primary indicator |

---

## Site/Location Management Test Cases

| BRN | Module | Sub-Module | Company | CR | USR No | Title | User | Test Scenario | Test Steps | Expected Result |
|-----|--------|------------|---------|----|----|-------|------|---------------|------------|-----------------|
| BRN#043 | Site Management | Operating Unit Setup | All | Yes | - | Operating Unit Master | Admin | **TC102**: Predefined Operating Units | 1. Navigate to Operating Unit object<br>2. View records<br>3. Try to create/edit (as non-admin) | OU master should contain BUSINESS_GROUP_ID, ORGANIZATION_ID, NAME, SHORT_CODE, SET_OF_BOOKS_ID, BU. Non-admins cannot modify |
| BRN#043 | Site Management | Location Creation | All | Yes | - | Create Account Site | Sales Coordinator | **TC103**: Map account to OU and location | 1. Open account<br>2. Click Create New Location<br>3. Select: New or Existing address<br>4. If new: enter Country, Address1-4, City, Postal Code, State<br>5. Select Operating Unit (filtered by BU)<br>6. Select Shipping Type (Bill-To/Ship-To)<br>7. Save | Should create Location record and Account Site record linking Account + OU + Location + Shipping Type |
| BRN#043 | Site Management | Location Creation | All | Yes | - | Location Status and ERP Readiness | Sales Coordinator | **TC104**: New location activation and ERP sync | 1. Create location<br>2. Check Status<br>3. Check ERP ID field<br>4. After ERP sync, verify ERP Ready flag | Status should auto-set to "Active". ERP Ready should be FALSE until ERP ID populated by OIC integration |
| BRN#043 | Site Management | Location Creation | All | Yes | - | Reuse Location for Multiple Sites | Sales Coordinator | **TC105**: Same location for different OU/Site Type | 1. Create Account Site 1: Dubai Office + OU1 + Bill-To<br>2. Create Account Site 2: Dubai Office + OU2 + Ship-To | Should allow reuse of same location across different OUs and site types |
| BRN#043 | Site Management | Location Viewing | All | Yes | - | View Account Sites | Sales Coordinator | **TC106**: List view of account sites | 1. Navigate to account<br>2. View Account Sites related list | Should display: Site Name, Operating Unit, Location, Site Type, Status, Created Date/By with filter options |
| BRN#043 | Site Management | Sensitive Country Location | All | Yes | - | Sensitive Country Location Restriction | Sales Coordinator | **TC107**: Restrict sensitive country location creation | 1. Try to create location for sensitive country directly<br>2. System should require lead process<br>3. Create lead with sensitive country<br>4. Complete AML approval<br>5. Convert | For sensitive countries, must go through Lead → AML Approval → Conversion. Location should be marked: Sensitive Country = TRUE, AML Approved = TRUE |
| BRN#044 | Site Management | Location Update | All | Yes | - | Country Restriction | Sales Coordinator | **TC108**: Prevent mixed-country locations | 1. Account has location from UAE<br>2. Try to create location from India<br>3. Check error | Should block with error: "This Account already has Locations from [Country]. To add a Location from another Country, create a new Account" |
| BRN#044 | Site Management | Location Update | All | Yes | - | Inactivate Location | Sales Coordinator | **TC109**: Mark location inactive | 1. Open active location<br>2. Change Status to Inactive<br>3. Provide reason<br>4. Save<br>5. Try to use in new transaction | Should capture reason, Inactivated By, date. Should not appear in new transaction dropdowns. Should show warning on existing linked records |

---

## Integration and API Test Cases

| BRN | Module | Sub-Module | Company | Test Scenario | Test Steps | Expected Result |
|-----|--------|------------|---------|---------------|------------|-----------------|
| BRN#024 | Account Management | Credit Score Integration | EEIC & GCGE- All BUs | **TC110**: Etihad Bureau credit score API | 1. Create UAE account<br>2. Enter Trade License Number<br>3. Save<br>4. Verify credit score field populated | System should call Etihad Bureau API and populate External Credit Score automatically |
| N/A | Account Management | AML API Integration | All | **TC111**: Idenfo AML API integration | 1. Create account<br>2. Trigger AML check<br>3. Verify API call logs<br>4. Check AML_API_Response__c and Idenfo_Id__c fields | Should successfully call Idenfo API for authentication and customer creation. Fields should populate with response |

---

## Trigger and Automation Test Cases

| Component | Test Scenario | Test Steps | Expected Result |
|-----------|---------------|------------|-----------------|
| LeadTrigger | **TC112**: Lead trigger fires on insert and update | 1. Insert new lead<br>2. Update lead<br>3. Check debug logs | beforeInsert and beforeUpdate should fire. LeadTriggerHandler should execute |
| OpportunityTrigger | **TC113**: Opportunity trigger handles all contexts | 1. Insert opportunity<br>2. Update opportunity<br>3. Delete opportunity<br>4. Undelete opportunity | All trigger contexts should execute correctly: before/after insert/update/delete, after undelete |
| ContentDocumentLinkTrigger | **TC114**: Document routing after insert | 1. Upload non-site inspection document to opportunity<br>2. Check ContentDocumentLinks<br>3. Upload site inspection to opportunity<br>4. Check links | Non-site inspection should route to Account. Site inspection should stay on Opportunity. Original opp links should be deleted for non-site docs |
| BusinessUnitMappingRuleTrigger | **TC115**: Rule change triggers lead reevaluation | 1. Create leads with BU = "Automation"<br>2. Create/update Business Unit Mapping Rule for Automation<br>3. Check AsyncApexJob | ReevaluateLeadsBatch should be enqueued. Leads should be reevaluated |
| TradeLicenseExpiryScheduler | **TC116**: Scheduled job execution | 1. Schedule TradeLicenseExpiryScheduler<br>2. Create accounts with various expiry dates<br>3. Execute scheduler<br>4. Check notifications and flags | Accounts should be notified at correct intervals. Flags should be set/cleared appropriately |

---

## Component and Controller Test Cases

| Component/Class | Test Scenario | Test Steps | Expected Result |
|-----------------|---------------|------------|-----------------|
| CustomLeadConverter | **TC117**: Convert lead with existing account | 1. Lead with Account__c populated<br>2. Call convertLead()<br>3. Check result | Should use existing account. Should return Opportunity ID |
| CustomLeadConverter | **TC118**: Convert lead for Energy cluster with Vertical Head | 1. Lead with RecordType = EEIC<br>2. Vertical Head populated and active<br>3. Convert | Opportunity owner should be Vertical Head |
| CustomLeadConverter | **TC119**: Convert lead for Technology cluster (manual owner) | 1. Lead with RecordType = CNS<br>2. Convert | Should not auto-assign owner (manual selection required) |
| CustomLeadConverter | **TC120**: Handle conversion errors | 1. Lead with invalid data<br>2. Call convertLead()<br>3. Catch exception | Should throw AuraHandledException with error details |
| DocumentRuleEvaluator | **TC121**: Evaluate document rules with ALL logic | 1. Create Document Rule with Custom_Logic__c = 'ALL'<br>2. Add 2 conditions<br>3. Create record matching all conditions<br>4. Call getMandatoryDocuments() | Should return rule as applicable with mandatory document types |
| DocumentRuleEvaluator | **TC122**: Evaluate with ANY logic | 1. Create rule with logic = 'ANY'<br>2. Add conditions<br>3. Record matches one condition<br>4. Evaluate | Should return rule as applicable |
| DocumentRuleEvaluator | **TC123**: Custom formula evaluation | 1. Create rule with logic = 'CUSTOM'<br>2. Set formula = "(1 AND 2) OR 3"<br>3. Add 3 conditions<br>4. Evaluate | Should correctly parse and evaluate custom formula |
| DocumentRuleEvaluator | **TC124**: No conditions always matches | 1. Create rule with no conditions<br>2. Evaluate against any record | Should return true (always applicable) |
| DocumentUploadController | **TC125**: Upload file with document type | 1. Call uploadFileApex() with recordId, contentDocumentId, docType<br>2. Check ContentVersion<br>3. Check ContentDocumentLink | Should update Document_Type__c on ContentVersion. Should create link if not exists |
| DocumentUploadController | **TC126**: Get uploaded documents | 1. Upload multiple documents to record<br>2. Call getUploadedDocTypes()<br>3. Verify response | Should return list with: docType, fileName, contentDocumentId, contentVersionId, fileExtension, uploadedBy, uploadedDate |
| DocumentUploadController | **TC127**: Update checklist flag - all mandatory uploaded | 1. Create rule with mandatory docs<br>2. Upload all mandatory documents<br>3. Call updateChecklistFlag() | Checkbox field should be set to TRUE |
| DocumentUploadController | **TC128**: Update checklist flag - mandatory missing | 1. Upload only some mandatory documents<br>2. Call updateChecklistFlag() | Checkbox should remain FALSE or be reset to FALSE |
| DocumentValidationHelper | **TC129**: Validate documents on status change | 1. Create Document Rule with Status_Field__c = 'Status', Status_Value__c = 'Work in Progress'<br>2. Add mandatory document detail<br>3. Create record in status<br>4. Change status without uploading doc | Should call addError() preventing status change: "Mandatory documents for rule [Name] must be uploaded" |
| DocumentValidationHelper | **TC130**: Bypass validation if checkbox is true | 1. Same setup as TC129<br>2. Set Checkbox_Field__c = TRUE<br>3. Change status | Should allow status change (checkbox bypasses validation) |
| DocumentRuleController | **TC131**: Insert document rule with conditions and details | 1. Prepare rule, conditions, details data<br>2. Call insertRecords()<br>3. Query created records | Should create Document_Rule__c, Document_Condition__c, Document_Detail__c records |
| DocumentRuleController | **TC132**: Get fields for object | 1. Call getFieldsForObject('Lead')<br>2. Check returned list | Should return sorted list of field API names |
| DocumentRuleController | **TC133**: Get picklist options | 1. Call getPicklistOptions('Lead')<br>2. Check response | Should return map of field name to picklist values |
| FieldPickerController | **TC134**: Get object fields sorted | 1. Call getObjectFields('Lead')<br>2. Verify response | Should return sorted FieldOption list with label and value |
| ContentDocumentHandler | **TC135**: Route non-site inspection docs to account | 1. Create opportunity with account<br>2. Upload document with type ≠ site inspection<br>3. Check ContentDocumentLinks | Document should link to Account. Opportunity link should be deleted |
| ContentDocumentHandler | **TC136**: Keep site inspection docs on opportunity | 1. Upload document with Document_Type__c from Site_Inspection_Doc_Type__c metadata<br>2. Check links | Document should remain on Opportunity. Account link should be deleted |
| OpportunityTriggerHelper | **TC137**: Auto-assign team members on opportunity creation | 1. Create opportunity with Business Unit = "Manpower"<br>2. Business Unit Mapping Rule exists for this BU with Purpose = 'Team Members'<br>3. Check OpportunityTeamMember records | Users from Related_Users__r should be auto-added with correct roles and access levels |
| OpportunityTriggerHelper | **TC138**: Set record type from lead on conversion | 1. Convert lead with RecordType = EEIC<br>2. Check opportunity RecordTypeId | Opportunity should have matching RecordType based on Lead_Source_Record_Type_Name__c |
| LeadTriggerHelper | **TC139**: Evaluate rules on lead insert | 1. Create Business Unit Mapping Rule<br>2. Add conditions<br>3. Insert lead matching conditions | Vertical_Head__c should be assigned from matching rule |
| LeadTriggerHelper | **TC140**: Clear vertical head if no rule matches | 1. Lead with BU that has no active rule<br>2. Insert lead | Vertical_Head__c should be null |
| LeadTriggerHelper | **TC141**: Evaluate document rules on update | 1. Lead in "Work in Progress"<br>2. Document rule requires docs for status change<br>3. Change status without docs | Should add error preventing update |
| ReevaluateLeadsBatch | **TC142**: Batch reevaluation of leads | 1. Create multiple leads with BU = "Automation"<br>2. Update Business Unit Mapping Rule<br>3. Execute batch | Batch should query leads with Status = New or Work in Progress, matching BU. Should update Vertical_Head__c |
| BusinessUnitMappingRuleTriggerHelper | **TC143**: Enqueue batch on rule insert | 1. Insert new Business Unit Mapping Rule<br>2. Check AsyncApexJob | Should enqueue ReevaluateLeadsBatch with affected BUs |
| BusinessUnitMappingRuleTriggerHelper | **TC144**: Enqueue batch on rule update | 1. Update rule (change Vertical_Head__c or Is_Active__c)<br>2. Check jobs | Should enqueue batch for both old and new BU if BU changed |
| BusinessUnitMappingRuleTriggerHelper | **TC145**: Enqueue batch on rule delete | 1. Delete rule<br>2. Check jobs | Should enqueue batch for deleted rule's BU |
| HttpCalloutService | **TC146**: Send authenticated request | 1. Set up test mock<br>2. Call sendAuthenticatedRequest()<br>3. Verify headers | Should set Authorization: Bearer [token], apiKey, apiSecret, static headers from metadata |
| CallOutToIndenfoAMLAPI | **TC147**: AML API callout | 1. Create account<br>2. Call callIdenfoAPI()<br>3. Mock success response | Should authenticate, call Create Customer API, update AML_API_Response__c and Idenfo_Id__c |
| HandleErrorLog | **TC148**: Log integration errors | 1. Simulate integration exception<br>2. Call getIntegrationErrorLog()<br>3. Insert log | Should create Error_Logs__c record with: Class_Name__c, Method_Name__c, Log_Message__c, Stack_Trace__c, Related_Record_ID__c |

---

## LWC Component Test Cases (Manual Testing Required)

| Component | Test Scenario | Test Steps | Expected Result |
|-----------|---------------|------------|-----------------|
| customLeadConvertButton | **TC149**: Convert lead button UI | 1. Open lead record<br>2. Click Convert button<br>3. Watch spinner<br>4. Verify navigation | Spinner should display during conversion. Should navigate to opportunity on success. Should show error toast on failure |
| documentRuleWizard | **TC150**: Create document rule via wizard | 1. Open wizard<br>2. Step 1: Select SObject, set sequence, active, logic<br>3. Step 2: Add conditions<br>4. Step 3: Add document details<br>5. Save | Should create Document Rule with related conditions and details. Should show success message |
| documentRuleWizard | **TC151**: Dynamic field rendering in wizard | 1. Select SObject = Lead<br>2. In conditions, select field (picklist type)<br>3. Select field (boolean type)<br>4. Select field (date type) | Value input should render as: combobox for picklist, checkbox for boolean, date input for date, number input for number, text input for others |
| documentRuleWizard | **TC152**: Custom formula validation | 1. Set Custom Logic = "Custom"<br>2. Enter invalid formula<br>3. Try to proceed | Should show error for invalid formula |
| dynamicUploadDocument | **TC153**: Upload with status gating (Lead) | 1. Lead with Status = "Qualified"<br>2. Open component<br>3. Check Upload button | Button should be disabled with note: "You can only upload when status is New or Work In Progress" |
| dynamicUploadDocument | **TC154**: Upload and view documents | 1. Open record (valid status)<br>2. Click Upload<br>3. Select document type<br>4. Upload file<br>5. View datatable | Document should upload. Datatable should update showing: Type, Mandatory (Yes/No), Uploaded (Yes/No), Uploaded By, Date, Preview button |
| dynamicUploadDocument | **TC155**: Preview uploaded file | 1. Upload PDF document<br>2. Click preview button in datatable<br>3. Modal opens | Should display PDF in iframe modal |
| dynamicUploadDocument | **TC156**: Preview image file | 1. Upload PNG/JPG<br>2. Click preview | Should display image in modal |
| dynamicUploadDocument | **TC157**: Mandatory vs non-mandatory display | 1. View documents datatable<br>2. Check Mandatory column | Should correctly show "Yes" for mandatory doc types from rule, "No" for optional |
| dynamicUploadDocument | **TC158**: Checklist auto-update | 1. Rule has 3 mandatory docs<br>2. Upload 2 docs<br>3. Check checkbox<br>4. Upload 3rd doc<br>5. Check checkbox | Checkbox should remain FALSE until all mandatory uploaded, then auto-update to TRUE |
| uploadDocument | **TC159**: Legacy upload component (if used) | Similar tests to dynamicUploadDocument | Should function similarly to dynamicUploadDocument |
| flowToastMessage | **TC160**: Blacklisted account warning | 1. Create account with Status = "Blacklisted"<br>2. Create lead linked to this account<br>3. Open lead record with component | Should display warning toast: "This Lead's Account is blacklisted!" |
| dynamicFieldSelector | **TC161**: Edit condition field dynamically | 1. Open Business Unit Mapping Condition record<br>2. Component loads fields from parent rule's Target Object<br>3. Select field, operator, value<br>4. Save | Should update condition record with selected field, operator, value |

---

## Security and Permission Test Cases

| Test Scenario | Test Steps | Expected Result |
|---------------|------------|-----------------|
| **TC162**: Transfer Lead permission | 1. Login as user without Transfer Lead permission<br>2. Try to change lead owner | Should not allow ownership change |
| **TC163**: Create Account permission restriction | 1. Login as non-admin<br>2. Try to create account manually (not via lead conversion) | Should not have access. Accounts only created via lead conversion |
| **TC164**: Transfer Opportunity permission | 1. Login as user without permission<br>2. Try to change opportunity owner | Should not allow change |
| **TC165**: Report folder access by role | 1. Login as Sales Coordinator<br>2. Check visible reports<br>3. Login as Bid Manager<br>4. Check visible reports<br>5. Login as GM<br>6. Check visible reports | Each role should see appropriate reports based on permissions |
| **TC166**: Blacklist account permission | 1. Login as non-credit control user<br>2. Try to change Account Status to "Blacklisted" | Field should be read-only. Only users with special permission set can modify |

---

## Apex Test Class Coverage Summary

| Test Class | Coverage Target | Key Test Methods | Purpose |
|------------|-----------------|------------------|---------|
| BusinessUnitMappingRuleTriggerTest | BusinessUnitMappingRuleTrigger, BusinessUnitMappingRuleTriggerHandler, BusinessUnitMappingRuleTriggerHelper | testRuleInsert_ReevaluatesLeads, testRuleUpdate_VerticalHeadChange_ReevaluatesLeads, testRuleDelete_ReevaluatesLeads, testAfterUndelete_ReevaluatesLeads | Tests trigger for all DML operations (insert, update, delete, undelete) and verifies batch job enqueuing |
| CustomLeadConverterTest | CustomLeadConverter | testConvertLead_EnergyCluster_WithVerticalHead, testConvertLead_TechnologyCluster_ManualOwner, testConvertLead_DmlExceptionFailure_CatchesError | Tests lead conversion logic for different clusters and error handling |
| DocumentRuleControllerTest | DocumentRuleController | testInsertRecords_HappyPath, testInsertRecords_ExceptionPath, testGetFieldsForObject, testGetPicklistOptions, testGetFieldInfo | Tests document rule creation and metadata retrieval |
| DocumentRuleEvaluatorTest | DocumentRuleEvaluator | testAccountWithNoConditions, testAllLogic_Pass, testAnyLogic_Pass, testCustomFormulaLogic_Invalid, testBlankAndBooleanConditions, testNumericAndStringComparisons | Tests rule evaluation logic for different condition types and operators |
| DocumentUploadControllerTest | DocumentUploadController | testGetObjectName, testUploadAndGetUploadedDocTypes, testUpdateChecklistFlag | Tests document upload and checklist update functionality |
| DocumentValidationHelperTest | DocumentValidationHelper | testValidateDocuments_NoActiveRules, testValidateDocuments_CheckboxTrue, testValidateDocuments_AllDocumentsUploaded, testValidateDocuments_MissingMandatoryDocument | Tests document validation before status changes |
| ContentDocumentHandlerTest | ContentDocumentHandler | testNonSiteInspection_createsAccount_deletesOpp, testSiteInspection_deletesAccount_keepsOpp_latestWins, testIgnoresNonOpportunityAndOppWithoutAccount | Tests document routing logic between Opportunity and Account |
| LeadTriggerTest | LeadTrigger, LeadTriggerHandler, LeadTriggerHelper | testNewLead_RuleMatches_HeadAssigned, testUpdateLead_BUChanges_NewHeadAssigned, testUpdateLead_BUChangesToNoRule_HeadCleared, testComplexLogic_AndAndParentheses_Success | Tests lead trigger for vertical head assignment based on rules |
| OpportunityTriggerHelperTest | OpportunityTrigger, OpportunityTriggerHandler, OpportunityTriggerHelper | testOwnershipRuleAddsOwnerAsOpportunityTeamMember, testTeamMembersRuleAddsRelatedUser_ReadWrite_mapsToEdit, testDuplicatePrevention_noDuplicateCreatedIfExistingTeamMember | Tests opportunity team member auto-assignment |
| TradeLicenseExpirySchedulerTest | TradeLicenseExpiryScheduler | testFlagSetAndClearAndNoErrorsOnNotifications | Tests scheduler for trade license expiry notifications and flag management |
| HttpCalloutServiceTest | HttpCalloutService | testSendRequest_Success, testSendAuthenticatedRequest_Success, testGetAuthConfig_Valid | Tests HTTP callout service for API integrations |
| HandleErrorLogTest | HandleErrorLog | testGetIntegrationErrorLog_WithException, testGetExceptionErrorLog_WithException, testGetInfoLog, testLimitLength | Tests error logging functionality |
| FieldPickerControllerTest | FieldPickerController | testGetFields_ValidObject_ReturnsSortedList, testGetFields_InvalidObject_ThrowsException, testGetFields_NullAndBlankInput_ReturnsEmptyList | Tests dynamic field picker for condition builder |

---

## Data Validation and Business Rules Test Cases

| BRN/Feature | Test Scenario | Test Steps | Expected Result |
|-------------|---------------|------------|-----------------|
| Duplicate Rules | **TC167**: Lead to Account duplicate matching | 1. Create Account with VAT = "VAT123"<br>2. Create Lead with same VAT<br>3. Set Existing Customer = "No"<br>4. Convert | Should detect duplicate and show potential matches |
| Validation Rules | **TC168**: Prevent blacklisted account lead creation | 1. Account with Status = "Blacklisted"<br>2. Try to create/tag lead to this account | Should show error: "Cannot create lead: account is blacklisted" |
| Formula Fields | **TC169**: Lead Unique ID formula on Opportunity | 1. Convert lead<br>2. Check opportunity<br>3. Verify Lead_Unique_ID__c formula field | Should display lead's unique identifier |
| Workflow/Flow | **TC170**: Status auto-update on owner change | 1. Lead Status = "New"<br>2. Change owner<br>3. Check status | Should auto-update to "Assigned" |
| Workflow/Flow | **TC171**: Send notification on lead assignment | 1. Assign lead to user<br>2. Check user's notifications | Flow should send bell notification |
| Record Types | **TC172**: Record type based BU picklist values | 1. Create lead with RecordType = EEIC<br>2. Check BU picklist<br>3. Create with RecordType = GCGE<br>4. Check BU picklist | BU values should be filtered based on RecordType |
| Dynamic Forms | **TC173**: Account lookup visibility | 1. Set Existing Customer = "No"<br>2. Observe form<br>3. Set to "Yes" | Account lookup should toggle visibility |

---

## Integration Points Test Cases

| Integration | Test Scenario | Test Steps | Expected Result |
|-------------|---------------|------------|-----------------|
| Etihad Bureau API | **TC174**: Credit score fetch for UAE accounts | 1. Create account in UAE<br>2. Customer Type ≠ Government/Semi-Government<br>3. Enter Trade License Number<br>4. API callout triggered | Should fetch credit score and populate External_Credit_Score__c field |
| Idenfo AML API | **TC175**: AML check API integration | 1. Account requiring AML check<br>2. Trigger AML verification<br>3. Authenticate with Idenfo<br>4. Create customer in Idenfo | Should successfully authenticate, create customer, receive Idenfo_Id__c, log response in AML_API_Response__c |
| ERP Integration (OIC) | **TC176**: Account sync to ERP | 1. Account marked "Sync to ERP" = TRUE<br>2. OIC job runs<br>3. ERP creates account<br>4. Returns ERP ID | Account should receive ERP_ID__c. Status should update |
| ERP Integration (OIC) | **TC177**: Location sync to ERP | 1. Create Location with Status = "Active"<br>2. OIC picks up record<br>3. ERP creates location<br>4. Returns ERP Location ID | Location should receive ERP_Location_ID__c. ERP_Ready__c should set to TRUE |

---

## End-to-End Test Scenarios

### E2E-001: Complete Lead to Opportunity Lifecycle (Energy Cluster)

**Preconditions:**
- Business Unit Mapping Rule configured for "Automation" BU
- Document Rules configured for Lead
- Approval Process configured

**Test Steps:**
1. Login as Sales Coordinator
2. Create new Lead:
   - Company: "Acme Corp"
   - Lead Source: "Walk-in"
   - Business Unit: "BU-E3 : Engineering and Contracting Supplies (Trading)"
   - Existing Customer: "No"
   - Fill all mandatory fields
   - Enter VAT/TRN
3. Verify Vertical Head auto-populated
4. Save lead (Status = "New")
5. Upload required documents:
   - Scope of Work
   - Trade License
   - VAT Certificate
6. Mark documents completed checkbox
7. Submit for Approval
8. Login as Bid Manager → Approve
9. Login as General Manager → Approve (if two-level)
10. Login as Sales Coordinator
11. Click Convert button
12. Verify conversion success

**Expected Results:**
- Lead should be created with Status = "New"
- Vertical Head should auto-populate based on BU
- Documents should upload and tag correctly
- Approval process should route correctly
- Final approval should set Status = "Approved"
- Conversion should create:
  - New Account (with details from lead)
  - Contact (primary contact from lead)
  - Opportunity (owned by Vertical Head)
- Documents should route: non-site to Account, site inspection to Opportunity
- Lead Status should update to "Converted"
- All history should be tracked

---

### E2E-002: Opportunity Creation with Team Member Auto-Assignment

**Preconditions:**
- Business Unit Mapping Rule for Opportunity with Purpose = "Team Members"
- Related Users configured with roles and access levels

**Test Steps:**
1. Create Opportunity with Business Unit matching rule
2. Save opportunity
3. Check Opportunity Team Members related list
4. Verify each team member has correct:
   - User
   - Role (from TeamMemberRole__c)
   - Access Level (Read → "Read", Read/Write → "Edit")
5. Verify no duplicates if member already exists
6. Check audit trail

**Expected Results:**
- Team members from rule's Related_Users__r should be auto-added
- Access levels should map correctly
- Duplicate prevention should work
- All actions logged

---

### E2E-003: Account Onboarding with Trade License Tracking

**Preconditions:**
- Scheduled job configured for TradeLicenseExpiryScheduler
- Email templates configured
- Custom notification type configured

**Test Steps:**
1. Create Account via lead conversion
2. Set Trade License Expiry Date = 30 days from today
3. Complete onboarding checklist:
   - Enter bank details
   - Upload documents
   - Add trade references (min 2)
   - Etihad Bureau fetches credit score (for UAE)
4. Submit for onboarding approval
5. Credit/Finance team approves
6. Status = "Onboarded Successfully"
7. Run scheduler on day -30, -15, -5 from expiry
8. Verify notifications sent
9. Let account expire
10. Run scheduler on day +1, +15, +30 after expiry
11. On day +31 (threshold), verify flag set
12. Try to create binding quote
13. Renew license (update expiry date)
14. Verify flag cleared

**Expected Results:**
- Onboarding checklist should track completion
- Scheduler should send reminders at correct intervals
- Both email and bell notifications should be sent
- Post-threshold flag should block binding quotes
- Renewal should clear restrictions

---

## Test Data Setup Requirements

### Users Required:
- Sales Coordinator
- Bid Manager
- General Manager
- Technical Proposal Manager
- Account Manager
- Credit Control Team Member
- Admin

### Master Data Required:
- Record Types: EEIC, GCGE, CNS, GCG
- Business Units for each cluster
- Business Unit Mapping Rules
- Document Rules with Conditions and Details
- Document Configuration metadata (Site Inspection doc types)
- Operating Units
- Country records (with classification and sensitive flags)
- Custom Notification Types
- Email Templates (Regret Letters, Onboarding)
- Approval Processes (Lead and Account)

### Sample Data:
- 5-10 Accounts (mix of new/existing, UAE/non-UAE, different customer types)
- 10-20 Leads (various statuses, BUs, customer types)
- 5-10 Opportunities (various stages)
- 5-10 Contacts
- Multiple ContentVersion records with different Document_Type__c values
- Trade References
- Locations and Account Sites

---

## Test Execution Notes

### Automated Testing:
- All Apex test classes should achieve minimum 75% code coverage (target: 85%+)
- Run all tests before deployment: `sfdx force:apex:test:run --codecoverage --resultformat human --wait 30`
- Review test results for failures

### Manual Testing:
- LWC components require manual UI testing
- Approval processes should be tested end-to-end
- Document upload and preview features need browser testing
- Notifications (email and bell) should be verified in real-time
- Reports and dashboards should be validated with real data

### Performance Testing:
- Bulk lead upload (100+ records)
- Bulk document upload
- Batch job execution (ReevaluateLeadsBatch)
- Scheduler execution with large datasets

### Integration Testing:
- Mock external APIs for unit tests
- Sandbox integration testing with real API endpoints
- Error handling for API failures
- Timeout scenarios

---

## Known Issues and Notes

Based on the CSV "Demo comments" and "Comments by BA" columns:

1. **Lead Unique ID Format**: Verify correct format display (BU prefix showing correctly)
2. **Lead Conversion**: Test with all document types uploaded
3. **Notification Access**: Ensure all users mentioned receive notifications
4. **Report Access**: Verify Bid Manager can access all assigned reports
5. **Document Upload**: Validate site inspection appears in upload options when marked as required
6. **Approval Notifications**: Ensure GM receives notification even when BM rejects
7. **Regret Letter Templates**: Templates need to be provided and tested
8. **Country Validation**: Auto-population of country fields to be thoroughly tested

---

## Test Execution Checklist

- [ ] All Apex test classes run successfully
- [ ] Code coverage > 75% (target: 85%+)
- [ ] All manual test scenarios executed
- [ ] Security and permissions validated
- [ ] Approval processes tested end-to-end
- [ ] Document upload/routing verified
- [ ] Notifications (email + bell) tested
- [ ] Reports accessible and showing correct data
- [ ] Integration points mocked and tested
- [ ] Error handling validated
- [ ] Audit trails verified
- [ ] Performance testing completed
- [ ] User acceptance testing (UAT) completed
- [ ] Test results documented

---

## Appendix: Test Data Templates

### Sample Lead Creation
```javascript
Lead testLead = new Lead();
testLead.LastName = 'Test Corp Lead';
testLead.Company = 'Test Corporation';
testLead.Business_Unit__c = 'BU-E3 : Engineering and Contracting Supplies (Trading)';
testLead.Status = 'New';
testLead.Existing_Customer__c = 'No';
testLead.Customer_Classification__c = 'Private Companies';
testLead.Primary_Contact_Name__c = 'John Doe';
testLead.Phone = '+971501234567';
testLead.Email = 'john@testcorp.com';
testLead.Scope_of_Work__c = 'Engineering supplies for project';
testLead.Street = '123 Sheikh Zayed Road';
testLead.City = 'Dubai';
testLead.Country = 'United Arab Emirates';
testLead.TRN_Number__c = 'TRN123456789';
testLead.VAT_Number__c = 'VAT987654321';
insert testLead;
```

### Sample Document Upload
```javascript
ContentVersion cv = new ContentVersion();
cv.Title = 'Trade License';
cv.PathOnClient = 'TradeLicense.pdf';
cv.VersionData = Blob.valueOf('Test Content');
cv.Document_Type__c = 'Trade License';
insert cv;

ContentDocumentLink cdl = new ContentDocumentLink();
cdl.ContentDocumentId = [SELECT ContentDocumentId FROM ContentVersion WHERE Id = :cv.Id].ContentDocumentId;
cdl.LinkedEntityId = leadId;
cdl.ShareType = 'V';
cdl.Visibility = 'AllUsers';
insert cdl;
```

### Sample Business Unit Mapping Rule
```javascript
Business_Unit_Mapping_Rule__c rule = new Business_Unit_Mapping_Rule__c();
rule.Name = 'Automation BU Rule';
rule.Target_Object__c = 'Lead';
rule.Business_Unit__c = 'BU-E3 : Engineering and Contracting Supplies (Trading)';
rule.Is_Active__c = true;
rule.Vertical_Head__c = verticalHeadUserId;
rule.Condition_Logic__c = 'ALL';
rule.Purpose__c = 'Ownership';
insert rule;
```

---

**Document Version:** 1.0  
**Last Updated:** October 13, 2025  
**Prepared By:** Development Team  
**Status:** Ready for Test Execution

