# 🔐 Auto Assign – Password Reset (ServiceNow Flow Designer Project)

This project is a comprehensive **ServiceNow Flow Designer implementation** that automates **catalog task assignment**, **manager approval workflows**, and **ACL enforcement** for a *Password Reset* catalog item. It is designed for use in the **Global scope** (Yokohama release), integrating Flow Designer with catalog variables, ACLs, and custom task generation logic.

---

## 🚀 Day 2 Sprint Tasks Covered

### ✅ Task 0 – Log Metrics
- Sprint sheet updated to track Study mins, Build mins, blockers

### ✅ Task 1 – Tables & ACL Theory
- Studied Access Control Types, Evaluation Order, Table vs Field-level rules

### ✅ Task 2 – Identify Roles & Tables
- Looked up roles `itil`, `itil_admin`
- Verified Incident table and relevant fields (`assignment_group`, `urgency`)

### ✅ Task 3 – Build ACLs
- `incident.read`: visible to `itil` users
- `incident.assignment_group.write`: editable only by `itil_admin`

### ✅ Task 4 – Test & Validate ACLs
- Used impersonation to verify ACL enforcement
- Discovered `Deny Unless` is better for restricting fields in our use case

### ✅ Task 5 – Catalog Variables & UI Policy
- Created 4 variables:
  - `employee_id` (Single Line Text)
  - `system_affected` (Multiple Choice)
  - `urgency` (Single Choice)
  - `manager_approval_required` (Checkbox)
- UI Policy:
  - Shows approval checkbox **only if urgency = High**

### ✅ Task 6 – Add Variables & Policy
- Attached variables to Password Reset Request
- UI Policy successfully toggles manager approval based on urgency

### ✅ Task 7 – Flow: Lookup, Assign, Approve
- Triggered flow on `Catalog Item Created` (`sc_cat_item`)
- Retrieved submitted variables using "Get Catalog Variables"
- Looked up manager of Requested For user
- Conditional logic: if urgency is high, create approval for manager

### ✅ Task 8 – Full Flow Implementation
- Created a Catalog Task dynamically (`sc_task`) with:
  - Linked RITM
  - Dynamic short description
- Created `sysapproval_approver` record with:
  - `Approver = Manager`
  - `State = Requested`
  - `Approval for = Requested Item`
  - `Source Table = sc_req_item`
- Sent email to requester after task creation

---

## 🔐 ACLs Included

| Table     | Operation | Field              | Role         |
|-----------|-----------|--------------------|--------------|
| `incident` | `read`    | `*`                | `itil`       |
| `incident` | `write`   | `assignment_group` | `itil_admin` |

---

## 📦 Contents

- `Auto_Assign_Password_Reset.xml` – Update Set with:
  - ACLs
  - Flow Designer logic
  - UI Policies
  - Catalog Variable definitions
- Flow handles Catalog Task + Manager Approval with data-driven logic

---

## 🧪 Known Troubleshooting

| Issue | Solution |
|-------|----------|
| Flow test run didn’t show Approver | Test run context doesn’t bind RITM properly. Use live catalog submission |
| Approval didn’t appear in RITM | Fixed by using `Approval for` field in `sysapproval_approver`, not `Sys ID` |
| Catalog task not created | Solved by adding explicit `Create Record → sc_task` step |
| Field ACL not enforced at first | Switched from `Allow If` to `Deny Unless` to ensure restriction |

---

## 💻 How to Deploy

1. Import `Auto_Assign_Password_Reset.xml` via **System Update Sets > Retrieved**
2. Activate Flow manually
3. Associate Flow trigger with **Password Reset Request** catalog item
4. Submit a request as a user with a manager

---

✅ Built on: Yokohama | 🌐 Global Scope  
📅 Last Updated: 2025-04-23  
👨‍💻 Author: D H Hruday Mohan
