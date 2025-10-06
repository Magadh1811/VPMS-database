#  Vehicles Parking Management System — Database Schema

A robust **PostgreSQL schema** for managing parking operations, users, vehicles, and bookings.
Built for scalability and clean data relationships. The database is hosted and managed using **Supabase** for reliable cloud-based PostgreSQL infrastructure.

---

## 🧩 Key Features

*  **Secure authentication** with `pgcrypto`
*  **Role-based access control** (`user`, `admin`)
*  **Multi-vehicle support** per user
*  **Hierarchical parking management** — Lot → Level → Spot
*  **Smart booking system** — ensures one active booking per spot/vehicle
*  **Cascade deletes** to maintain data consistency
*  **Supabase integration** — fully cloud-managed PostgreSQL setup

---

##  Database Structure

```mermaid
erDiagram
    USERS ||--o{ VEHICLES : owns
    USERS ||--o{ BOOKINGS : makes
    VEHICLES ||--o{ BOOKINGS : linked
    PARKING_LOTS ||--o{ PARKING_LEVELS : contains
    PARKING_LEVELS ||--o{ PARKING_SPOTS : includes
    PARKING_SPOTS ||--o{ BOOKINGS : reserved_by

    USERS {
        uuid id
        text name
        text email
        text password_hash
        user_role role
    }

    VEHICLES {
        uuid id
        uuid user_id
        text plate
        text type
    }

    PARKING_LOTS {
        uuid id
        text name
    }

    PARKING_LEVELS {
        uuid id
        uuid lot_id
        text name
        int sort_order
    }

    PARKING_SPOTS {
        uuid id
        uuid lot_id
        uuid level_id
        text number
        spot_status status
    }

    BOOKINGS {
        uuid id
        uuid user_id
        uuid vehicle_id
        uuid spot_id
        timestamptz start_time
        timestamptz end_time
    }
```

---

## ⚙️ Setup Instructions

1. Create the extension:

   ```sql
   CREATE EXTENSION IF NOT EXISTS pgcrypto;
   ```

2. Run the schema:

   ```bash
   psql -U <username> -d <database> -f schema.sql
   ```

   Or if using **Supabase**, you can execute the script directly from the SQL editor in your project dashboard.

3. Verify enums:

   ```sql
   SELECT unnest(enum_range(NULL::user_role));
   SELECT unnest(enum_range(NULL::spot_status));
   ```

---

##  Enum Types

| Enum          | Values                                    |
| ------------- | ----------------------------------------- |
| `user_role`   | `'user'`, `'admin'`                       |
| `spot_status` | `'AVAILABLE'`, `'OCCUPIED'`, `'DISABLED'` |
