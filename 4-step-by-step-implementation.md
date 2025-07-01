Let’s continue step-by-step. Here’s how to implement:

---

## ✅ 1. Employee List & Edit Page with Table (React + Axios)

### 🔹 `pages/EmployeeList.js`

```jsx
import React, { useEffect, useState } from 'react';
import { fetchEmployees, deleteEmployee } from '../services/employeeService';
import { useNavigate } from 'react-router-dom';

function EmployeeList() {
  const [employees, setEmployees] = useState([]);
  const navigate = useNavigate();

  useEffect(() => {
    loadEmployees();
  }, []);

  const loadEmployees = async () => {
    const res = await fetchEmployees();
    setEmployees(res.data);
  };

  const handleDelete = async (id) => {
    if (window.confirm("Are you sure you want to delete this employee?")) {
      await deleteEmployee(id);
      loadEmployees();
    }
  };

  return (
    <div>
      <h2>Employee List</h2>
      <table border="1" cellPadding="10">
        <thead>
          <tr>
            <th>Emp Code</th>
            <th>Name</th>
            <th>Email</th>
            <th>Phone</th>
            <th>DOJ</th>
            <th>Status</th>
            <th>Actions</th>
          </tr>
        </thead>
        <tbody>
          {employees.map(emp => (
            <tr key={emp.id}>
              <td>{emp.emp_code}</td>
              <td>{emp.first_name} {emp.last_name}</td>
              <td>{emp.email}</td>
              <td>{emp.phone}</td>
              <td>{emp.doj}</td>
              <td>{emp.status}</td>
              <td>
                <button onClick={() => navigate(`/employees/${emp.id}/edit`)}>Edit</button>
                <button onClick={() => handleDelete(emp.id)}>Delete</button>
              </td>
            </tr>
          ))}
        </tbody>
      </table>
    </div>
  );
}

export default EmployeeList;
```

---

### 🔹 `pages/EditEmployee.js`

```jsx
import React, { useEffect, useState } from 'react';
import { useParams, useNavigate } from 'react-router-dom';
import { getEmployee, updateEmployee } from '../services/employeeService';

function EditEmployee() {
  const { id } = useParams();
  const navigate = useNavigate();
  const [formData, setFormData] = useState({});

  useEffect(() => {
    async function loadData() {
      const res = await getEmployee(id);
      setFormData(res.data);
    }
    loadData();
  }, [id]);

  const handleChange = e => {
    setFormData({ ...formData, [e.target.name]: e.target.value });
  };

  const handleSubmit = async e => {
    e.preventDefault();
    await updateEmployee(id, formData);
    alert('Employee updated');
    navigate('/employees');
  };

  return (
    <form onSubmit={handleSubmit}>
      <h2>Edit Employee</h2>
      <input name="first_name" value={formData.first_name || ''} onChange={handleChange} />
      <input name="last_name" value={formData.last_name || ''} onChange={handleChange} />
      <input name="email" value={formData.email || ''} onChange={handleChange} />
      <input name="phone" value={formData.phone || ''} onChange={handleChange} />
      <input name="doj" type="date" value={formData.doj || ''} onChange={handleChange} />
      <select name="status" value={formData.status} onChange={handleChange}>
        <option value="active">Active</option>
        <option value="inactive">Inactive</option>
      </select>
      <button type="submit">Update</button>
    </form>
  );
}

export default EditEmployee;
```

---

## ✅ 2. Dropdowns for Departments/Designations

### Backend API (`controllers/deptController.js`)

```js
exports.getDepartments = (req, res) => {
  db.query('SELECT * FROM departments', (err, results) => {
    if (err) return res.status(500).json({ error: err.message });
    res.json(results);
  });
};

exports.getDesignations = (req, res) => {
  db.query('SELECT * FROM designations', (err, results) => {
    if (err) return res.status(500).json({ error: err.message });
    res.json(results);
  });
};
```

### Routes

```js
const router = require('express').Router();
const deptController = require('../controllers/deptController');

router.get('/departments', deptController.getDepartments);
router.get('/designations', deptController.getDesignations);

module.exports = router;
```

### Frontend: Fetch in Form

```jsx
const [departments, setDepartments] = useState([]);
const [designations, setDesignations] = useState([]);

useEffect(() => {
  const fetchDropdowns = async () => {
    const deptRes = await API.get('/departments');
    const desgRes = await API.get('/designations');
    setDepartments(deptRes.data);
    setDesignations(desgRes.data);
  };
  fetchDropdowns();
}, []);

<select name="department_id" onChange={handleChange}>
  <option value="">Select Dept</option>
  {departments.map(d => <option value={d.id} key={d.id}>{d.name}</option>)}
</select>

<select name="designation_id" onChange={handleChange}>
  <option value="">Select Designation</option>
  {designations.map(d => <option value={d.id} key={d.id}>{d.name}</option>)}
</select>
```

---

## ✅ 3. Role-Based Access Control (Admin vs HR)

### Middleware: `checkRole.js`

```js
module.exports = function (role) {
  return function (req, res, next) {
    if (req.user.role !== role) {
      return res.status(403).json({ message: 'Forbidden: Access Denied' });
    }
    next();
  };
};
```

### Use in Routes

```js
const auth = require('../middleware/auth');
const checkRole = require('../middleware/checkRole');

router.post('/employees', auth, checkRole('admin'), employeeController.createEmployee);
```

### React: Hide buttons if not admin

```js
const user = JSON.parse(localStorage.getItem('user'));
{user?.role === 'admin' && <button>Edit</button>}
```

> Tip: Store decoded JWT user in localStorage on login:

```js
const decoded = jwt_decode(res.data.token);
localStorage.setItem('user', JSON.stringify(decoded));
```

---

## ✅ 4. Full Deployment Guidance

### 🔹 Backend (Node + MySQL)

* Host: [Railway](https://railway.app), [Render](https://render.com), VPS, or your own server
* Setup `.env` file on host
* Add CORS allowed origin
* Use `pm2` to run server in background

### 🔹 Frontend (React)

* Host on **Netlify** / **Vercel**
* Set API base URL to your deployed backend:

```js
const API = axios.create({
  baseURL: 'https://yourbackend.com/api',
});
```

### 🔹 Database (MySQL)

* Use Railway, PlanetScale, or VPS-hosted MySQL
* Open firewall ports or use DB URL in `.env`

### 🔹 Domain + SSL (Optional)

* Use Cloudflare or direct DNS
* Let’s Encrypt or hosting provider SSL

---

