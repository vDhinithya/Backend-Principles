# 12. Mastering Databases with Postgres

### 1. The Role of Databases and DBMS
*   *Persistence:* The core purpose of a database is to store data on secondary storage (disk/SSD) so it survives application restarts. While RAM (used for caching like Redis) is fast, it is volatile and expensive. Disk storage offers cheap, massive capacity with a slight speed trade-off.
*   *DBMS (Database Management System):* A software layer (like Postgres) that efficiently organizes data and provides CRUD operations and integrity (data which is stored is valid/correctness), and security.
*   *Why not text files?* Parsing text files via application code is extremely slow, lacks schema enforcement (data integrity), and fails dangerously during concurrent access (multiple users writing at once).

### 2. Relational vs. Non-Relational (Why Postgres?)
*   *Relational (SQL):* Strict schema, highly structured tables/rows. Ensures **Data Integrity**. Ideal for systems where structure is predictable (e.g., CRM, E-commerce).
*   *Non-Relational (NoSQL - e.g., MongoDB):* Flexible schema, stores in collections as document. Ideal when data structure is unpredictable (e.g., Content Management Systems).
*   *Why Postgres is the standard:* It is open-source, highly reliable, heavily compliant with SQL standards (making migrations easy), and features **Native JSON/JSONB support**. You can dump unstructured JSON into a Postgres column, eliminating the need to adopt a NoSQL database just for dynamic data.

### 3. Postgres Data Types (Best Practices)
*   *Identifiers:* Use `serial` or `bigserial` for auto-incrementing integers, or `uuid` for globally unique, URL-safe identifiers.
*   *Numbers:*
    *   Use `decimal` or `numeric` when **accuracy is critical** (e.g., financial prices).
    *   Use `real` or `float` when **speed is critical** and minor floating-point inaccuracies are acceptable (e.g., scientific computations, sizing).
*   *Strings (The Golden Rule):* Avoid `char` (pads empty spaces) and `varchar(255)` (a legacy MySQL habit). Postgres officially recommends using `text` for all strings. Enforce length limitations in your backend application code, not the database, to avoid painful future database migrations.
*   *JSON:* Always use `jsonb` (JSON Binary) instead of `json`. Postgres serializes `jsonb` into a native format that is significantly faster to query and index.

### 4. Database Migrations and Seeding
*   *Migrations:* You must never manually modify a production database schema via a GUI. Use migration scripts (e.g., `1_create_users.sql`) processed by tools (like dbmate, Flyway, or Liquibase).
    *   **Up Migrations:** Apply changes (create tables, add indexes).
    *   **Down Migrations:** Revert those exact changes (drop tables) for safe rollbacks.
*   *Seeding:* The practice of injecting mock/test data into your development database via SQL scripts (often using CTEs - Common Table Expressions) so backend engineers can test APIs locally.

### 5. Schema Design and Relationships
*   *Enums:* Use custom Enum types (e.g., `active`, `archived`) instead of raw text. This pushes data validation to the database level and acts as self-documenting code for future developers.
*   *Primary Keys:* Implicitly enforce `UNIQUE` and `NOT NULL` constraints.
*   *Foreign Keys (Referential Integrity):* Protects data from corruption across tables.
    *   `ON DELETE RESTRICT`: Prevents deletion of a parent row if child rows exist (e.g., cannot delete a User if they own a Project).
    *   `ON DELETE CASCADE`: Automatically deletes all child rows when the parent is deleted.
*   *Relationship Modeling:*
    *   **One-to-One:** The primary key of the child table is also the foreign key referencing the parent (e.g., `users` and `user_profiles`).
    *   **One-to-Many:** The child table holds a foreign key to the parent (e.g., `projects` and `tasks`).
    *   **Many-to-Many:** Requires a **Linking Table** with a *Composite Primary Key* made of two foreign keys (e.g., `project_members` linking users and projects).

### 6. Security: Parameterized Queries
*   *The Threat:* If a backend constructs SQL by concatenating strings directly from an API payload, it is vulnerable to **SQL Injection** (e.g., a user passing `DROP TABLE users;`).
*   *The Solution:* Use parameterized queries (empty slots). The database driver explicitly treats the provided parameters strictly as strings/values, stripping them of any executable SQL power.

### 7. Dynamic Queries (List APIs)
When fetching lists (e.g., `GET /users`), the backend must dynamically construct the SQL query based on URL parameters:
*   *Filtering:* Use the `WHERE` clause combined with `ILIKE` for case-insensitive pattern matching.
*   *Sorting:* Use `ORDER BY {column} {ASC/DESC}`. Always set a sane default (e.g., `ORDER BY created_at DESC`).
*   *Pagination:* Use `LIMIT` (how many records to return) and `OFFSET` (how many records to skip).

### 8. Database Indexes (Performance Tuning)
*   *The Problem:* A standard `SELECT` forces the database to perform a **Sequential Scan**, checking every physical row on the disk one by one. This is disastrously slow at scale.
*   *The Solution:* An index acts like the index of a book—it is a highly optimized lookup table mapping a specific value directly to its physical disk location.
*   *When to Index:* Create indexes for columns frequently used in `WHERE` clauses, `JOIN` conditions (like foreign keys), and `ORDER BY` clauses.
*   *The Trade-off:* Indexes speed up reads (`SELECT`), but they slow down writes (`INSERT`/`UPDATE`) because the database must update the index lookup table every time data changes. Do not over-index.

### 9. Database Triggers
*   *Purpose:* Automate repetitive database actions natively.
*   *Example Use Case:* Instead of forcing the backend application code to manually pass an `updated_at` timestamp every time a `PATCH` request is made, create a database function and a **Trigger**. The trigger listens for any `UPDATE` on a row and automatically overrides the `updated_at` column with `now()`.
    postgres-database-notes.md
    Displaying postgres-database-notes.md.