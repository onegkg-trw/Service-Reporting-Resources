# Backend Plan

All POST/PATCH requests return the full created object, schema can be found in the backend schema folder and will match the associated get responses

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

GET: `/api/companies/{id}` -> Returns metadata about a company
Response:

```{jsonc}
{
  "id": "{id}",
  "name": "valo",
  "address": "1200 South Street, Lexington, MA XXXXX", // Excluded if servicer is present
  "servicer": { // Optional
    "name": "Thermo-Fisher",
    "address": "500 Crescent Circle, Roxbury, MA XXXXX"
  }
}
```

GET: `/api/companies` -> Returns a list of companies
Returns:

```{jsonc}
[
  {
    "name": "valo",
    "id": "{id}"
  },
  {
    "name": "biontech",
    "id": "{id}"
  },
  // ...
]
```

GET: `/api/reports/{id}` -> Returns full data of the report
Response:

```{jsonc}
{
  "id": "{id}",
  "name": "Report2",
  "our_reference": {
    "service_report_no": 135,
    "trw_contact": "Rita Vicaire",
    "po_no": "{po_no}",
    "billing_company": "valo",
    "address": "35 Science Drive, Lexington, MA 12345",
    "service_report": {
      "company": "valo",
      "contact_person_one": {
        "name": "Andrew Stephenson",
        "email": "asteph@valo.net",
        "phone": "+1 (xxx) xxx-xxxx"
      },
      "contact_person_two": { // Optional
        "name": "Abraham Crenshaw",
        "email": "abc@valo.net",
        "phone": "+1 (xxx) xxx-xxxx"
      },
      "service_call_information": {
        "purpose_of_call": "Fix the Tecan",
        "date_reported": "MM/DD/YYYY", // Double check formatting
        "system_down": true,
        "status_of_call": true
      },
      "instrument_configuration": {
        "manufacturer": "Tecan",
        "model": "EvoWare",
        "serial_number": "{serial_number}"
      },
      "service_dates": {
        "times": [
          {
            "id": "{id}",
            "is_travel": false,
            "is_onsite": true,
            "start": "HH:MM",
            "end": "HH:MM"
          },
          {
            "id": "{id}",
            "is_travel": true,
            "is_onsite": false,
            "start": "HH:MM",
            "end": "HH:MM",
          },
          // ...
        ],
        "service_detail": "- Fixed the arm\n- Fixed the software",
      }
    }
  }
}
```

GET: `/api/reports/{id}/times` -> Returns the time entries for a report
Response:
TODO: Update to fit updated TimeEntry schema
```{jsonc}
        [
          {
            "id": "{id}",
            "is_travel": false,
            "is_onsite": true,
            "start": "HH:MM",
            "end": "HH:MM"
          },
          {
            "id": "{id}",
            "is_travel": true,
            "is_onsite": false,
            "start": "HH:MM",
            "end": "HH:MM",
          },
          // ...
```

POST: `/api/reports/{id}/times` -> Add a time entry, entry can be incomplete
Request:

TODO: Update to match updated schema
```{jsonc}
{
  "is_travel": true, // Optional
  "is_onsite": false, // Optional
  "start": "HH:MM",
  "end": "HH:MM", // Optional
  "description": "what did you do" // Optional
}
```

PATCH: `/api/reports/{id}/times/{time_id}` -> Updates one or more times from a complete or incomplete time entry
Request:

```{jsonc}
{
  "is_travel": true, // Optional
  "is_onsite": false, // Optional
  "start": "HH:MM", // Optional
  "end": "HH:MM", // Optional
  "description": "what did you do" // Optional
}
```

POST: `/api/reports/` -> Add a new report
Request:
TODO: Update to match modern schema
```{jsonc}
// All fields are optional unless explicitly stated otherwise
// Some unlisted fields will be autofilled based on company name, these can be modified with a subsequent PATCH request
// Fields not listed here should not be included
{
  "our_reference": {
    "trw_contact": "Rita Vicaire",
  },
  "service_report": {
    "company": "Valo", // Required
    "contact_person_one": {
      "name": "Andrew Stephenson",
      "email": "asteph@valo.net",
      "phone": "+1 (xxx) xxx-xxxx",
    },
    "contact_person_two": {
      // Optional
      "name": "Abraham Crenshaw",
      "email": "abc@valo.net",
      "phone": "+1 (xxx) xxx-xxxx",
    },
    "service_call_information": {
      "purpose_of_call": "Fix the Tecan",
      "date_reported": "MM/DD/YYYY", // Double check formatting
      "system_down": true,
      "status_of_call": true,
    },
    "instrument_configuration": {
      "manufacturer": "Tecan",
      "model": "EvoWare",
      "serial_number": "{serial_number}",
    },
    "service_detail": "- Fixed the arm\n- Fixed the software",
  },
}
```

PATCH: `/api/reports/{id}` -> Makes non-time updates to a report
Request
```{jsonc}
// All fields are optional
{
  "name": "Report2",
  "our_reference": {
    "trw_contact": "Rita Vicaire",
    "po_no": "{po_no}",
    "billing_company": "valo",
    "address": "35 Science Drive, Lexington, MA 12345",
  },
  "service_report": {
    "company": "valo",
    "contact_person_one": {
      "name": "Andrew Stephenson",
      "email": "asteph@valo.net",
      "phone": "+1 (xxx) xxx-xxxx",
    },
    "contact_person_two": {
      // Optional
      "name": "Abraham Crenshaw",
      "email": "abc@valo.net",
      "phone": "+1 (xxx) xxx-xxxx",
    },
    "service_call_information": {
      "purpose_of_call": "Fix the Tecan",
      "date_reported": "MM/DD/YYYY", // Double check formatting
      "system_down": true,
      "status_of_call": true,
    },
    "instrument_configuration": {
      "manufacturer": "Tecan",
      "model": "EvoWare",
      "serial_number": "{serial_number}",
    },
    "service_detail": "- Fixed the arm\n- Fixed the software",
  },
}
```
Responds with the full updated object json

DELETE: `/api/reports/{id}` -> Deletes the report
Responds with `204 No Content` and no body

DELETE: `/api/reports/{id}/times/{time_id}` -> Deletes a time entry
Responds with `204 No Content` and no body

GET: `/api/users/` -> Gets a list of users
Response:
```{jsonc}
[
  {
    "name": "Rita Vicaire",
    "id": "{id}"
  },
  {
    "name": "Hamza El Ousrouti",
    "id": "{id}"
  },
  // ...
]
```

GET: `/api/users/{id}` -> Gets full data about the user

PATCH: `/api/users/{id}` -> Updates the User

POST: `/api/users` -> Adds a user
Request: 
TODO: Figure out user schema based on how oauth works
```{jsonc}
{
  "google_id": "108234567890123456789",
  "email": "rita@trw.com",
  "name": "Rita Vicaire",
  "access_token": "ya29.a0AfH6...",
  "refresh_token": "1//0gLd...",
  "token_expiry": "2026-05-20T14:30:00Z"
}
```
Response:
```{jsonc}
{
  "_id": "664f1a2b3c4d5e6f7a8b9c0d",
  "google_id": "108234567890123456789",
  "email": "rita@robotwhispererllc.com",
  "name": "Rita Vicaire",
  "created_at": "2026-05-20T13:00:00Z",
  "last_login": "2026-05-20T13:00:00Z"
}
```

DELETE: `/api/users/{id}` -> Deletes a user
Responds with `204 No Content` and an empty body

GET: `/api/reports/{id}/export` -> Streams word document export of the report
