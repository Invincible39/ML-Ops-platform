# Hypervisor-like MLOps Platform

---

## **Project Description**

A hypervisor-like service designed for efficient MLOps workflows. The platform facilitates the creation of organizations, clusters, and deployment of machine learning models with a focus on:

- **Efficient Scheduling**: Prioritized deployments with optimal resource utilization.
- **User Authentication**: Secure login and role-based access control (RBAC) using JWT.
- **Organization Management**: Manage organizations and invite users via unique codes.
- **Cluster and Deployment Management**: Create clusters with specific resources and deploy Dockerized ML models.

---

## **Key Features**

1. **User Authentication**:
   - Secure registration and login with hashed passwords.
   - JWT-based authentication with bearer tokens.
   - Role-based permissions.

2. **Organization Management**:
   - Create organizations with unique invite codes.
   - Add users to organizations by invite codes.

3. **Cluster Management**:
   - Create clusters with defined CPU, RAM, and GPU resources.
   - Track available resources for each cluster.

4. **Deployment Management**:
   - Deploy Dockerized models to clusters.
   - Automatically queue deployments if resources are unavailable.

5. **Task Scheduling**:
   - Efficient deployment scheduling using **priority-first resource allocation**.
   - Handles resource constraints and retries failed deployments up to a configurable maximum.

6. **Middleware**:
   - Custom middleware for managing database sessions and permissions.

7. **Monitoring and Debugging**:
   - Task queues managed with **Celery** and **Flower**.

---

## **Technologies Used**

- **FastAPI**: Framework for building APIs.
- **SQLAlchemy**: ORM for PostgreSQL interactions.
- **Supabase**: PostgreSQL database hosting.
- **Pydantic**: Data validation and schema management.
- **JWT**: Secure authentication.
- **Redis**: For caching and distributed locks.
- **Celery**: Background task scheduling.
- **Flower**: Monitor Celery tasks.
- **Uvicorn**: ASGI server for FastAPI.

---

## **How to Set Up the Application**

### **Prerequisites**

- **Python**: Version 3.7 or later.
- **Virtual Environment**: Recommended to avoid dependency conflicts.
- **PostgreSQL Database**: Use Supabase or any PostgreSQL instance.

---

### **1. Clone the Repository**

```bash
git clone <repository_url>
cd <repository_folder>
```

### **2. Setting up virtual env**

python -m venv .venv
source .venv/bin/activate 

### **3.  Install Dependencies**

pip install -r requirements.txt


### **4. Configure the .env File**
DATABASE_URL=postgresql://<username>:<password>@<host>:<port>/<database_name>
SECRET_KEY=your_secret_key
ALGORITHM=HS256
ACCESS_TOKEN_EXPIRE_MINUTES=30
REDIS_PORT=6379

### **5. Initialize the Database**
python -m app.database.initialize_data


### **6. Start the Application**
cd app
uvicorn main:app --reload

### **7. Start Background Workers**
celery -A workers.celery.task_scheduler worker -B --loglevel=info -Q critical

Start Flower for monitoring:
celery -A workers.celery.task_scheduler flower --port=5555 -Q critical

# **Testing the Application**

## **Workflow Steps**

### **1. Sign Up**
- Use the `/user/create_user` API to register a new user.

### **2. Login**
- Use the `/user/login_user` API to authenticate.
- Copy the **Bearer Token** from the response.


- **Create Tables first through create_tables api**


### **3. Create an Organization**
- Use the `/organization/create_organization` API to create an organization with a random invite code.

### **4. Join an Organization**
- Use the `/organization/join_organization` API with the invite code to join the organization.

### **5. Create Clusters**
- Once part of an organization, use the `/cluster/create_cluster` API to define clusters with **CPU**, **RAM**, and **GPU** resources.

### **6. Deploy Applications**
- Use the `/deployment/create_deployment` API to deploy Dockerized applications to clusters.
- Use the **Bearer Token** in the `Authorization` header for all requests.

---

## **API Overview**

### **User Management**
| **Endpoint**          | **Method** | **Description**       |
|------------------------|------------|-----------------------|
| `/user/create_user`    | `POST`     | Create a new user     |
| `/user/login_user`     | `POST`     | Login and get a token |

---

### **Organization Management**
| **Endpoint**                      | **Method** | **Description**                       |
|------------------------------------|------------|---------------------------------------|
| `/organization/create_organization` | `POST`     | Create a new organization             |
| `/organization/join_organization`   | `POST`     | Join an organization using an invite code |

---

### **Cluster Management**
| **Endpoint**            | **Method** | **Description**       |
|--------------------------|------------|-----------------------|
| `/cluster/create_cluster`| `POST`     | Create a new cluster  |
| `/cluster/list_clusters` | `GET`      | List clusters in an organization |

---

### **Deployment Management**
| **Endpoint**                | **Method** | **Description**       |
|------------------------------|------------|-----------------------|
| `/deployment/create_deployment` | `POST`     | Create a deployment   |
| `/deployment/list_deployments`  | `GET`      | List deployments      |

---

## **Postman Collection**

1. **Import Collection**: A Postman collection is included in the repository as `postman_collection.json`.
2. **Set Authorization**:
   - After logging in, copy the token from the `/user/login_user` response.
   - Add the token as a **Bearer Token** in the `Authorization` header for all subsequent requests.

---

## **Architecture Overview**

### **Database Tables**
- **`users`**: Stores user details and hashed passwords.
- **`roles`**: Manages RBAC roles.
- **`organizations`**: Stores organization data.
- **`clusters`**: Tracks clusters and their resources.
- **`deployments`**: Manages application deployments.
- **`policies`**: Defines permissions.
- **`resources`**: Tracks API endpoints.
- **`role_policies`**: Links roles to permissions.

---

## **Scheduling Tasks**

### **`schedule_deployment`**
- **Functionality**:
  - Prioritizes deployments based on resource availability.
  - Retries deployments if resources are insufficient.

### **`complete_deployment`**
- **Functionality**:
  - Frees up resources after a deployment completes.

---

## **Technologies and Tools**

| **Tool**       | **Purpose**                                   |
|-----------------|-----------------------------------------------|
| **FastAPI**    | API framework                                |
| **SQLAlchemy** | ORM for database                             |
| **Supabase**   | PostgreSQL database                          |
| **Celery**     | Background task management                   |
| **Redis**      | Caching and distributed locks                |
| **Flower**     | Monitoring Celery tasks                      |
| **JWT**        | Authentication                               |
| **Uvicorn**    | ASGI server                                  |


## Start Celery

```
celery -A workers.celery.task_scheduler worker -B --loglevel=info -Q critical

```

## Start Flower

```
celery -A workers.celery.task_scheduler flower --port=5555 -Q critical

```