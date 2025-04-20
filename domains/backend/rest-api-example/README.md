# REST API Design Example

This example demonstrates best practices for designing RESTful APIs.

## API Design Principles

1. **Resource-Oriented**: Design around resources and their representations
2. **Standard HTTP Methods**: Use HTTP methods appropriately (GET, POST, PUT, DELETE)
3. **Consistent URL Structure**: Follow consistent hierarchical patterns
4. **Stateless**: Maintain stateless interactions between client and server
5. **Proper Status Codes**: Use appropriate HTTP status codes
6. **Content Negotiation**: Support multiple formats (JSON, XML)
7. **Versioning**: Implement API versioning strategy
8. **Pagination**: Include pagination for large collections
9. **Filtering & Sorting**: Allow resource filtering and sorting
10. **Error Handling**: Provide consistent error responses

## Example API Endpoints

Below is a simplified User and Order API:

### User Resource

```
# Get all users (with pagination)
GET /api/v1/users?page=1&limit=10

# Get a specific user
GET /api/v1/users/{id}

# Create a new user
POST /api/v1/users

# Update a user
PUT /api/v1/users/{id}

# Delete a user
DELETE /api/v1/users/{id}
```

### Order Resource

```
# Get all orders for a user
GET /api/v1/users/{id}/orders

# Get a specific order
GET /api/v1/orders/{id}

# Create a new order for a user
POST /api/v1/users/{id}/orders

# Update an order
PUT /api/v1/orders/{id}

# Delete an order
DELETE /api/v1/orders/{id}
```

## Example Request/Response

### Request
```http
GET /api/v1/users/123 HTTP/1.1
Host: api.example.com
Accept: application/json
Authorization: Bearer <token>
```

### Response
```http
HTTP/1.1 200 OK
Content-Type: application/json
Cache-Control: max-age=3600

{
  "id": "123",
  "name": "Jane Smith",
  "email": "jane.smith@example.com",
  "createdAt": "2024-10-15T08:30:00Z",
  "updatedAt": "2025-03-20T11:45:00Z",
  "_links": {
    "self": {"href": "/api/v1/users/123"},
    "orders": {"href": "/api/v1/users/123/orders"}
  }
}
```

## Error Response Example

```http
HTTP/1.1 404 Not Found
Content-Type: application/json

{
  "error": {
    "code": "resource_not_found",
    "message": "The requested user with ID '456' could not be found",
    "requestId": "abc-123-xyz"
  }
}
```

## Implementation Considerations

- **Authentication**: JWT, OAuth 2.0, or API Keys
- **Rate Limiting**: Prevent abuse with throttling
- **Documentation**: OpenAPI/Swagger specification
- **CORS**: Cross-Origin Resource Sharing configuration
- **Security Headers**: Implement proper security headers

## Code Examples

See the `src` directory for implementation examples:
- Express.js (Node.js) implementation
- Spring Boot (Java) implementation
