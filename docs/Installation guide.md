## 🚀 HiATHOS - INSTALLATION GUIDE

#### 🔽 Clone the Repositories

Clone the project repositories to your local machine:

```bash
git clone https://github.com/AthosFatecSjc/backend.git
git clone https://github.com/AthosFatecSjc/frontend.git
```
---
### ⚙️ Backend Setup
Navigate to the backend folder:
```bash
cd backend
```

#### ✅ Prerequisites

Make sure you have installed:

* Docker
* Docker Compose

> ❗ No need to install Java, Python, or PostgreSQL locally — everything runs inside Docker.

#### 📁 Project Structure

```bash

├── backend-java
├── backend-python
├── init-scripts
├── docker-compose.yml
├── start.sh
├── .env.example
└── .env
```
#### ▶️ Run the Environment

##### 1. Configure Environment Variables

Create a `.env` file based on the example:

```bash
cp .env.example .env
```
Edit the file and replace placeholder values with your configuration.

##### 2. Grant Execution Permission

```bash
chmod +x start.sh
```
##### 3. Start the Application

```bash
./start.sh
```
🔄 What this script does:

* 🐳 Starts Docker containers
* ⏳ Waits for databases to be ready
* ☕ Starts Java backend
* 🐍 Installs Python dependencies
* 🚀 Starts Python backend

##### 4. Stop the Environment

To stop all services:
```bash
docker compose down
```
#### 🌐 Available Services

After running the project, access:

| Service                | URL                                            |
| ---------------------- | ---------------------------------------------- |
| 🗄️ Adminer (Postgres) | [http://localhost:8080](http://localhost:8080) |
| 🍃 Mongo Express       | [http://localhost:8081](http://localhost:8081) |
| ☕ Java Backend         | [http://localhost:8181](http://localhost:8181) |
| 🐍 Python Backend      | [http://localhost:8000](http://localhost:8000) |

#### 💡 Tips

* If something fails, check container logs:

  ```bash
  docker compose logs -f
  ```

* To rebuild containers:

  ```bash
  docker compose up --build
  ```
* Ensure ports are free before running
---
### 🎨 Frontend Setup
Navigate to the frontend folder:
```bash
cd frontend
```

#### ✅ Prerequisites

Make sure you have installed:

* Node.js 18+
* npm or yarn

#### 📁 Project Structure

```bash

├── node_modules
├── src
├── public
├── package-lock.json
├── package.json
├── start.sh
```
#### ▶️ Run the Environment

##### 1. Install dependencies

```bash
npm install
```

##### 2. Run manually

```bash
npm run dev
```
##### 3. Run using the automatic script
The project includes a script to start the frontend automatically.

First, grant execution permission:

```bash
chmod +x start.sh
```
Then run:
```bash
./start.sh
```
#### 🌐 Available Service

After running the project, access:

| Service                | URL                                            |
| ---------------------- | ---------------------------------------------- |
| 💻 Frontend Vue | [http://localhost:3000](http://localhost:3000) |