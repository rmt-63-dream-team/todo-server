# Task Management App - Software Design

## Overview

A RESTful API for managing tasks built with Express.js, Sequelize ORM, and PostgreSQL database.

## Technology Stack

- **Runtime**: Node.js
- **Framework**: Express.js
- **ORM**: Sequelize
- **Database**: PostgreSQL
- **Authentication**: JWT (JSON Web Tokens)
- **Password Hashing**: bcrypt

## Architecture

### Database Schema

#### Users Table

```sql
CREATE TABLE Users (
  id SERIAL PRIMARY KEY,
  username VARCHAR(255) UNIQUE NOT NULL,
  email VARCHAR(255) UNIQUE NOT NULL,
  password VARCHAR(255) NOT NULL,
  createdAt TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
  updatedAt TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);
```

#### Tasks Table

```sql
CREATE TABLE Tasks (
  id SERIAL PRIMARY KEY,
  title VARCHAR(255) NOT NULL,
  description TEXT,
  status VARCHAR(50) DEFAULT 'pending',
  priority VARCHAR(50) DEFAULT 'medium',
  dueDate DATE,
  userId INTEGER REFERENCES Users(id) ON DELETE CASCADE,
  createdAt TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
  updatedAt TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);
```

#### Categories Table (Optional)

```sql
CREATE TABLE Categories (
  id SERIAL PRIMARY KEY,
  name VARCHAR(255) NOT NULL,
  color VARCHAR(7),
  userId INTEGER REFERENCES Users(id) ON DELETE CASCADE,
  createdAt TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
  updatedAt TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);
```

#### TaskCategories Table (Junction Table)

```sql
CREATE TABLE TaskCategories (
  id SERIAL PRIMARY KEY,
  taskId INTEGER REFERENCES Tasks(id) ON DELETE CASCADE,
  categoryId INTEGER REFERENCES Categories(id) ON DELETE CASCADE,
  createdAt TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
  updatedAt TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);
```

## Project Structure

```
project-root/
├── config/
│   └── database.js          # Database configuration
├── controllers/
│   ├── UserController.js    # User-related logic
│   ├── TaskController.js    # Task-related logic
│   └── CategoryController.js # Category-related logic
├── helpers/
│   ├── bcrypt.js            # Password hashing utilities
│   └── jwt.js               # JWT token utilities
├── middlewares/
│   ├── authentication.js    # JWT verification
│   ├── authorization.js     # User authorization
│   └── errorHandler.js      # Global error handler
├── models/
│   ├── index.js             # Sequelize initialization
│   ├── user.js              # User model
│   ├── task.js              # Task model
│   ├── category.js          # Category model
│   └── taskcategory.js      # TaskCategory model
├── routes/
│   ├── index.js             # Main router
│   ├── users.js             # User routes
│   ├── tasks.js             # Task routes
│   └── categories.js        # Category routes
├── seeders/                 # Database seeders
├── migrations/              # Database migrations
├── .env                     # Environment variables
├── .gitignore
├── app.js                   # Express app setup
├── package.json
└── server.js                # Entry point
```

## Model Relationships

### Associations

- **User** `hasMany` **Task** (One user can have multiple tasks)
- **Task** `belongsTo` **User** (Each task belongs to one user)
- **Task** `belongsToMany` **Category** through **TaskCategories**
- **Category** `belongsToMany` **Task** through **TaskCategories**
- **Category** `belongsTo` **User** (Each category belongs to one user)

## Security Features

### Authentication Flow

1. User registers with username, email, and password
2. Password is hashed using bcrypt before storing
3. User logs in with email/username and password
4. Server validates credentials and generates JWT token
5. Client includes JWT token in Authorization header for protected routes
6. Server validates token on each protected request

### Authorization

- Users can only access and modify their own tasks and categories
- Middleware checks token validity and user ownership

## Status Codes & Error Handling

- **200**: Success
- **201**: Created
- **400**: Bad Request (validation errors)
- **401**: Unauthorized (invalid/missing token)
- **403**: Forbidden (insufficient permissions)
- **404**: Not Found
- **500**: Internal Server Error

## Environment Variables

```
PORT=3000
DATABASE_URL=postgresql://username:password@localhost:5432/taskmanagement
JWT_SECRET=your_jwt_secret_key
NODE_ENV=development
```

## Features

1. User registration and authentication
2. CRUD operations for tasks
3. Task filtering by status, priority, and due date
4. Task categorization
5. User-specific task management
6. Secure password storage
7. Token-based authentication
