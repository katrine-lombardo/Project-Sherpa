# Database Structure

### Users
The `users` table contains a user's associated authorization and verification data.

| Field            | Type              | Description                          |
| ---------------- | ----------------- | ------------------------------------ |
| id               | SERIAL            | Primary Key                          |
| firstname        | VARCHAR           | First name                           |
| lastname         | VARCHAR           | First name                           |
| email            | VARCHAR           | User's email address                 |
| email_verified_at| DATETIME          | Date user verified email             |
| hashedpassword   | VARCHAR           | User’s hashed password               |
| created_at       | DATETIME          | Date user was created                |
| updated_at       | DATETIME          | Date user was last updated           |

### Employees
The `employees` table elaborates on the user table, adding additional employment
information like held position, hire date, permissions level, and reporting
structure.

| Field            | Type              | Description                          |
| ---------------- | ----------------- | ------------------------------------ |
| employee_id      | INTEGER           | Primary Key & FK (users.id)          |
| position_id      | INTEGER           | Foreign Key (positions.position_id)  |
| hire_date        | DATETIME          | Date employee was hired              |
| permissions      | VARCHAR           | "Admin", "Department Head", "Manager", or "End User" |
| manager_id       | INTEGER           | The employee ID of the employee's manager (nullable for CEO or Founder roles) |

### Departments
The `departments` table separates department as its own concern, given the need
to be able to change department names and configuration frequently.

| Field            | Type              | Description                          |
| ---------------- | ----------------- | ------------------------------------ |
| department_id    | SERIAL            | Primary Key                          |
| name             | VARCHAR           | Name of the department               |

### Positions
The `positions` table describes a position's department, name, seniority level,
and whether it is a leadership position or not.

| Field            | Type              | Description                          |
| ---------------- | ----------------- | ------------------------------------ |
| position_id      | SERIAL            | Primary Key                          |
| department_id    | INTEGER           | Foreign Key (departments.department_id) |
| name             | VARCHAR           | Name of the position, AKA job title  |
| level            | INTEGER           | Seniority on a scale of 0-10         |
| is_leadership    | BOOLEAN           | Indicates if the position is a leadership role (default: FALSE) |

### Skills
The `skills` table contains information pertaining to skills, including a
definition and whether it's a leadership or professional skill.

| Field            | Type              | Description                          |
| ---------------- | ----------------- | ------------------------------------ |
| skill_id         | SERIAL            | Primary Key                          |
| name             | VARCHAR           | Name of the specific skill           |
| description      | TEXT              | A few sentences defining the skill   |
| type             | VARCHAR           | "Professional" or "Leadership"       |

### Reviews
The `reviews` table allows us to see the current skill proficiency for a
particular skill for a particular employee as determined by an evaluator,
typically but not always an employee's manager. An employee can have multiple
skills with different proficiency levels.

| Field            | Type              | Description                          |
| ---------------- | ----------------- | ------------------------------------ |
| id               | SERIAL            | Primary Key                          |
| employee_id      | INTEGER           | Foreign Key (employees.employee_id)  |
| skill_id         | INTEGER           | Foreign Key (skills.skill_id)        |
| proficiency      | INTEGER           | Proficiency level reviewed on a scale of 1-5  |
| last_updated     | DATETIME          | Date proficiency was last reviewed   |
| updated_by       | INTEGER           | Employee ID of the evaluator         |

### Competencies
The `competencies` table allows us to pull the required skill levels for any
position at any level, linking positions to skills with proficiency targets. A
position requires exactly 8 professional skills. A skill can belong to none,
one, or multiple positions.

| Field            | Type              | Description                          |
| ---------------- | ----------------- | ------------------------------------ |
| id               | SERIAL            | Primary Key                          |
| position_id      | INTEGER           | Foreign Key (positions.position_id)  |
| skill_id         | INTEGER           | Foreign Key (skills.skill_id)        |
| target           | INTEGER           | Proficiency level required           |




# Database Structure (DBML)

Ref: employees.manager_id > employees.employee_id // one-to-many
Ref: positions.department_id > departments.department_id // one-to-many
Ref: reviews.employee_id > employees.employee_id // one-to-many
Ref: reviews.skill_id > skills.skill_id // one-to-many
Ref: reviews.updated_by > employees.employee_id // one-to-many
Ref: reviews.position_id > positions.position_id // one-to-many
Ref: competencies.skill_id > skills.skill_id // one-to-many
```

### Explanation of Relationships

1. **Employees and Managers**:
    - Each employee has one manager, but a manager can manage multiple employees. The `manager_id` in the `employees` table references the `employee_id` in the same table.
    - `Ref: employees.manager_id > employees.employee_id // one-to-many`

2. **Departments and Positions**:
    - A department can contain zero or many positions. Each position belongs to one department.
    - `Ref: positions.department_id > departments.department_id // one-to-many`

3. **Positions and Skills**:
    - A position requires exactly 8 professional skills. A skill can belong to none, one, or multiple positions.
    - This is represented by the `competencies` table, which links positions to skills with proficiency targets.
    - `Ref: competencies.position_id > positions.position_id // one-to-many`
    - `Ref: competencies.skill_id > skills.skill_id // one-to-many`

4. **Employee Skills**:
    - An employee can have multiple skills with different proficiency levels.
    - `Ref: reviews.employee_id > employees.employee_id // one-to-many`
    - `Ref: reviews.skill_id > skills.skill_id // one-to-many`
    - `Ref: reviews.updated_by > employees.employee_id // one-to-many`
