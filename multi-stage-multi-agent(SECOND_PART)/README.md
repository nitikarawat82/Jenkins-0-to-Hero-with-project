## Project PART 2: Multi-Stage Jenkins Pipeline for a Three-Tier Application

This project demonstrates a multi-stage Jenkins CI/CD pipeline for building, testing, containerizing, and deploying a three-tier application consisting of a frontend, backend, and database.

In a three-tier application, we have three separate layers:

- **Frontend** – User interface
- **Backend** – Application logic and APIs
- **Database** – Stores application data

Managing the deployment of these different components manually can be time-consuming. Whenever there is a code change, we may need to manually build, test, and deploy the frontend and backend. This can also lead to deployment errors and inconsistent environments.

## Solution

To solve this problem, we use Jenkins to automate the CI/CD process.
