# 🐦 Chirpy

Chirpy is a lightweight Twitter-style microblogging platform built with **Go** and **PostgreSQL**.  
It allows users to create accounts, post short messages called *chirps*, and interact with a simple REST API.

---

## 🚀 Features

- User authentication with JWT tokens  
- Create, retrieve, and delete chirps  
- User registration and profile updates  
- Token refresh endpoint  
- Admin webhook integration  
- Metrics, readiness, and reset endpoints  
- PostgreSQL database migrations managed by **Goose**

---

## 🧱 Tech Stack

- **Language:** Go (Golang)  
- **Database:** PostgreSQL  
- **Migrations:** Goose  
- **Authentication:** JWT  
- **Architecture:** RESTful API

---

## ⚙️ Setup Instructions

### 1. Clone the Repository

```bash
git clone https://github.com/<your-username>/chirpy.git
cd chirpy
```

### 2. Environment Variables

Create a `.env` file in the project root:

```bash
DB_URL=postgres://<user>:<password>@localhost:5432/chirpy?sslmode=disable
JWT_SECRET=<your_jwt_secret>
```

### 3. Install Dependencies

```bash
go mod tidy
```

### 4. Database Setup (PostgreSQL)

Make sure PostgreSQL is running locally and create the `chirpy` database:

```bash
createdb chirpy
```

### 5. Run Goose Migrations

```bash
go install github.com/pressly/goose/v3/cmd/goose@latest

# Run migrations
goose -dir sql/schema postgres "$DB_URL" up
```

### 6. Start the Server

```bash
go run main.go
```

The server will start on [http://localhost:8080](http://localhost:8080).

---

## 🧑‍💻 API Documentation

### Base URL

```
http://localhost:8080/api
```

---

### **Users**

#### Create User

`POST /api/users`

```bash
curl -X POST http://localhost:8080/api/users   -H "Content-Type: application/json"   -d '{"email": "test@example.com", "password": "mypassword"}'
```

**Response**
```json
{
  "id": "uuid",
  "email": "test@example.com"
}
```

---

#### Update User

`PUT /api/users`

**Requires Authentication**

```bash
curl -X PUT http://localhost:8080/api/users   -H "Authorization: Bearer <token>"   -H "Content-Type: application/json"   -d '{"email": "new@example.com"}'
```

---

### **Authentication**

#### Login

`POST /api/login`

```bash
curl -X POST http://localhost:8080/api/login   -H "Content-Type: application/json"   -d '{"email": "test@example.com", "password": "mypassword"}'
```

**Response**
```json
{
  "token": "jwt-token",
  "refresh_token": "refresh-token"
}
```

---

#### Refresh Token

`POST /api/refresh`

```bash
curl -X POST http://localhost:8080/api/refresh   -H "Authorization: Bearer <refresh-token>"
```

---

### **Chirps**

#### Create Chirp

`POST /api/chirps`

```bash
curl -X POST http://localhost:8080/api/chirps   -H "Authorization: Bearer <token>"   -H "Content-Type: application/json"   -d '{"body": "Hello world!"}'
```

**Response**
```json
{
  "id": 1,
  "body": "Hello world!",
  "created_at": "2025-10-31T00:00:00Z"
}
```

---

#### Get Chirps

`GET /api/chirps`

```bash
curl http://localhost:8080/api/chirps
```

---

#### Get Chirp by ID

`GET /api/chirps/{id}`

```bash
curl http://localhost:8080/api/chirps/1
```

---

#### Delete Chirp

`DELETE /api/chirps/{id}`

**Requires Authentication**

```bash
curl -X DELETE http://localhost:8080/api/chirps/1   -H "Authorization: Bearer <token>"
```

---

### **Webhooks**

`POST /api/webhooks`

Used for admin or third-party integrations.

```bash
curl -X POST http://localhost:8080/api/webhooks   -H "Content-Type: application/json"   -d '{"event": "chirp.deleted", "chirp_id": 42}'
```

---

### **System Endpoints**

#### Readiness Check

`GET /api/readiness`

```bash
curl http://localhost:8080/api/readiness
```

Returns `200 OK` if the service is healthy.

---

#### Metrics

`GET /api/metrics`

Returns Prometheus-style metrics.

---

#### Reset (Development Only)

`POST /api/reset`

Resets database state. Use only in local development.

```bash
curl -X POST http://localhost:8080/api/reset
```

---

## 🧩 Example Environment Variables

```bash
PORT=8080
DB_URL=postgres://jlam:qwe123@localhost:5432/chirpy?sslmode=disable
JWT_SECRET=mysecretkey
```

---

## 🧠 Notes

- JWTs expire based on the duration configured in your Go code (`expires_in`).
- Tokens should be sent in the `Authorization: Bearer <token>` header.
- Goose migrations are stored in `sql/schema/`.

---

## 📜 License

MIT License © 2025 [Your Name]
