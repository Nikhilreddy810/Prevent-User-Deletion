# Prevent User Deletion if Assigned to an Incident

## Problem Statement
In ServiceNow, users can be deleted even when they are assigned to incidents. This can cause loss of accountability and break incident tracking.

## Objective
To prevent deletion of a user who is still assigned to one or more incidents.

## Implementation
A Business Rule is created on the `sys_user` table.

- Table: sys_user  
- When: Before Delete  
- Condition: Check if user is assigned to any incident  

If the user is assigned, deletion is blocked and an error message is shown.

## Business Rule Script
```javascript
(function executeRule(current, previous) {
    var incGr = new GlideRecord('incident');
    incGr.addQuery('assigned_to', current.sys_id);
    incGr.setLimit(1);
    incGr.query();
    if (incGr.next()) {
        gs.addErrorMessage('This user cannot be deleted because they are assigned to one or more incidents.');
        current.setAbortAction(true);
    }
})(current, previous);
