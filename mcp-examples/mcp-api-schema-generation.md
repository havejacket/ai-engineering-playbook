## MCP Example: API Schema Generation (OpenAPI)

### What is Model-Context-Protocol (MCP)?

Model-Context-Protocol (MCP) is a framework developed by Anthropic that enables LLMs to interact with external tools and data sources. MCP allows AI models to:

1. **Access External Data:** Query databases, APIs, documents, and other information sources in real-time.
2. **Execute Code:** Run code snippets to process data or perform calculations.
3. **Use Specialized Tools:** Leverage purpose-built tools for specific tasks like data visualization or schema validation.
4. **Maintain Context:** Hold coherent, multi-turn conversations while incorporating external information.

### How MCP Benefits Developers

* **Enhanced Context:** Access to real-time data from databases, APIs, and documents provides more accurate and up-to-date information.
* **Reduced Manual Work:** Automatically extract and transform information from various sources instead of manual copying and reformatting.
* **Improved Accuracy:** Direct access to source data eliminates transcription errors and misinterpretations.
* **Specialized Capabilities:** Connect to domain-specific tools that extend the AI's capabilities beyond its training data.
* **Workflow Integration:** Seamlessly incorporate AI assistance into existing development workflows and toolchains.

### MCP for API Schema Generation

One powerful application of MCP is generating API schemas by connecting to existing data sources. The AI can:

* **Query Database Schemas:** Directly examine table structures to generate accurate data models.
* **Analyze Existing APIs:** Connect to running services to understand their endpoints and behavior.
* **Read Documentation:** Parse formal specifications or requirements documents.
* **Validate Output:** Use schema validation tools to ensure generated specifications are valid.

The core idea is to leverage an LLM's ability to understand and synthesize information from diverse data sources (the "Context" in MCP) according to a defined interaction pattern (the "Protocol") to create a formal API specification. This can significantly speed up the process of API design and documentation.

### Example Scenario

Let's say we have a simple natural language description of a new User service API.

**Input Context (Provided to the LLM):**

```text
Document: New User Service API Requirements
Author: Jane Doe
Date: 2024-05-10

We need to create a new User Service with the following capabilities:

1.  Create User: Allows creating a new user. Requires a username (string, mandatory) and email (string, mandatory, must be a valid email format). Returns the created user object with an assigned ID.

2.  Get User: Retrieves a user by their ID. The ID will be passed as a path parameter. Returns the user object (id, username, email) or a 404 if not found.

User Object fields:
- id (string, read-only, UUID format)
- username (string)
- email (string)
```

**Example Prompt:**

```plaintext
Given the API requirements document provided above, please generate a draft OpenAPI 3.0.0 schema in YAML format. 

The schema should include definitions for:
- Paths for creating a user (POST /users) and retrieving a user (GET /users/{id}).
- Request body for user creation, including required fields username and email with basic validation (e.g., email format).
- Response for successful user creation (201 status code), returning the user object.
- Path parameter for user ID in the Get User endpoint.
- Responses for Get User: successful retrieval (200 status code) with the user object, and user not found (404 status code).
- A schema definition for the User object (id, username, email).

Pay attention to marking fields as required, readOnly where appropriate, and specifying data types and formats (e.g., string, uuid, email).
```

**Expected Output Snippet (YAML - OpenAPI 3.0.0):**

```yaml
openapi: 3.0.0
info:
  title: User Service API
  version: v1.0.0
paths:
  /users:
    post:
      summary: Create a new user
      requestBody:
        required: true
        content:
          application/json:
            schema:
              type: object
              required:
                - username
                - email
              properties:
                username:
                  type: string
                  example: new_user
                email:
                  type: string
                  format: email
                  example: user@example.com
      responses:
        '201':
          description: User created successfully
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/User'
  /users/{id}:
    get:
      summary: Get user by ID
      parameters:
        - name: id
          in: path
          required: true
          description: ID of the user to retrieve
          schema:
            type: string
            format: uuid
      responses:
        '200':
          description: Successful retrieval
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/User'
        '404':
          description: User not found

components:
  schemas:
    User:
      type: object
      properties:
        id:
          type: string
          format: uuid
          readOnly: true
        username:
          type: string
        email:
          type: string
          format: email
```

### Benefits

*   **Speed:** Rapidly generate a baseline schema from unstructured or semi-structured requirements.
*   **Consistency:** Helps maintain a consistent style and structure if used across multiple API development efforts.
*   **Completeness Check:** The process of prompting and reviewing can help identify ambiguities or omissions in the initial requirements.
*   **Documentation Headstart:** Provides a solid foundation for formal API documentation.
