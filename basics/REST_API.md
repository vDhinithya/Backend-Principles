# 11. Complete REST API Design



### 1. The Core Philosophy of REST
*   **Representational:** Resources (data objects) can be represented in various formats depending on the client. For a backend API, this is almost always JSON, though XML or HTML are also valid depending on the context.
*   **State:** The current condition or attributes of a resource (e.g., a project's status and name) that get transferred between the client and server.
*   **Transfer:** The movement of these resource representations over the network using standard HTTP methods.

### 2. The Six Architectural Constraints of REST
To make the web scalable, REST relies on specific constraints. The most critical for backend engineers are:
*   **Client-Server Separation:** The client handles UI/UX routing, while the backend is strictly responsible for data storage, validations, and business logic.
*   **Statelessness:** The server must never store client context (like session state) between requests. Every single HTTP request must contain all the information (e.g., auth tokens, IDs) the server needs to process it. This is what allows microservices to scale horizontally across multiple instances.
*   **Layered System:** The architecture is hierarchical. A client does not know if it is hitting your Spring Boot server directly, an API Gateway, or a load balancer.

### 3. URL Structure and Resource Naming (The Golden Rule)
When designing routes, the path segment must logically represent the resource hierarchy.
*   **Anatomy of an API URL:** `https://api.example.com/v1/projects/zist?q=something#header` (Scheme -> Subdomain -> Versioning -> Resource / represent hierarchical layer -> after ? represent the query parameter -> #(fragment) direct to a particular section in webpage ).
*   **Always Use Plural Nouns:** Resources in the URL must always be plural, even when fetching a single entity.
    *   *Correct:* `GET /projects` (List) and `GET /projects/123` (Single).
    *   *Incorrect:* `GET /project/123`.
*   **Formatting Constraints:** Never use spaces or underscores in URLs. Use hyphens (kebab-case) for readability (e.g., `/projects/event-registration`).

### 4. Idempotency in HTTP Methods
*   **Definition:** An operation is *idempotent* if executing it multiple times produces the exact same side effect on the server as executing it once.
*   **Idempotent Methods:**
    *   **GET:** Fetching data 1,000 times does not mutate the database.
    *   **PUT/PATCH:** Updating a status to "ACTIVE" 1,000 times means the status just stays "ACTIVE".
    *   **DELETE:** Deleting an entity removes it once. Subsequent calls throw a `404 Not Found`, but the database state remains unchanged.
*   **Non-Idempotent Method:**
    *   **POST:** Calling a POST request 1,000 times with the exact same JSON payload will create 1,000 distinct rows in the database, each with a unique generated ID.

### 5. The Standard HTTP Methods (CRUD)
*   **GET (Read):** Fetches a list or a single resource. Never accepts a JSON body.
*   **POST (Create):** Adds a new entity. Typically, returns the newly created object and a `201 Created` status.
*   **PUT (Replace):** Replaces the *entire* representation of an entity. The payload must contain every single field, even ones that aren't changing.
*   **PATCH (Update Partial):** Updates only specific fields (e.g., just changing the status). This is generally preferred over PUT in modern applications.
*   **DELETE (Delete):** Removes an entity.

### 6. Handling Custom Actions (Non-CRUD)
Not all business logic fits cleanly into standard CRUD (e.g., archiving an organization, cloning a repository, or sending a notification email).
*   **The Standard Approach:** Use the **POST** method for any custom action. The REST specification leaves POST open-ended for this exact reason.
*   **Route Design:** Append the action verb to the end of the resource hierarchy.
    *   *Example:* `POST /organizations/123/archive` or `POST /projects/456/clone`.

### 7. Pagination, Sorting, and Filtering (List APIs)
A `GET` request for a list should never dump the entire database table. It must handle scale gracefully via query parameters.
*   **Pagination:** Limit the payload size using `?page=1&limit=10`. The response should include metadata (total records, current page, total pages) alongside the data array.
*   **Sorting:** Allow dynamic ordering using `?sortBy=createdAt&sortOrder=desc`.
*   **Filtering:** Pass field values in the query, e.g., `?status=ACTIVE`.
*   **Sane Defaults:** If the client sends a raw `GET /projects` request with no parameters, your controller must enforce default values automatically (e.g., default `page=1`, limit to `20` records, sort by `createdAt` descending).

### 8. Standard HTTP Status Codes
*   **200 OK:** Successful fetch, partial update, or custom action execution.
*   **201 Created:** Successful creation (POST) of a new database row.
*   **204 No Content:** Successful operation but no body to return (the standard response for a successful DELETE).
*   **404 Not Found:** The requested specific resource ID does not exist. *(Note: If a list query like `GET /projects?status=ARCHIVED` finds no results, do not throw a 404. Return a `200 OK` with an empty array `[]`).*

### 9. Best Practices for API Interface Design
*   **Design Before Coding:** Look at the frontend wireframes to identify your core resources (nouns). Map out the exact endpoints, request bodies, and responses in Swagger or Insomnia *before* writing the actual Spring Boot controllers or DTOs.
*   **Consistent JSON Payloads:** Always use `camelCase` for JSON keys (e.g., `organizationId`). Never use abbreviations (use `description`, not `desc`), as the client engineers lack the context you have.
*   **Global Consistency:** Once you establish a pattern for pagination metadata or URL structures, enforce it strictly across all microservices and endpoints.