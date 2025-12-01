# Kontist API Connector

The **Kontist API Connector** enables Microsoft Power Automate and Power Apps to interact with the Kontist Banking API using a unified GraphQL action. This connector allows automation of workflows related to **accounts**, **transactions**, **statements**, **transfers**, **subscriptions**, and **user information**.

The Kontist API provides a modern GraphQL interface offering structured banking and financial data tailored for self-employed professionals and freelancers.

---

## Publisher

**Kontist GmbH**  
API Documentation: [[https://kontist.dev/docs/](https://kontist.dev/docs/](https://kontist.dev/docs/](https://kontist.dev/docs/))

---

## Supported Features

The connector supports the following capabilities:

- Execute **GraphQL queries** against the Kontist API.
- Access to:
  - **Accounts** (balance, IBAN, metadata)
  - **Transactions** (filterable, detailed metadata)
  - **Statements** (PDF download URLs)
  - **Transfers** (initiate and confirm payments)
  - **Users** (profile and MFA-related endpoints)
  - **Subscriptions** (webhooks)
- OAuth2 authentication (Authorization Code + Refresh Token support).
- Fully generic GraphQL request body for maximum flexibility.
- JSON responses suitable for low-code / no-code automation.

---

## Prerequisites

To use the connector, you need:

- A **Kontist Business Account**.
- A registered **OAuth2 client** at the Kontist Developer Portal.
- OAuth2 configuration (Authorization Code flow recommended).
- Required OAuth2 scopes (see section **OAuth2 Scopes**).

More details:  
[[https://kontist.dev/docs/#authentication](https://kontist.dev/docs/#authentication](https://kontist.dev/docs/#authentication](https://kontist.dev/docs/#authentication))

---

## OAuth2 Scopes

The connector can request different scopes that define which API resources the flow may access. Below is a detailed explanation of each scope.

### `accounts`

Provides access to account-level data such as:

- Balance.
- IBAN, BIC.
- Account holder information.
- Account metadata.

**Use cases:** Display account information, synchronize account metadata, build dashboards, validate balances.

---

### `clients`

Allows management of OAuth2 clients:

- Create, update, delete OAuth clients.
- Retrieve OAuth client metadata.

**Important:** Not required for typical Power Automate workflows. This scope is only for administrative platforms.

---

### `offline`

Enables issuance of **refresh tokens**, allowing long-running flows to operate without re-authentication. Required for background and recurring Power Automate flows.

**Use cases:** Scheduled flows, always-on integrations.

---

### `statements`

Provides access to:

- Account statements.
- Statement metadata.
- Statement PDF download URLs.

**Use cases:** Automated document archiving (SharePoint, OneDrive), accounting integrations.

---

### `subscriptions`

Enables management of webhook subscriptions:

- Create, update, delete webhook endpoints.
- Subscribe to events such as new transactions or new statements.

**Use cases:** Event-driven automation, near real-time notifications.

---

### `transactions`

Provides access to full transaction information:

- Incoming and outgoing payments.
- Filters (date, type, amount).
- Metadata (fees, splits, MCC, description, purpose).

**Use cases:** Bookkeeping, reporting, cashflow automation, reconciliation.

---

### `transfers`

Enables creating and confirming SEPA transfers:

- `createTransfer` mutations.
- `confirmTransfer` (requires confirmation code for MFA).

**Use cases:** Automated payments, approval workflows, batch payouts.

**Note:** Initiating payments should only be exposed in controlled flows.

---

### `users`

Provides access to user information:

- Name and email.
- MFA-related endpoints.
- Profile metadata.

**Use cases:** Identifying the connected user, confirming transfers (MFA-based operations).

---

## Actions

The connector exposes one unified action, designed for maximum flexibility.

### Execute GraphQL Query

**HTTP method and path:** `POST /graphql`  
**operationId:** `fetchGraphQl`

This action allows sending any GraphQL query or mutation supported by the Kontist API.

It accepts the following request body fields:

- `query` (string, required) – the GraphQL query.
- `variables` (object, optional) – dynamic parameters.
- `operationName` (string, optional) – used when sending multiple operations in one document.

#### Request Body Schema (GraphQLRequest)

```json
{
  "query": "query Sample($first: Int!) { viewer { mainAccount { transactions(first: $first) { edges { node { id amount } } } } } }",
  "variables": {
    "first": 10
  }
}
```

#### Successful Response Schema (GraphQLResponse)

```json
{
  "data": {},
  "errors": []
}
```

#### Error Handling

- `400` – Invalid GraphQL query.
- `401` – Missing or invalid OAuth token.
- `500` – Internal server error.

#### Security

This action can be authorized with the following scopes, depending on the GraphQL operation being executed:

- `accounts`
- `users`
- `transactions`
- `transfers`
- `subscriptions`
- `statements`
- `offline`

This allows any supported Kontist GraphQL operation to be executed through a single Power Automate action.

---

## How to Use the Connector

1. Import the custom connector into your Power Platform environment.
2. Create a new connection using OAuth2 and grant the requested scopes.
3. Add the **Execute GraphQL Query** action to your flow or app.
4. Insert the GraphQL `query` string and optional `variables` and `operationName`.
5. Process the returned JSON in subsequent actions (for example, parse JSON, condition checks, or writing to SharePoint, Dataverse, or Excel).

---

## Known Limitations

- Only a single, generic GraphQL action is exposed.
- Users must construct valid GraphQL queries manually.
- Pagination requires handling GraphQL `cursor` fields in the query and in the subsequent requests.
- Transfers require MFA and additional confirmation steps in some cases.
- Rate limiting may apply for high-frequency calls.

---

## GraphQL Playground

The Kontist API provides an interactive **GraphQL Playground** that allows you to explore the schema, test queries, and validate mutations before using them inside Power Automate.

You can access the Playground here:  
**[https://kontist.dev/playground](https://kontist.dev/playground)**

The Playground enables you to:

- Write and execute GraphQL queries with autocomplete and syntax highlighting.
- Inspect the entire available schema, including queries, mutations, and input types.
- Test queries with variables exactly as they will be sent from the custom connector.
- Validate pagination logic (`first`, `after`, cursors) and filter parameters.
- Review error messages in the same structure returned by the API.
- Experiment with advanced fields (fees, splits, metadata) before building your flows.

Using the Playground is highly recommended during development, as it ensures that your queries are correct, complete, and optimized before integrating them into your Power Automate workflows.

---

## API Documentation

- Developer Docs: [[https://kontist.dev/docs/](https://kontist.dev/docs/](https://kontist.dev/docs/](https://kontist.dev/docs/))
- Authentication: [[https://kontist.dev/docs/#authentication](https://kontist.dev/docs/#authentication](https://kontist.dev/docs/#authentication](https://kontist.dev/docs/#authentication))
- GraphQL Explorer: [[https://kontist.dev/graphql](https://kontist.dev/graphql](https://kontist.dev/graphql](https://kontist.dev/graphql))
