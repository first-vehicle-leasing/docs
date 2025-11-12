# FVL Enquiries API

This endpoint lets approved partners create an enquiry in FVL CRM.

Base URL: https://crm.fvl.co.uk

## Endpoint

POST `/api/enquiry`

Auth: Required — send your partner key in the header `X-Api-Key: <your-key>`.

Content-Type: application/json

Accept: application/json

## Authentication

Provide your issued API key in the request header:

```
X-Api-Key: YOUR_PARTNER_KEY
```

If the key is missing or invalid, the request will be rejected.

## Success response

HTTP 200 OK

Body:
```json
{ "message": "Enquiry created successfully" }
```

Note: This endpoint returns only a confirmation message. No enquiry payload is echoed back.

## Request Body

Send a JSON object with the fields below. Items marked Required must be provided.

| Field                              | Type               | Required | Rules / Limits                                             |
| ---------------------------------- | ------------------ | :------: | ---------------------------------------------------------- |
| `first_name`                       | string             |     ✓    | `max:60`                                                   |
| `last_name`                        | string             |     ✓    | `max:60`                                                   |
| `email`                            | string (email)     |     ✓    | valid email, `max:128`                                     |
| `landline`                         | string (GB phone)  |     *    | `nullable`, **required without `mobile`**                  |
| `mobile`                           | string (GB mobile) |     *    | `nullable`, **required without `landline`**                |
| `message`                          | string             |     ✓    | free text                                                  |
| `receive_marketing_communications` | boolean            |          | `nullable`, `boolean`                                      |
| `vehicle_type`                     | string             |          | `nullable`, `max:10`. **Default** (if omitted): `car`.     |
| `vehicle`                          | string             |          | `nullable`, `max:1024` (e.g., `BMW 320i M Sport 4dr Auto`) |
| `cap_code`                         | string             |          | `nullable`, `max:36`                                       |
| `cap_id`                           | number             |          | `nullable`, `numeric`                                      |
| `lease_type`                       | number             |          | `nullable`, `numeric`                                      |
| `initial_rental_in_month`          | number             |          | `nullable`, `numeric`, `min:0`                             |
| `initial_rental`                   | number             |          | `nullable`, `numeric`, `min:0`                             |
| `term`                             | number             |          | `nullable`, `numeric`, `min:0`                             |
| `annual_mileage`                   | number             |          | `nullable`, `numeric`, `min:0`                             |
| `include_vehicle_maintenance`      | boolean            |          | `nullable`, `boolean`                                      |
| `monthly_rental`                   | number             |          | `nullable`, `numeric`, `min:0`                             |
| `special_offer`                    | boolean            |          | `nullable`, `boolean`                                      |
| `utm_campaign`                     | string             |          | `nullable`, `max:1024`                                     |
| `utm_source`                       | string             |          | `nullable`, `max:1024`                                     |
| `utm_medium`                       | string             |          | `nullable`, `max:1024`                                     |
| `source_reference`                 | string             |          | `nullable`, `max:512`                                      |

### Phone rule: 

At least one of mobile or landline must be present.

- `mobile` must be a valid GB mobile number.
- `landline` must be a valid GB phone number.

	Numbers are validated using GB rules; include country code where possible (e.g., +44...).

## Example Requests

### Minimal valid example

```bash
curl -X POST "https://crm.fvl.co.uk/api/enquiry" \
  -H "X-Api-Key: YOUR_PARTNER_KEY" \
  -H "Content-Type: application/json" \
  -H "Accept: application/json" \
  -d '{
    "first_name": "Alex",
    "last_name": "Taylor",
    "email": "alex.taylor@example.com",
    "mobile": "+447700900123",
    "message": "Please call after 3pm."
  }'
```

### Typical full payload

```json
{
  "first_name": "Alex",
  "last_name": "Taylor",
  "email": "alex.taylor@example.com",
  "landline": "+441416001234",
  "mobile": "+447700900123",
  "message": "Interested in the 36-month offer.",
  "receive_marketing_communications": true,
  "vehicle_type": "car",
  "vehicle": "BMW 320i M Sport 4dr Auto",
  "cap_code": "CAP-3SER-320I",
  "cap_id": 123456,
  "lease_type": 1,
  "initial_rental_in_month": 9,
  "initial_rental": 2995.00,
  "term": 36,
  "annual_mileage": 10000,
  "include_vehicle_maintenance": false,
  "monthly_rental": 335.99,
  "special_offer": true,
  "utm_source": "google",
  "utm_medium": "cpc",
  "utm_campaign": "brand_q4",
  "source_reference": "LEAD-98765"
}
```

## Error Responses

### 422 Unprocessable Entity (validation)

```json
{
  "message": "The given data was invalid.",
  "errors": {
    "mobile": ["The mobile field is required when landline is not present."],
    "message": ["The message field is required."]
  }
}
```

### 401 / 403 Unauthorized

```json
{
  "message": "Unauthenticated."
}
```

### 415 Unsupported Media Type

```json
{
  "message": "Unsupported media type."
}
```

(Other standard 4xx/5xx errors may be returned as appropriate.)

## Notes

- The endpoint returns only a confirmation message on success.
- Phone numbers are normalised internally; providing the +44 country code improves accuracy.
- All numeric fields accept non-negative values (min:0) where specified.
- Text fields have maximum lengths as listed above—truncate before sending to avoid validation errors.
