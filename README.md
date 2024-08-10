# Project Sherpa

## About Sherpa
Project Sherpa is a tool that helps managers encourage and validate their team
members's growth, standardising the promotion and development cycle. After an
engagement survey produced a result where the lowest satisfaction rating came
from transparency around career
development, my boss and I collaboratively came up with a solution to improve
this engagement metric. Over several iterations, Sherpa became
the name of the semi-annual process that required managers to work with their
direct employees to provide greater transparency around the explicit
requirements for career growth within the organisation.

## Motivation
I originally created a version of this project using Excel/Google Sheets. This
format was "good enough", however major limitations existed around editing
permissions, editing history, viewability, and general user experience. I built
the Sherpa App to address these limitations, while simultaneously adding
additional functionality to improve the overall design of the process.

## Technologies Used
Python JavaScript React Postgres Bootstrap Docker


## Design

### Database Structure
This database structure manages user, employee, department, position, and skill
information by using relational principles to ensure data integrity and ease of
querying. Key tables include users for authorization data, employees for job
details, departments for organizational units, positions for job roles, and
skills for employee capabilities. Joining tables like Reviews and
Competencies are employed to handle many-to-many relationships, ensuring
normalization, flexibility, scalability, and improved query performance by
avoiding redundancy and simplifying complex data associations.

### Backend
FastAPI

### Frontend
React with Vite


## Obstacles


## Learnings



## Future Development



## Getting the App Running
Run the server:
```uvicorn api.main:app --reload```
