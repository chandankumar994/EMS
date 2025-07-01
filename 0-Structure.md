# EMS
Below is the structure of **Employee Management System (EMS)** project, covering front-end, back-end, database, APIs, and planning.

---

## 🚀 PROJECT OVERVIEW

**Tech Stack:**

* **Frontend:** React (with optional libraries like Redux, React Router)
* **Backend:** Node.js + Express
* **Database:** MySQL
* **API Format:** RESTful API (JSON-based)

---

## ✅ PHASE 1: PLANNING

### 1.1 Key Features (MVP):

* Admin Login
* Dashboard
* Employee Registration
* View/Update/Delete Employees
* Role-based Access (Optional)
* Department/Designation Management
* Leave Management (optional in V2)

---

### 1.2 ER DIAGRAM DESIGN

```plaintext
[Users]
- id (PK)
- username
- password_hash
- role (admin, hr)

[Employees]
- id (PK)
- emp_code (unique)
- first_name
- last_name
- email
- phone
- department_id (FK)
- designation_id (FK)
- doj
- status (active/inactive)

[Departments]
- id (PK)
- name

[Designations]
- id (PK)
- name
- department_id (FK)
```

---

## 🗄️ PHASE 2: DATABASE DESIGN

### 2.1 Tables & Keys (MySQL)

```sql
CREATE TABLE users (
  id INT AUTO_INCREMENT PRIMARY KEY,
  username VARCHAR(50) UNIQUE,
  password_hash VARCHAR(255),
  role ENUM('admin', 'hr') DEFAULT 'admin'
);

CREATE TABLE departments (
  id INT AUTO_INCREMENT PRIMARY KEY,
  name VARCHAR(100) NOT NULL
);

CREATE TABLE designations (
  id INT AUTO_INCREMENT PRIMARY KEY,
  name VARCHAR(100) NOT NULL,
  department_id INT,
  FOREIGN KEY (department_id) REFERENCES departments(id)
);

CREATE TABLE employees (
  id INT AUTO_INCREMENT PRIMARY KEY,
  emp_code VARCHAR(10) UNIQUE,
  first_name VARCHAR(50),
  last_name VARCHAR(50),
  email VARCHAR(100) UNIQUE,
  phone VARCHAR(15),
  department_id INT,
  designation_id INT,
  doj DATE,
  status ENUM('active', 'inactive') DEFAULT 'active',
  FOREIGN KEY (department_id) REFERENCES departments(id),
  FOREIGN KEY (designation_id) REFERENCES designations(id)
);
```

---

## 🧠 PHASE 3: BACKEND (Node.js + Express)

### 3.1 Folder Structure

```
backend/
│
├── config/           # DB connection
├── controllers/      # Business logic
├── models/           # DB queries
├── routes/           # API routes
├── middleware/       # Auth, validation
├── utils/            # Helpers (e.g. password hash)
├── .env
├── app.js
└── package.json
```

### 3.2 Sample APIs

| Endpoint             | Method | Description         |
| -------------------- | ------ | ------------------- |
| `/api/auth/login`    | POST   | Login for admin     |
| `/api/employees`     | GET    | List all employees  |
| `/api/employees/:id` | GET    | Get employee detail |
| `/api/employees`     | POST   | Add employee        |
| `/api/employees/:id` | PUT    | Update employee     |
| `/api/employees/:id` | DELETE | Delete employee     |

---

### 3.3 Key Backend Technologies

* bcrypt → password hashing
* jsonwebtoken (JWT) → auth
* dotenv → env config
* express-validator → validations
* mysql2 → MySQL connector

---

## 🎨 PHASE 4: FRONTEND (React)

### 4.1 Folder Structure

```
frontend/
│
├── src/
│   ├── components/
│   ├── pages/
│   ├── services/     # Axios + API calls
│   ├── context/      # Auth Context
│   ├── App.js
│   └── index.js
├── public/
└── package.json
```

### 4.2 Key Pages

* `/login` → Admin Login
* `/dashboard` → Summary Stats
* `/employees` → List View
* `/employees/new` → Registration Form
* `/employees/:id/edit` → Update Form

### 4.3 State & Routing

* **React Router** for navigation
* **useContext/useReducer** or Redux for state management (optional for V1)

### 4.4 Sample Service Call (Axios)

```js
// services/employeeService.js
import axios from 'axios';

const API_URL = 'http://localhost:5000/api/employees';

export const getEmployees = () => axios.get(API_URL);
export const getEmployee = (id) => axios.get(`${API_URL}/${id}`);
export const createEmployee = (data) => axios.post(API_URL, data);
export const updateEmployee = (id, data) => axios.put(`${API_URL}/${id}`, data);
export const deleteEmployee = (id) => axios.delete(`${API_URL}/${id}`);
```

---

## 🔐 PHASE 5: AUTHENTICATION

* Admin login via `/api/auth/login`
* Passwords stored as hash (`bcrypt`)
* JWT token stored in localStorage or HTTP-only cookies
* Middleware in backend to protect `/api/employees/*` endpoints

---

## 🧪 PHASE 6: TESTING

* Unit tests using Jest (backend)
* Frontend: React Testing Library
* Postman for API testing

---

## 🛠️ PHASE 7: DEPLOYMENT (later stage)

* Backend: Node on render.com / Railway / VPS
* Frontend: Vercel / Netlify
* DB: PlanetScale / Railway / MySQL server

---

## 🎯 Next Steps (Action Plan)

| Task                                       | Responsible | Deadline |
| ------------------------------------------ | ----------- | -------- |
| Set up project skeleton (frontend/backend) | You         | Day 1    |
| Create database schema in MySQL            | You         | Day 2    |
| Develop login API and UI                   | You         | Day 3    |
| Build employee CRUD APIs                   | You         | Day 4–5  |
| Connect frontend with backend              | You         | Day 6    |
| Final testing and polish                   | You         | Day 7    |

---

