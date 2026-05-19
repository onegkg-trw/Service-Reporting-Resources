# Service Reporting

Considerations:
  - What compatibility do we want?
    - Android, iOS, Windows?, Web?
    - Alternative: Mobile friendly web app
      - Benefits:
        - Don't need to build out two frontends
        - Don't have to pay app store fees
        - Oneg is more familiar with web dev than with mobile dev
      - Downsides:
        - Marginally to significantly worse user experience than native apps on any given platform
        - Can't separate concerns. You probably don't need to export to word on mobile for example
  - Authorization: Who needs access and how do we ensure only they can get access
  - Database: How to store info
  - To Start:
    - Add/Edit description and times
    - Export to word (Need to figure out how to do that)
  - Additional potential features:
    - Edit all fields from desktop/web UI
    - Field validation/autofilling
    - LLM Autosummary of bulletpointed description?
      - Confidentiality concerns? Is this even something we want?
    - PM Report templates/autofilling

# Database
  - Flat JSON entries
    - Mongo
    - Proto schema below

  # Backend
  - Go, Java, C#, TS?
    - Go:
      - Great json serialization
      - Familiar with language
      - Less familiar with backend stuff in go
    - C#:
      - Less familiar with the language
      - Good practice for windows service work
      - Better language than Java
      - First class nullable types

# Frontend
  - Web: TS + React, React Native Web?
  - Desktop: C#, PyQT, React Native, Java,
  - Mobile React Native
