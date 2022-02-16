## REST API Design
- For real-time projects, who will use your API?
- Also, how will they use your API?
- Design the API based on requirements

## API Design Process
1. Review API requirements
2. Identify main resource / entity
3. Use HTTP methods to assign action on resource

## Step 1: Review API Requirements
- Create a REST API for the
- Customer Relationship Management (CRM) system

- REST clients should be able to
    - Get a list of customers
    - Get a single customer by id
    - Add a new customer
    - Update a customer
    - Delete a customer

## Step 2: Identify main resource / entity
- To identify main resource / entity, look for the most prominent "noun"
- For our project, it is "customer"
- Convention is to use plural form of resource / entity: customers
`/api/customers`

## Step 3: Use HTTP methods to assign action on resource

|HTTP Method| CRUD Action|
|-----------|------------|
|POST| Create a new entity|
|GET|  Read a list of entities or single entity|
|PUT| Update an existing entity|
|DELETE| Delete an existing entity|

## CRUD Endpoint Examples
|HTTP Method| Endpoint |CRUD Action|
|-----------|----------|-----------|
|POST| /api/customers| Create a new customer|
|GET |/api/customers |Read a list of customers|
|GET |/api/customers/{customerId}| Read a single customer|
|PUT |/api/customers |Update an existing customer|
|DELETE| /api/customers/{customerId} |Delete an existing customer|

For POST and PUT, we will send customer data as JSON in request message body.

## Anti-Patterns
- DO NOT DO THIS… these are REST anti-patterns, bad practice
```
/api/customersList
/api/deleteCustomer
/api/addCustomer
/api/updateCustomer
```
- Don't include actions in the endpoint

## More API Examples

## PayPal
- PayPal Invoicing API
- https://developer.paypal.com/docs/api/invoicing/

- Create draft invoice 
POST `/v1/invoicing/invoices`

- List Invoices
GET `/v1/invoicing/invoices`

- Show invoice details 
GET `/v1/invoicing/invoices/{invoice_id}`

- Update invoice 
PUT `/v1/invoicing/invoices/{invoice_id}`

- Delete draft invoice
DELETE `/v1/invoicing/invoices/{invoice_id}`     

## GitHub
- GitHub Repositories API
- https://developer.github.com/v3/repos/#repositories

- Create a new repository 
POST `/user/repos`

- List your repositories
DELETE `/repos/:owner/:repo`

- Delete a repository
GET `/user/repos`

- Get a repository
GET `/repos/:owner/:repo`

## SalesForce REST API
- Industries REST API
- https://sforce.co/2J40ALH

- Retrieve All Individuals
GET `/services/apexrest/v1/individual/{individual_id}`
- Retrieve One Individual
GET `/services/apexrest/v1/individual/`

- Create an individual
POST `/services/apexrest/clinic01/v1/individual/`

- Update an individual
PUT `/services/apexrest/clinic01/v1/individual/`

