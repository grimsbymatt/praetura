# .Net Developer Technical Task

**Estimated Time**: 2–4 hours  
**Tech Stack**: .NET 6/7/8, ASP.NET Core, EF Core  
**Database**: EF In-Memory or SQLite  

---

## Scenario

You are building part of a loan origination service. This system accepts loan applications via a REST API and processes them in the background based on eligibility rules. The service should be secure, cleanly structured, and designed with production readiness in mind.

---

## Core Requirements

### API Endpoints

#### `POST /loan-applications`
Accepts:
```json
{
  "name": "Alice Example",
  "email": "alice@example.com",
  "monthlyIncome": 3500,
  "requestedAmount": 8000,
  "termMonths": 36
}
```

Returns:
```json
{
  "id": "123e4567-e89b-12d3-a456-426614174000",
  "status": "Pending",
  "createdAt": "2025-05-27T10:30:00Z"
}
```

#### `GET /loan-applications/{id}`
Returns the full application record.

### Expected HTTP Status Codes
- `201 Created` – POST success  
- `200 OK` – GET success  
- `400 Bad Request` – Validation errors  
- `404 Not Found` – Invalid or missing application ID  

---

### Eligibility Rules
Loan applications are processed with the following rules:
- Monthly income must be at least £2,000  
- Requested amount must be no more than monthly income multiplied by 4  
- Term must be between 12 and 60 months  
- Email must be a valid format  

---

## Background Processing
Implement a background job using `BackgroundService` or `IHostedService`:
- Runs periodically (e.g. every 60 seconds)  
- Processes unreviewed loan applications  
- Applies eligibility rules and updates status to `"Approved"` or `"Rejected"`  
- Adds a `ReviewedAt` timestamp  

### Background Processing Notes:
- Use cancellation tokens appropriately  
- Handle exceptions gracefully — the service should not crash  
- Consider what happens if processing fails part-way through  

---

## Data Model

```csharp
LoanApplication
{
  Id (GUID),
  Name,
  Email,
  MonthlyIncome (decimal),
  RequestedAmount (decimal),
  TermMonths (int),
  Status (string),
  CreatedAt (DateTime),
  ReviewedAt (DateTime?)
}
```

---

## Optional Features (Prioritised)
We would like you to implement **at least one** of the following optional features. Choose based on what best demonstrates your strengths or experience.

### Idempotency
- Support a client-supplied `Idempotency-Key` header for POST requests  
- Prevent duplicate submissions from being stored or processed  

### Logging and Observability
- Use `ILogger` to log:  
  - Incoming requests  
  - Validation errors  
  - Background job activity  
  - Decision outcomes  
- Support and log `X-Correlation-ID` if provided  

### Role-Based Access Control (RBAC)
- Simulate roles using headers or middleware  
  - Admins: can view any application  
  - Users: can only view their own  

### Outbox Pattern (Simulated)
- On loan approval or rejection, write a message to an `Outbox` table  
- Simulate publishing via console or log output  
- Mark message as “published” once handled  

### Unit Testing
- Include at least one unit test for the eligibility logic  
- Optionally test validation or controller behaviour  

---

## Architecture Notes
Please include a short section in your README addressing:
- What would you change if this system had to handle **50,000 applications per day?**

---

## How to Submit
- GitHub repository (preferred), or a `.zip` file with the full source code  
- Include this `README.md`  
- Clearly label endpoints and provide instructions on how to run the project  
