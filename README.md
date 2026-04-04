# Sahal Service Staff Offer API Documentation

> REST API for partner companies to verify employees and save SomGas loan adjustments.

**Base URL (Production)**
```
http://161.97.124.100:9009
```

**Authentication**
Send your API key with every request:
```
Authorization: api_nLu4b7EXKd5WbVshJ90815e8KkUuAUW7
```
All requests must use `Content-Type: application/json`.

---

## Endpoints

### 1) Check Employee Status
**POST** `/api/v1/employees/status`

Checks whether an employee exists and returns their current status.

**Headers**
```
Content-Type: application/json
Authorization: api_nLu4b7EXKd5WbVshJ90815e8KkUuAUW7
```

**Request (choose ONE identifier)**

```jsonc
// By mobile (local number without '+' and country code)
{ "mobile": "684222206" }
```

**Response — 200**
```json
{
    "ResultCode": 0,
    "Language": 0,
    "ReplyMessage": "Success",
    "data": [
        {
            "Status": "Normal",
            "EmpId": "SH87991",
            "Name": "Mohamed Mahad Farah"
        }
    ]
}
```
Where `status` ∈ `["Active","Inactive", "Temporary", "NotFound"]`.

**Errors**
- `400 Bad Request` – invalid input (must provide one identifier)
- `401 Unauthorized` – missing/invalid API key
- `404 Not Found` – no employee matched

**Example**
```bash
curl --location 'http://161.97.124.100:9009/api/v1/employees/status' \
--header 'Content-Type: application/json' \
--header 'Authorization: api_nLu4b7EXKd5WbVshJ90815e8KkUuAUW7' \
--data '{"mobile":"684222206"}'
```

---

### 2) Save SomGas Loan / Adjustment
**POST** `/api/v1/employees/debts/confirm`

Creates an adjustment record (e.g., loan deduction/payment) for an employee.

**Headers**
```
Content-Type: application/json
Authorization: api_nLu4b7EXKd5WbVshJ90815e8KkUuAUW7
```

**Request**
```json
{
    "Amount": 10.80,
    "Tell": "616998767",
    "ActionDate": "2026-01-27",
    "OrderId": "S69283",
    "Isconfirmed": 1
}
```


**Field notes**
- `Amount` — number (positive).  
- `ActionDate` — `YYYY-MM-DD`.  
- `OrderId` — unique request reference.  
- `Isconfirmed` — `1` (confirmed) or `0` (pending).

**Response — 200**
```json
{
    "ResultCode": 0,
    "Language": 0,
    "ReplyMessage": "Success",
    "data": null
}
```

**Errors**
- `400 Bad Request` – invalid input (e.g., non-positive amount) or duplicate OrderId
- `401 Unauthorized` – missing/invalid API key
- `404 Not Found` – employee not found
- `409 Conflict` – duplicate order or business rule violation (e.g., inactive employee)

**Error Response — 400 (Duplicate OrderId)**
```json
{
    "ResultCode": 2,
    "Language": 0,
    "ReplyMessage": "OrderId already exists",
    "data": null
}
```

**Example**
```bash
curl --location 'http://161.97.124.100:9009/api/v1/employees/debts/confirm' \
--header 'Content-Type: application/json' \
--header 'Authorization: api_nLu4b7EXKd5WbVshJ90815e8KkUuAUW7' \
--data '{
    "Amount": 10.80,
    "Tell": "616998767",
    "ActionDate": "2026-01-27",
    "OrderId": "S69283",
    "Isconfirmed": 1
}'
```

---