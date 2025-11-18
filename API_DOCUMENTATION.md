# Task Management API Documentation

Base URL: `http://localhost:3000/api`

## Table of Contents

- [Authentication](#authentication)
- [Users](#users)
- [Tasks](#tasks)
- [Categories](#categories)

---

## Authentication

All protected endpoints require a JWT token in the Authorization header:

```
Authorization: Bearer <your_jwt_token>
```

---

## Users

### 1. Register User

Create a new user account.

**Endpoint**: `POST /users/register`

**Request Body**:

```json
{
  "username": "john_doe",
  "email": "john@example.com",
  "password": "securePassword123"
}
```

**Response** (201 Created):

```json
{
  "message": "User registered successfully",
  "user": {
    "id": 1,
    "username": "john_doe",
    "email": "john@example.com"
  }
}
```

**Error Responses**:

- `400`: Validation error (missing fields, invalid email, duplicate username/email)

---

### 2. Login User

Authenticate and receive JWT token.

**Endpoint**: `POST /users/login`

**Request Body**:

```json
{
  "email": "john@example.com",
  "password": "securePassword123"
}
```

**Response** (200 OK):

```json
{
  "message": "Login successful",
  "access_token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...",
  "user": {
    "id": 1,
    "username": "john_doe",
    "email": "john@example.com"
  }
}
```

**Error Responses**:

- `400`: Missing email or password
- `401`: Invalid credentials

---

### 3. Get User Profile

Get current user's profile information.

**Endpoint**: `GET /users/profile`

**Headers**: `Authorization: Bearer <token>`

**Response** (200 OK):

```json
{
  "id": 1,
  "username": "john_doe",
  "email": "john@example.com",
  "createdAt": "2025-11-18T10:30:00.000Z"
}
```

**Error Responses**:

- `401`: Unauthorized (invalid or missing token)

---

## Tasks

### 1. Create Task

Create a new task for the authenticated user.

**Endpoint**: `POST /tasks`

**Headers**: `Authorization: Bearer <token>`

**Request Body**:

```json
{
  "title": "Complete project documentation",
  "description": "Write comprehensive API documentation",
  "status": "pending",
  "priority": "high",
  "dueDate": "2025-12-01"
}
```

**Response** (201 Created):

```json
{
  "message": "Task created successfully",
  "task": {
    "id": 1,
    "title": "Complete project documentation",
    "description": "Write comprehensive API documentation",
    "status": "pending",
    "priority": "high",
    "dueDate": "2025-12-01",
    "userId": 1,
    "createdAt": "2025-11-18T10:30:00.000Z",
    "updatedAt": "2025-11-18T10:30:00.000Z"
  }
}
```

**Valid Values**:

- `status`: "pending", "in-progress", "completed"
- `priority`: "low", "medium", "high"

**Error Responses**:

- `400`: Validation error (missing title)
- `401`: Unauthorized

---

### 2. Get All Tasks

Retrieve all tasks for the authenticated user with optional filtering.

**Endpoint**: `GET /tasks`

**Headers**: `Authorization: Bearer <token>`

**Query Parameters** (optional):

- `status`: Filter by status (pending, in-progress, completed)
- `priority`: Filter by priority (low, medium, high)
- `sortBy`: Sort by field (dueDate, createdAt, priority)
- `order`: Sort order (ASC, DESC)

**Example**: `GET /tasks?status=pending&priority=high&sortBy=dueDate&order=ASC`

**Response** (200 OK):

```json
{
  "tasks": [
    {
      "id": 1,
      "title": "Complete project documentation",
      "description": "Write comprehensive API documentation",
      "status": "pending",
      "priority": "high",
      "dueDate": "2025-12-01",
      "userId": 1,
      "categories": [
        {
          "id": 1,
          "name": "Work",
          "color": "#FF5733"
        }
      ],
      "createdAt": "2025-11-18T10:30:00.000Z",
      "updatedAt": "2025-11-18T10:30:00.000Z"
    }
  ]
}
```

**Error Responses**:

- `401`: Unauthorized

---

### 3. Get Task by ID

Retrieve a specific task by its ID.

**Endpoint**: `GET /tasks/:id`

**Headers**: `Authorization: Bearer <token>`

**Response** (200 OK):

```json
{
  "id": 1,
  "title": "Complete project documentation",
  "description": "Write comprehensive API documentation",
  "status": "pending",
  "priority": "high",
  "dueDate": "2025-12-01",
  "userId": 1,
  "categories": [
    {
      "id": 1,
      "name": "Work",
      "color": "#FF5733"
    }
  ],
  "createdAt": "2025-11-18T10:30:00.000Z",
  "updatedAt": "2025-11-18T10:30:00.000Z"
}
```

**Error Responses**:

- `401`: Unauthorized
- `403`: Forbidden (task belongs to another user)
- `404`: Task not found

---

### 4. Update Task

Update an existing task.

**Endpoint**: `PUT /tasks/:id`

**Headers**: `Authorization: Bearer <token>`

**Request Body** (all fields optional):

```json
{
  "title": "Updated task title",
  "description": "Updated description",
  "status": "in-progress",
  "priority": "medium",
  "dueDate": "2025-12-15"
}
```

**Response** (200 OK):

```json
{
  "message": "Task updated successfully",
  "task": {
    "id": 1,
    "title": "Updated task title",
    "description": "Updated description",
    "status": "in-progress",
    "priority": "medium",
    "dueDate": "2025-12-15",
    "userId": 1,
    "createdAt": "2025-11-18T10:30:00.000Z",
    "updatedAt": "2025-11-18T11:00:00.000Z"
  }
}
```

**Error Responses**:

- `400`: Validation error
- `401`: Unauthorized
- `403`: Forbidden (task belongs to another user)
- `404`: Task not found

---

### 5. Delete Task

Delete a task.

**Endpoint**: `DELETE /tasks/:id`

**Headers**: `Authorization: Bearer <token>`

**Response** (200 OK):

```json
{
  "message": "Task deleted successfully"
}
```

**Error Responses**:

- `401`: Unauthorized
- `403`: Forbidden (task belongs to another user)
- `404`: Task not found

---

### 6. Update Task Status

Quickly update only the status of a task.

**Endpoint**: `PATCH /tasks/:id/status`

**Headers**: `Authorization: Bearer <token>`

**Request Body**:

```json
{
  "status": "completed"
}
```

**Response** (200 OK):

```json
{
  "message": "Task status updated successfully",
  "task": {
    "id": 1,
    "status": "completed",
    "updatedAt": "2025-11-18T11:00:00.000Z"
  }
}
```

**Error Responses**:

- `400`: Invalid status value
- `401`: Unauthorized
- `403`: Forbidden
- `404`: Task not found

---

## Categories

### 1. Create Category

Create a new category for organizing tasks.

**Endpoint**: `POST /categories`

**Headers**: `Authorization: Bearer <token>`

**Request Body**:

```json
{
  "name": "Work",
  "color": "#FF5733"
}
```

**Response** (201 Created):

```json
{
  "message": "Category created successfully",
  "category": {
    "id": 1,
    "name": "Work",
    "color": "#FF5733",
    "userId": 1,
    "createdAt": "2025-11-18T10:30:00.000Z",
    "updatedAt": "2025-11-18T10:30:00.000Z"
  }
}
```

**Error Responses**:

- `400`: Validation error (missing name)
- `401`: Unauthorized

---

### 2. Get All Categories

Retrieve all categories for the authenticated user.

**Endpoint**: `GET /categories`

**Headers**: `Authorization: Bearer <token>`

**Response** (200 OK):

```json
{
  "categories": [
    {
      "id": 1,
      "name": "Work",
      "color": "#FF5733",
      "userId": 1,
      "createdAt": "2025-11-18T10:30:00.000Z",
      "updatedAt": "2025-11-18T10:30:00.000Z"
    },
    {
      "id": 2,
      "name": "Personal",
      "color": "#33C4FF",
      "userId": 1,
      "createdAt": "2025-11-18T10:35:00.000Z",
      "updatedAt": "2025-11-18T10:35:00.000Z"
    }
  ]
}
```

**Error Responses**:

- `401`: Unauthorized

---

### 3. Update Category

Update an existing category.

**Endpoint**: `PUT /categories/:id`

**Headers**: `Authorization: Bearer <token>`

**Request Body**:

```json
{
  "name": "Work Projects",
  "color": "#FF0000"
}
```

**Response** (200 OK):

```json
{
  "message": "Category updated successfully",
  "category": {
    "id": 1,
    "name": "Work Projects",
    "color": "#FF0000",
    "userId": 1,
    "updatedAt": "2025-11-18T11:00:00.000Z"
  }
}
```

**Error Responses**:

- `400`: Validation error
- `401`: Unauthorized
- `403`: Forbidden (category belongs to another user)
- `404`: Category not found

---

### 4. Delete Category

Delete a category.

**Endpoint**: `DELETE /categories/:id`

**Headers**: `Authorization: Bearer <token>`

**Response** (200 OK):

```json
{
  "message": "Category deleted successfully"
}
```

**Error Responses**:

- `401`: Unauthorized
- `403`: Forbidden (category belongs to another user)
- `404`: Category not found

---

### 5. Assign Category to Task

Add a category to a task.

**Endpoint**: `POST /tasks/:taskId/categories/:categoryId`

**Headers**: `Authorization: Bearer <token>`

**Response** (201 Created):

```json
{
  "message": "Category assigned to task successfully"
}
```

**Error Responses**:

- `400`: Category already assigned to this task
- `401`: Unauthorized
- `403`: Forbidden (task or category belongs to another user)
- `404`: Task or category not found

---

### 6. Remove Category from Task

Remove a category from a task.

**Endpoint**: `DELETE /tasks/:taskId/categories/:categoryId`

**Headers**: `Authorization: Bearer <token>`

**Response** (200 OK):

```json
{
  "message": "Category removed from task successfully"
}
```

**Error Responses**:

- `401`: Unauthorized
- `403`: Forbidden
- `404`: Task, category, or association not found

---

## Error Response Format

All error responses follow this format:

```json
{
  "message": "Error description",
  "errors": ["Detailed error 1", "Detailed error 2"]
}
```

### Common HTTP Status Codes

- **200 OK**: Request succeeded
- **201 Created**: Resource created successfully
- **400 Bad Request**: Invalid input or validation error
- **401 Unauthorized**: Missing or invalid authentication token
- **403 Forbidden**: User doesn't have permission to access resource
- **404 Not Found**: Resource not found
- **500 Internal Server Error**: Server error

---

## Notes

- All timestamps are in ISO 8601 format
- Dates should be in YYYY-MM-DD format
- Color codes should be in hexadecimal format (#RRGGBB)
- Token expires after 24 hours (configurable)
