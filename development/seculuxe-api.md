# 🔐 Laser Engraver Authentication & UUID Workflow

This document explains how laser engraver software authenticates with the Seculuxe API, how UUIDs are generated and managed, and how to interact with the API endpoints for creating and retrieving UUIDs.

---

## 1. 🧾 Overview

Each laser engraver acts as an **authenticated Supabase user account**. This gives every laser its own identity and access control, allowing us to:

- Securely identify which laser created a marking
- Prevent unauthorized submissions
- Maintain clean audit logs
- Associate lasers with their (human) owners (when relevant)

---

## 2. 🔐 Authentication Flow

### 🔁 Step-by-step process:

1. **Laser logs in** using a dedicated Supabase Auth account (`email + password`)
2. On successful login, the laser receives:
   - A short-lived **JWT access token**
   - A longer-lived **refresh token**
3. The laser stores these tokens locally (on disk or in memory)
4. The JWT is sent in the `Authorization` header for all API requests
5. When the JWT expires, the laser can refresh the session using the refresh token

### 🔑 Example (Node.js with `@supabase/supabase-js`):

```ts
const supabase = createClient(SUPABASE_URL, SUPABASE_ANON_KEY);

const { data, error } = await supabase.auth.signInWithPassword({
  email: "laser-001@seculuxe.com",
  password: "your-secret-password"
});

const accessToken = data.session.access_token;
```

---

## 3. ⚙️ UUID Generation

Each item to be engraved must have a **globally unique UUID**. This can be generated in one of two ways:

### Option A: Use Seculuxe API

Send a request to our helper endpoint to generate one:

```http
GET /uuid/generate
Authorization: Bearer <access-token>
```

Response:
```json
{ "uuid": "a12ef1ab-567c-4cf1-b888-5e321cb9d203" }
```

### Option B: Generate it locally

You may also use a UUID generator built into your software stack, e.g.:

- Node.js: `crypto.randomUUID()`
- Python: `uuid.uuid4()`
- C++: platform-specific libraries

---

## 4. 📤 Registering a UUID with the API

Once a UUID has been generated and engraved, it should be **registered** in the database via the API.

### Endpoint

```http
POST /register-uuid
Authorization: Bearer <access-token>
Content-Type: application/json
```

### Request body

```json
{
  "uuid": "a12ef1ab-567c-4cf1-b888-5e321cb9d203",
  "label": "Bottle XYZ",
}
```

### Response

```json
{
  "data": {
    "id": "a12ef1ab-567c-4cf1-b888-5e321cb9d203",
    "label": "Bottle XYZ",
    "created_at": "2025-11-03T12:00:00Z"
  }
}
```

---

## 5. 📥 Retrieving UUIDs (optional)

To retrieve UUIDs registered by the laser:

```http
GET /uuid/list
Authorization: Bearer <access-token>
```

Optional filters may be added in the future (e.g. by label or date).

---

## 6. 🔒 Security Notes

- The API requires authentication for all endpoints.
- Lasers can **only access UUIDs they created**.
- Tokens should be stored securely on the device.
- If a device is lost or compromised, revoke the credentials in the Seculuxe admin panel. (TBD)

---

## 7. 📌 Additional Notes

- Laser devices may be linked to human owners via the `laser_owners` table (not required for registration).
- Rate limiting and attestation may be added in future versions.

