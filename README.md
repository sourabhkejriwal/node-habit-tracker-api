# Habit Tracker API Node JS

## Overview

A habit tracking API built with Node.js, Express, PostgreSQL, and Drizzle ORM.

## Tech Stack

- **Runtime**: Node.js with TypeScript
- **Framework**: Express.js
- **Database**: PostgreSQL
- **ORM**: Drizzle ORM
- **Authentication**: JWT (JSON Web Tokens)
- **Password Hashing**: bcrypt
- **Testing**: Jest & Supertest
- **Security**: Helmet & CORS
- **CI/CD**: github actions //todo fixes
- **Deployment**: render.com via Docker

## Deployed API endpoint

- https://node-habit-tracker-api.onrender.com/health
  (free account, might break in future)

## API Endpoints

### Authentication

- `POST /api/auth/register` - Create new user account
- `POST /api/auth/login` - Login and receive JWT token

### User Management

- `GET /api/users/profile` - Get current user profile
- `PUT /api/users/profile` - Update user profile
- `PUT /api/users/password` - Change password

### Habits (Protected Routes)

- `GET /api/habits` - Get all user habits
- `POST /api/habits` - Create new habit
- `PUT /api/habits/:id` - Update habit
- `DELETE /api/habits/:id` - Delete habit
- `POST /api/habits/:id/complete` - Mark habit as completed today
- `GET /api/habits/:id/stats` - Get habit statistics

## Data Models

### Users

```typescript
{
  id: uuid (PK)
  email: string (unique)
  username: string (unique)
  password: string (hashed)
  firstName?: string
  lastName?: string
  createdAt: timestamp
  updatedAt: timestamp
}
```

### Habits

```typescript
{
  id: uuid (PK)
  userId: uuid (FK -> users.id)
  name: string
  description?: string
  frequency: 'daily' | 'weekly' | 'monthly'
  targetCount: number (default: 1)
  isActive: boolean (default: true)
  createdAt: timestamp
  updatedAt: timestamp
}
```

### Entries (Habit Completions)

```typescript
{
  id: uuid (PK)
  habitId: uuid (FK -> habits.id)
  completion_date: timestamp
  note?: string
  createdAt: timestamp
}
```

## Setup Instructions

### Prerequisites

- Node.js 24+
- PostgreSQL 14+
- npm or yarn

### Installation

```bash
# Install dependencies
npm install

# Setup environment variables
cp .env.example .env
# Edit .env with your database credentials

# Generate database migrations
npm run db:generate

# Run migrations
npm run db:migrate

# Start development server
npm run dev
```

### Environment Variables

```env
DATABASE_URL=postgresql://user:password@localhost:5432/habit_tracker
JWT_SECRET=your-secret-key-here
NODE_ENV=development
PORT=3000
```

## Running with Docker

### Build the image

```bash
docker build -t habit-tracker:latest .
```

### Run the container

Create `.env.docker` with your database details:

```bash
NODE_ENV=development
DATABASE_URL=postgresql://neondb_owner:YOUR_PASSWORD@YOUR_NEON_HOST/neondb?sslmode=require
JWT_SECRET=your-secret-at-least-32-chars
BCRYPT_ROUNDS=12
LOG_LEVEL=debug
```

Run:

```bash
docker run -p 3000:3000 --env-file .env.docker habit-tracker:latest
```

Visit: `http://localhost:3000`

### Stop the container

```bash
# Find the container ID
docker ps

# Stop it
docker stop <CONTAINER_ID>
```

---

## Testing

```bash
# Run all tests
npm test

# Run tests in watch mode
npm run test:watch

# Run specific test file
npm test habits.test.ts
```

## Key Features Explained

### Habit Completion Logic

- Users can only complete a habit once per day
- Attempting duplicate completion returns 409 Conflict
- Completions are tracked with timestamps for accurate streak calculation

### Statistics Calculation

- **Current Streak**: Consecutive days completed (must include today or yesterday)
- **Longest Streak**: Maximum consecutive days ever achieved
- **Total Completions**: Count of all completion entries
- **Completion Percentage**: (Days completed / Days since creation) × 100

### Security Features

- Password hashing with bcrypt (10 rounds)
- JWT tokens with expiration
- Request validation with Zod schemas
- SQL injection prevention via parameterized queries
- XSS protection with Helmet
- CORS configuration for cross-origin requests

## Common Issues & Solutions

### Database Connection Issues

- Ensure PostgreSQL is running
- Check DATABASE_URL format
- Verify database exists and user has permissions

### Migration Errors

- Run `npm run db:generate` before `npm run db:migrate`
- Check for pending migrations with Drizzle Studio
- Ensure database schema matches TypeScript types

### Test Failures

- Use separate test database
- Clear test data between runs
- Check for port conflicts (default: 3000)

## Additional Resources

- [Express.js Documentation](https://expressjs.com/)
- [Drizzle ORM Documentation](https://orm.drizzle.team/)
- [JWT.io](https://jwt.io/)
- [PostgreSQL Documentation](https://www.postgresql.org/docs/)
