# Medsmonitoring
Owner: Lead Clinical Pharmacist
Status: MVP v1.0 – In Progress
Tech Stack: No-code MVP (Cursor + Lovable)
Date: May 2025

🧭 Purpose
To create a standalone, read-only digital tool that helps prescription clerks in general practice easily access structured, up-to-date medication monitoring guidance — curated by a lead pharmacist. The MVP will be used to validate search functionality, display logic, and ease of use for medication safety tasks.

👥 User Types
Role	Access Level
Clerk	Read-only access to medication and drug class data
Pharmacist	Full CRUD access (admin panel) for data management

🧱 Technical Stack
Component	Tool	Purpose
Frontend	Cursor	Drag-and-drop UI builder (low-code capable)
Backend/DB	Cursor DB	Built-in database collections
Workflow Logic	Cursor	Conditional UI + logic handling
Reminders (optional)	Lovable	Later: Add follow-up tasks or workflow checklists
Deployment	Cursor hosted	Standalone web app
Authentication	Basic login (Cursor default)	SSO/NHS login deferred

🗂 Database Schema
🔹 medications
Field	Type	Description
name	Text	Drug name (e.g., "Ramipril")
class	Relation	Links to drug_classes
indications	Multi-line Text	Clinical uses (e.g., HTN, CHF)
normal_dose	Text	Standard dose (e.g., "5mg OD")
max_dose	Text	Max safe dose
monitoring	Multi-line Text	Required bloods/tests
monitoring_freq	Text	e.g., "Every 6 months"
review_required	Boolean	Is formal review needed?
review_freq	Text	Frequency if required
notes	Multi-line Text	Optional safety notes
high_risk_flag	Boolean	Flag as high-risk (True/False)

🔹 drug_classes
Field	Type	Description
class_name	Text	Name (e.g., "ACE Inhibitors")
class_monitoring	Multi-line Text	Shared requirements
class_notes	Multi-line Text	Optional class-wide comments

🔎 Core Features – MVP Scope
🔍 Search Functionality
Search by drug name or drug class

Return drug name, class, and high-risk status

💊 Medication Detail Page
Full view of medication data

Sidebar showing linked drug class details

⚠️ High-Risk Flag Display
Visual red badge or banner if high_risk_flag = True

👨‍⚕️ Admin Panel (Pharmacist Only)
Table to add, edit, or delete medications and classes

View all data at a glance for fast input updates

🛠 Implementation Steps
1. Project Setup in Cursor
Create new Cursor app

Define medications and drug_classes collections

Add pages: Dashboard, Drug Detail, Admin Panel

2. UI Design
Dashboard: Search bar, results list

Drug Detail Page: Medication info + class sidebar

Admin Panel: Table view, add/edit/delete buttons

3. Logic Configuration
Link medications to class

Conditional display for high-risk drugs

Show class sidebar only if detail page is open

4. Data Entry & Testing
Input 5–10 test medications and classes

Test:

Search speed

Flag visibility

Navigation ease

5. User Feedback
Allow 1–2 clerks to test

Collect feedback on:

Clarity

Ease of use

Suggestions for labels/layout

✅ MVP Acceptance Criteria
Feature	Metric
Drug search	<10 seconds to locate 1 drug
Flag visibility	High-risk clearly displayed
Admin edits	<60 seconds to update a record
Clerk usability	No training required

⏳ Deferred / Future Features (Not MVP)
Versioning / audit logs

NICE & SPS data integration

NHS SSO login

Offline access via PWA

Monitoring task automation (Lovable)

These will be scoped in v2 and are intentionally excluded from MVP.

