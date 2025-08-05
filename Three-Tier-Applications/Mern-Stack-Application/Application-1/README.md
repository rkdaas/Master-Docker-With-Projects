---

## 🧾 Dockerized MERN Stack with NGINX Reverse Proxy

This project is a containerized full-stack MERN (MongoDB, Express, React, Node.js) application where **NGINX acts as a reverse proxy** to route traffic between the frontend and backend containers. MongoDB is also included as a service, all orchestrated using **Docker Compose**.

---

### 📦 Folder Structure

```
mern/
├── backend/       # Node.js + Express app (Dockerized)
├── frontend/      # React frontend (Dockerized)
└── nginx/         # NGINX reverse proxy config (Dockerized)
```

---

### 🐳 Dockerfile Overview (All Files Already Present in Repo)

#### 🔹 Backend Dockerfile (`mern/backend/Dockerfile`)

* Uses Node.js v18 image.
* Installs dependencies from `package.json`.
* Sets `/app` as the working directory.
* Exposes port `5050` inside the container.
* Starts the backend server with `npm start`.

#### 🔹 Frontend Dockerfile (`mern/frontend/Dockerfile`)

* Also uses Node.js v18 image.
* Sets up the working directory and installs React app dependencies.
* Exposes port `5173` (Vite dev server or similar).
* Starts the frontend with `npm run dev`.

#### 🔹 NGINX Dockerfile (`mern/nginx/Dockerfile`)

* Based on official NGINX image.
* Replaces the default NGINX config with a custom one (`nginx.conf`) to handle:

  * Proxying `/api` to the backend
  * All other routes to the frontend

---

### 🧱 Docker Compose Setup

* A single `docker-compose.yml` file orchestrates all services.
* Services:

  * `frontend` (`fc`) — React app container
  * `backend` (`bc`) — Express + Node.js API
  * `mongodb` — Official MongoDB image used
  * `nginx` — Reverse proxy exposed at host port `8080`
* All services share a custom Docker network `mern-network` for internal communication.
* Only the NGINX container is exposed to the host. All internal services (frontend, backend, database) are isolated and accessible only through the reverse proxy.

---

### 🔁 Internal Request Flow

1. Users access the app via `http://localhost:8080`.
2. Request hits **NGINX container** (mapped from host `8080` to container port `80`).
3. NGINX internally routes the request:

   * `/api/...` → forwarded to backend container (`bc`) on port `5050`
   * `/`, `/home`, etc. → forwarded to frontend container (`fc`) on port `5173`
4. Backend connects to MongoDB using internal hostname `mongodb:27017`.

---

### 🔒 Security & Networking

* Only the NGINX container is externally accessible.
* The frontend, backend, and MongoDB containers are **not exposed** to the host.
* Internal container communication is done using **Docker’s internal DNS**, where container names (like `bc`, `fc`, and `mongodb`) resolve automatically within the network.
* This ensures secure and modular microservice communication.

---

### 🌐 URL Routing Behavior

* External URL: `http://localhost:8080`
* Routing is handled by NGINX:

  * `http://localhost:8080/` → React frontend
  * `http://localhost:8080/api/users` → Express backend
* The **browser address bar does not change** when requests are proxied internally. The experience remains seamless.

---

### 🛠️ Run the Application

To build and start all containers:

```bash
docker-compose up --build
```

Then open your browser at:

```
http://localhost:8080
```

---

### 📌 Notes

* Make sure all `Dockerfile`s (`backend`, `frontend`, `nginx`) are located in their respective subdirectories.
* MongoDB does not need a custom Dockerfile — it is pulled from the official image via Compose.
* Any changes to the source code or NGINX config will require rebuilding the containers.

---

Let me know if you want to add health checks, static frontend serving through NGINX, or MongoDB GUI integration like Mongo Express.
