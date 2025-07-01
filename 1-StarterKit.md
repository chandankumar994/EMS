Perfect! Here's a complete starter kit with all three components:

---

## ✅ 1. Sample React Login Page (Basic Functionality)

**File:** `src/pages/LoginPage.js`

```jsx
import React, { useState } from 'react';
import axios from 'axios';

function LoginPage({ onLogin }) {
  const [username, setUsername] = useState('');
  const [password, setPassword] = useState('');
  const [error, setError] = useState('');

  const handleSubmit = async (e) => {
    e.preventDefault();
    try {
      const res = await axios.post('http://localhost:5000/api/auth/login', { username, password });
      localStorage.setItem('token', res.data.token);
      onLogin(); // redirect to dashboard
    } catch (err) {
      setError('Invalid credentials');
    }
  };

  return (
    <div className="login-container">
      <h2>Admin Login</h2>
      <form onSubmit={handleSubmit}>
        <input type="text" placeholder="Username"
          value={username} onChange={(e) => setUsername(e.target.value)} required />
        <input type="password" placeholder="Password"
          value={password} onChange={(e) => setPassword(e.target.value)} required />
        <button type="submit">Login</button>
        {error && <p style={{color: 'red'}}>{error}</p>}
      </form>
    </div>
  );
}

export default LoginPage;
```

> Ensure routing is setup using React Router DOM, and token check in `App.js`.

---

## ✅ 2. Express.js Employee Routes + Controller

### 🔹 `routes/employeeRoutes.js`

```js
const express = require('express');
const router = express.Router();
const employeeController = require('../controllers/employeeController');
const auth = require('../middleware/auth');

router.get('/', auth, employeeController.getAllEmployees);
router.get('/:id', auth, employeeController.getEmployeeById);
router.post('/', auth, employeeController.createEmployee);
router.put('/:id', auth, employeeController.updateEmployee);
router.delete('/:id', auth, employeeController.deleteEmployee);

module.exports = router;
```

### 🔹 `controllers/employeeController.js`

```js
const db = require('../config/db');

// Get All Employees
exports.getAllEmployees = (req, res) => {
  db.query('SELECT * FROM employees', (err, results) => {
    if (err) return res.status(500).json({ error: err.message });
    res.json(results);
  });
};

// Get Employee by ID
exports.getEmployeeById = (req, res) => {
  db.query('SELECT * FROM employees WHERE id = ?', [req.params.id], (err, results) => {
    if (err) return res.status(500).json({ error: err.message });
    res.json(results[0]);
  });
};

// Create New Employee
exports.createEmployee = (req, res) => {
  const data = req.body;
  const query = `INSERT INTO employees SET ?`;
  db.query(query, data, (err, result) => {
    if (err) return res.status(500).json({ error: err.message });
    res.status(201).json({ id: result.insertId, ...data });
  });
};

// Update Employee
exports.updateEmployee = (req, res) => {
  const data = req.body;
  const query = `UPDATE employees SET ? WHERE id = ?`;
  db.query(query, [data, req.params.id], (err) => {
    if (err) return res.status(500).json({ error: err.message });
    res.sendStatus(200);
  });
};

// Delete Employee
exports.deleteEmployee = (req, res) => {
  db.query('DELETE FROM employees WHERE id = ?', [req.params.id], (err) => {
    if (err) return res.status(500).json({ error: err.message });
    res.sendStatus(200);
  });
};
```

> Add authentication middleware (`middleware/auth.js`) that checks for JWT token.

---

## ✅ 3. SQL File for MySQL Table Creation

### 📄 `database.sql`

```sql
-- Users Table
CREATE TABLE users (
  id INT AUTO_INCREMENT PRIMARY KEY,
  username VARCHAR(50) UNIQUE NOT NULL,
  password_hash VARCHAR(255) NOT NULL,
  role ENUM('admin', 'hr') DEFAULT 'admin'
);

-- Departments Table
CREATE TABLE departments (
  id INT AUTO_INCREMENT PRIMARY KEY,
  name VARCHAR(100) NOT NULL
);

-- Designations Table
CREATE TABLE designations (
  id INT AUTO_INCREMENT PRIMARY KEY,
  name VARCHAR(100) NOT NULL,
  department_id INT,
  FOREIGN KEY (department_id) REFERENCES departments(id)
);

-- Employees Table
CREATE TABLE employees (
  id INT AUTO_INCREMENT PRIMARY KEY,
  emp_code VARCHAR(10) UNIQUE NOT NULL,
  first_name VARCHAR(50) NOT NULL,
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

## 🔄 Suggested Next Steps:

1. ✅ Create `db.js` connection file using `mysql2`.
2. 🔐 Build login controller with JWT + bcrypt.
3. 🔧 Setup CORS and middleware in `app.js`.
4. 🎨 Design employee registration form on frontend.


