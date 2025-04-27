# Webhook Delivery Service
A robust, scalable backend system to manage webhook subscriptions, ingest and queue incoming webhooks, and reliably deliver them asynchronously with retries and delivery status visibility.

# 🐳 Local Setup Instructions (using Docker)
FastAPI + PostgreSQL + Redis + Celery (Dockerized)
This project is a production-ready full-stack app built with:
* FastAPI (Web framework)
* PostgreSQL (Database)
* Redis (Message broker for Celery)
* Celery (Background Task Processing)
* Docker & Docker Compose (Containerization)
## Project Structure
![image](https://github.com/user-attachments/assets/5840cc56-ce44-4ba0-bf7e-183c5f381f6e)

## Quick Start(Local Setup)
Follow these steps to get the app running on your local machine using Docker:

### 1. Clone the Repository
* git clone https://github.com/your-username/your-repo.git
cd your-repo

### 2. Create .env File
Create a .env file in the root with the following content:
* DATABASE_URL=postgresql+psycopg2://postgres:12345@db/webhooks
* REDIS_URL=redis://redis:6379/0
> Note: Use production URLs when deploying (Render, Upstash, etc.). 

### 3. Build and Run Containers
* docker-compose up --build
Docker will build and start the following services:
* Web: FastAPI app
* PostgreSQL: Database
* Redis: Redis server
* Worker: Celery background worker

### 4. Access the Application
Visit your FastAPI app:
* http://localhost:8000
You can access the interactive Swagger UI documentation at /docs.

## Docker Compose Breakdown
![image](https://github.com/user-attachments/assets/ca510807-a904-4070-b88d-7c78f33196d3)


  ## Useful Commands
  * Stop all containers:
    > docker-compose down
  * Rebuild everything:
    > docker-compose up --build
  *  Check logs:
    > docker-compose logs -f

  ## Deployment on Render
  * 1. Create a new Web Service for the FastAPI app.
  * 2. Provision a PostgreSQL Database on Render.
  * 3. Provision Redis-use Upstash .
  * 4. Set the correct environment variables in Render:
       > DATABASE_URL
       > REDIS_URL
  * 5. Make sure to append ?sslmode=require for PostgreSQL URLs.

# 🌐 Live Application Link
  > https://webhook-service-1-i4ar.onrender.com/docs#/

# 🏗️ Explanation of Architecture Choices

## Framework (FastAPI):
We chose FastAPI due to its high performance, modern asynchronous support, and easy-to-write Pythonic syntax. It is ideal for building APIs quickly and efficiently with automatic OpenAPI documentation support.

## Database (PostgreSQL):
PostgreSQL was selected for its robustness, scalability, and excellent support for relational data. It ensures ACID compliance, powerful querying, and reliability for production-grade applications.

## Async Task/Queueing System (Celery + Redis):
For handling background tasks like webhook event processing and retries, we integrated Celery. It allows task queuing, scheduling, and retry mechanisms efficiently. Redis was used as the Celery broker for its fast in-memory data handling.

## Retry Strategy:
Failed webhook deliveries are automatically retried using Celery's built-in retry mechanism. Exponential backoff strategies are applied to minimize load and maximize chances of successful delivery.

## Environment Configuration:
All sensitive information like database URLs and Redis endpoints are managed through environment variables (.env file) for better security and easy configuration across environments (development, staging, production).

## Project Structure:
The project follows a modular folder structure (app/) separating concerns like models, schemas, CRUD operations, and database setup, improving maintainability and scalability.


# Database Schema and Indexing Strategies
## 🛠️Tables:
* **subscriptions:** Stores webhook subscription information (id, target_url, secret).
* **webhook_deliveries:** Stores delivery logs (id, subscription_id, payload, status, 
 attempt, http_code, error_message, timestamp).

## 🛠️Indexes:
* id fields are primary keys (auto-indexed).
* Foreign key constraint on subscription_id in webhook_deliveries.

## 🛠️Rationale:
* subscription_id indexing improves lookup speed when fetching webhook logs for a subscription.
* No complex secondary indexes added because basic primary key and foreign key constraints are sufficient for moderate traffic volume.

# 🛠️ Sample cURL Commands
## ✅ Create a subscription:
curl -X POST http://127.0.0.1:8000/subscriptions \
-H "Content-Type: application/json" \
-d '{"target_url": "https://example.com/webhook", "secret": "your_secret_key"}'

## ✅ Ingest a webhook:
curl -X POST http://127.0.0.1:8000/ingest/1 \
-H "Content-Type: application/json" \
-d '{"payload": {"event": "order_created", "order_id": 12345}}'

## ✅ (Optional) Checking webhook status (future scope if added):
* Not implemented in current base version.

# 🛠️ Estimated Monthly Cost (Free Tier)
![image](https://github.com/user-attachments/assets/896f7fb6-bf27-4e2a-861a-6f81282ae4ba)

## ✅ Total Estimated Monthly Cost: $0 (under free tier limits)
Assuming ~5000 webhooks/day (~6000 total delivery attempts with retries) with moderate payload sizes.


# 🛠️ Assumptions Made
* Delivery retries are capped at 5 per webhook.
* Payload sizes are moderate (<10 KB).
* Target webhook endpoints are generally available (low failure rate).
* PostgreSQL free tier sufficient for webhook + log storage for 30 days rolling window.
* Redis free tier sufficient for Celery task queue under given traffic.

# 🛠️ Credits
* **FastAPI —** for building the asynchronous API.
* **SQLAlchemy —** for ORM/database interaction.
* **Celery —** for asynchronous background task processing.
* **Redis —** as the message broker for Celery.
* **PostgreSQL —** as the primary relational database.
* **Uvicorn —** for serving the FastAPI app.
* **httpx —** for making asynchronous HTTP requests to target webhook URLs.
* **ChatGPT —** for assisting in project structuring



