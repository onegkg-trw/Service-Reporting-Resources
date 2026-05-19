# Backend Plan

GET: `/api/reports` -> Returns a list of the reports and basic metadata for listing or querying a specific report
Response: 
```{jsonc}
[
  {
    "name": "report1",
    "company": "valo",
    "id": "{id}"
  },
  {
    "name": "report2",
    "company": "valo",
    "id": "{id}"
  },
  // ...
]
```
<!--
GET: `/api/company/{company}/reports` -> Returns the reports for a specific company
Response: is identical to above but more limited-->

GET: `/api/company/{company}/info` -> Returns metadata about a company
Response: 
```{jsonc}
{
  "name": "valo",
  "po_no": "09463", // Excluded if servicer is present
  "address": "1200 South Street, Lexington, MA XXXXX", // Excluded if servicer is present
  "servicer": { // Optional
    "name": "Thermo-Fisher",
    "po_no": "{po number}",
    "address": "500 Crescent Circle, Roxbury, MA XXXXX"
  }
}

GET: `/api/company/` -> Returns a list of companies
GET: `/api/reports/{id}` -> Returns full data of the report
GET: `/api/reports/{id}/time` -> Returns the time entries for a report

POST: `/api/reports/{id}/time` -> Add a time entry, entry can be incomplete
PATCH: `/api/reports/{id}/time/{time_id}/complete` -> Adds a clock out time to an incomplete entry
PATCH: `/api/reports/{id}/time/{time_id}` -> Updates one or more times from a complete or incomplete time entry

POST: `/api/reports/` -> Add a new report
PATCH: `/api/reports/{id}` -> Makes non-time updates to a report

DELETE: `/api/reports/{id}` -> Deletes the report
DELETE: `/api/reports/{id}/time/{time_id}` -> Deletes a time entry

GET: `/api/users/` -> Gets a list of users
POST: `/api/users` -> Adds a user
DELETE: `/api/users/{id}` -> Deletes a user
PATCH: `/api/users/{id}` -> Updates a user

Some OAuth stuff

GET: `/api/reports/{id}/export` -> Streams word document export of the report
