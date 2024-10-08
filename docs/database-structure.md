# Database Structure

### Users
The `users` table is automatically generated by Laravel with a user's associated authorization and verification data.

| Field            | Type              | Description                          |
| ---------------- | ----------------- | ------------------------------------ |
| id               | SERIAL            | Primary Key                          |
| name             | VARCHAR           | First and last name                  |
| email            | VARCHAR           | User's email address                 |
| email_verified_at| DATETIME          | Date user verified email             |
| password         | VARCHAR           | User’s hashed password               |
| remember_token   | VARCHAR           |                                      |
| created_at       | DATETIME          | Date user was created                |
| updated_at       | DATETIME          | Date user was last updated           |

### Employees
The `employees` table elaborates on the user table, adding additional information like held position, hire date, permissions level, and reporting structure.

| Field            | Type              | Description                          |
| ---------------- | ----------------- | ------------------------------------ |
| employee_id      | INTEGER           | Primary Key & FK (users.id)          |
| position_id      | INTEGER           | Foreign Key (positions.position_id)  |
| hire_date        | DATETIME          | Date employee was hired              |
| permissions      | VARCHAR           | "Admin", "Department Head", "Manager", or "End User" |
| manager_id       | INTEGER           | The employee ID of the employee's manager (nullable for CEO or Founder roles) |

### Departments
The `departments` table separates department as its own concern, given the need to be able to change department names and configuration frequently.

| Field            | Type              | Description                          |
| ---------------- | ----------------- | ------------------------------------ |
| department_id    | SERIAL            | Primary Key                          |
| name             | VARCHAR           | Name of the department               |

### Positions
The `positions` table describes a position's department, name, seniority level, and whether it is a leadership position or not.

| Field            | Type              | Description                          |
| ---------------- | ----------------- | ------------------------------------ |
| position_id      | SERIAL            | Primary Key                          |
| department_id    | INTEGER           | Foreign Key (departments.department_id) |
| name             | VARCHAR           | Name of the position, AKA job title  |
| level            | INTEGER           | Seniority on a scale of 0-10         |
| is_leadership    | BOOLEAN           | Indicates if the position is a leadership role (default: FALSE) |

### Skills
The `skills` table contains information pertaining to skills, including a definition and whether it's a leadership or professional skill.

| Field            | Type              | Description                          |
| ---------------- | ----------------- | ------------------------------------ |
| skill_id         | SERIAL            | Primary Key                          |
| name             | VARCHAR           | Name of the specific skill           |
| description      | TEXT              | A few sentences defining the skill   |
| type             | VARCHAR           | "Professional" or "Leadership"       |

### Employee Skills
The `employee_skills` joining table allows us to see the current skill proficiency for a particular skill for a particular employee as determined by an evaluator, typically but not always an employee's manager. An employee can have multiple skills with different proficiency levels.

| Field            | Type              | Description                          |
| ---------------- | ----------------- | ------------------------------------ |
| id               | SERIAL            | Primary Key                          |
| employee_id      | INTEGER           | Foreign Key (employees.employee_id)  |
| skill_id         | INTEGER           | Foreign Key (skills.skill_id)        |
| proficiency      | INTEGER           | Proficiency level on a scale of 1-5  |
| last_updated     | DATETIME          | Date proficiency was last updated    |
| updated_by       | INTEGER           | Employee ID of the evaluator         |

### Position Skills
The `position_skills` joining table allows us to pull the required skill levels for any position at any level. A position requires exactly 8 professional skills. A skill can belong to none or multiple positions. This is represented by the `position_skills` table, which links positions to skills with proficiency targets.

| Field            | Type              | Description                          |
| ---------------- | ----------------- | ------------------------------------ |
| id               | SERIAL            | Primary Key                          |
| position_id      | INTEGER           | Foreign Key (positions.position_id)  |
| skill_id         | INTEGER           | Foreign Key (skills.skill_id)        |
| target           | INTEGER           | Proficiency level required           |




# Database Structure (DBML)

```dbml
Table users {
    id SERIAL [pk]
    name VARCHAR [not null]
    email VARCHAR [not null, unique]
    email_verified_at DATETIME
    password VARCHAR [not null]
    remember_token VARCHAR
    created_at DATETIME [default: `CURRENT_TIMESTAMP`]
    updated_at DATETIME [default: `CURRENT_TIMESTAMP`]
}

Table employees {
    employee_id INTEGER [pk, ref: > users.id]
    position_id INTEGER [not null, ref: > positions.position_id]
    hire_date DATETIME [not null]
    permissions VARCHAR [not null]
    manager_id INTEGER [ref: > employees.employee_id, note: "nullable for CEO or Founder roles"] // one-to-many
}

Table departments {
    department_id SERIAL [pk]
    name VARCHAR [not null, unique]
}

Table positions {
    position_id SERIAL [pk]
    department_id INTEGER [not null, ref: > departments.department_id] // one-to-many
    name VARCHAR [not null]
    level INTEGER [not null]
    is_leadership BOOLEAN [default: false]
}

Table skills {
    skill_id SERIAL [pk]
    name VARCHAR [not null]
    description TEXT
    type VARCHAR [not null]
}

Table employee_skills {
    id SERIAL [pk]
    employee_id INTEGER [not null, ref: > employees.employee_id] // one-to-many
    skill_id INTEGER [not null, ref: > skills.skill_id] // one-to-many
    proficiency INTEGER [not null]
    last_updated DATETIME [default: `CURRENT_TIMESTAMP`]
    updated_by INTEGER [not null, ref: > employees.employee_id] // one-to-many
}

Table position_skills {
    id SERIAL [pk]
    position_id INTEGER [not null, ref: > positions.position_id] // one-to-many
    skill_id INTEGER [not null, ref: > skills.skill_id] // one-to-many
    target INTEGER [not null]
}

Ref: employees.manager_id > employees.employee_id // one-to-many
Ref: positions.department_id > departments.department_id // one-to-many
Ref: employee_skills.employee_id > employees.employee_id // one-to-many
Ref: employee_skills.skill_id > skills.skill_id // one-to-many
Ref: employee_skills.updated_by > employees.employee_id // one-to-many
Ref: position_skills.position_id > positions.position_id // one-to-many
Ref: position_skills.skill_id > skills.skill_id // one-to-many
```

### Explanation of Relationships

1. **Employees and Managers**:
    - Each employee has one manager, but a manager can manage multiple employees. The `manager_id` in the `employees` table references the `employee_id` in the same table.
    - `Ref: employees.manager_id > employees.employee_id // one-to-many`

2. **Departments and Positions**:
    - A department can contain zero or many positions. Each position belongs to one department.
    - `Ref: positions.department_id > departments.department_id // one-to-many`

3. **Positions and Skills**:
    - A position requires exactly 8 professional skills. A skill can belong to none or multiple positions.
    - This is represented by the `position_skills` table, which links positions to skills with proficiency targets.
    - `Ref: position_skills.position_id > positions.position_id // one-to-many`
    - `Ref: position_skills.skill_id > skills.skill_id // one-to-many`

4. **Employee Skills**:
    - An employee can have multiple skills with different proficiency levels.
    - `Ref: employee_skills.employee_id > employees.employee_id // one-to-many`
    - `Ref: employee_skills.skill_id > skills.skill_id // one-to-many`
    - `Ref: employee_skills.updated_by > employees.employee_id // one-to-many`
