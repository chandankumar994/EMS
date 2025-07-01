Here's help with all three:

---

## ✅ 1. **Creating a Seed User with Hashed Password**

To securely insert an admin user into the `users` table:

### 🔹 `seedUser.js` (one-time script)

```js
const bcrypt = require('bcrypt');
const db = require('./config/db'); // adjust path as needed

const createAdminUser = async () => {
  const username = 'admin';
  const password = 'admin123';
  const role = 'admin';

  try {
    const hash = await bcrypt.hash(password, 10);

    const sql = 'INSERT INTO users (username, password_hash, role) VALUES (?, ?, ?)';
    db.query(sql, [username, hash, role], (err, result) => {
      if (err) {
        console.error('❌ Failed to insert user:', err.message);
      } else {
        console.log('✅ Admin user created with ID:', result.insertId);
      }
      process.exit();
    });
  } catch (error) {
    console.error('❌ Hashing failed:', error.message);
    process.exit();
  }
};

createAdminUser();
```

### 📦 Run the script:

```bash
node seedUser.js
```

---

## ✅ 2. React Frontend JWT Token Management

### 🔹 Store token on login (in `LoginPage.js`)

```js
localStorage.setItem('token', res.data.token);
```

### 🔹 Create an Axios instance with token

**`services/api.js`**

```js
import axios from 'axios';

const API = axios.create({
  baseURL: 'http://localhost:5000/api',
});

// Auto add token to requests
API.interceptors.request.use((config) => {
  const token = localStorage.getItem('token');
  if (token) {
    config.headers.Authorization = `Bearer ${token}`;
  }
  return config;
});

export default API;
```

### 🔹 Usage in any service

```js
import API from './api';

export const fetchEmployees = () => API.get('/employees');
export const registerEmployee = (data) => API.post('/employees', data);
```

### 🔹 Handle logout

```js
const handleLogout = () => {
  localStorage.removeItem('token');
  navigate('/login');
};
```

---

## ✅ 3. Employee Registration React Form + API

### 🔹 `src/pages/RegisterEmployee.js`

```jsx
import React, { useState } from 'react';
import { registerEmployee } from '../services/employeeService';

function RegisterEmployee() {
  const [formData, setFormData] = useState({
    emp_code: '',
    first_name: '',
    last_name: '',
    email: '',
    phone: '',
    department_id: '',
    designation_id: '',
    doj: '',
    status: 'active',
  });

  const handleChange = (e) => {
    setFormData({ ...formData, [e.target.name]: e.target.value });
  };

  const handleSubmit = async (e) => {
    e.preventDefault();
    try {
      await registerEmployee(formData);
      alert('Employee Registered!');
      setFormData({});
    } catch (err) {
      alert('Error registering employee');
    }
  };

  return (
    <div>
      <h2>Register New Employee</h2>
      <form onSubmit={handleSubmit}>
        <input name="emp_code" placeholder="Employee Code" onChange={handleChange} required />
        <input name="first_name" placeholder="First Name" onChange={handleChange} required />
        <input name="last_name" placeholder="Last Name" onChange={handleChange} />
        <input name="email" type="email" placeholder="Email" onChange={handleChange} />
        <input name="phone" placeholder="Phone" onChange={handleChange} />
        <input name="department_id" placeholder="Department ID" onChange={handleChange} required />
        <input name="designation_id" placeholder="Designation ID" onChange={handleChange} required />
        <input name="doj" type="date" placeholder="Date of Joining" onChange={handleChange} />
        <select name="status" onChange={handleChange}>
          <option value="active">Active</option>
          <option value="inactive">Inactive</option>
        </select>
        <button type="submit">Register</button>
      </form>
    </div>
  );
}

export default RegisterEmployee;
```

### 🔹 `services/employeeService.js`

```js
import API from './api';

export const registerEmployee = (data) => API.post('/employees', data);
```


