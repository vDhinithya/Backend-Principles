# What is Backend?

## Story & Philosophy

Backend engineering is not just about writing code using a specific programming language or framework.  
It involves understanding **how systems work behind the scenes**, including networking, servers, data management, security, and infrastructure.

A strong backend engineer focuses on:

- System architecture
- Data handling
- Scalability
- Reliability
- Security
- Performance

Programming languages and libraries are simply **tools used to implement these systems**.

---

# What is Backend?

Traditionally, a **backend** is a computer (server) that listens for incoming network requests.

These requests can be:

- HTTP requests
- gRPC requests
- WebSocket requests
- Other protocol-based requests

The server exposes an **open port** (for example `8080`, `3000`, `80`, or `443`) that allows clients to connect to it.

Clients may include:

- Web browsers
- Mobile applications
- Other servers
- IoT devices

When a request arrives, the backend processes it and returns a response.

Because this machine **serves content or functionality**, it is called a **server**.

---

# How Backend Works

When a user interacts with a website or application, the request follows several steps before reaching the backend application.

## High-Level Flow
Browser → DNS Server → Firewall/Security Groups → Cloud Server(EC2) → Reverse Proxy(Ngnix) → Application Server(SpringBoot/Node.js)


---

# Step-by-Step Request Flow

## 1. Browser Request

The process begins when a user enters a URL in the browser.

Example: https://example.com


The browser sends a request asking:

> "Where is this website located?"

---

## 2. DNS Resolution

DNS stands for **Domain Name System**.

Humans use domain names like: example.com

But computers communicate using **IP addresses** like: 192.168.1.1


DNS translates the domain name into an IP address.

This is done using **DNS records**, such as:

- **A Record** → maps domain → IP address
- **CNAME Record** → maps domain → another domain

Example:example.com → 54.12.33.91


This IP may belong to a cloud server such as **AWS EC2**.

---

## 3. Firewall & Security Layer

Before reaching the actual server, the request passes through a **firewall**.

In AWS, this is typically implemented using **Security Groups**.

Security groups define which ports are open.

Common ports include:

| Port | Purpose |
|-----|------|
| 80 | HTTP |
| 443 | HTTPS |
| 22 | SSH |

If a request tries to access a port that is **not allowed**, it is blocked immediately.

This protects the server from unauthorized access.

---

## 4. Cloud Server (EC2 Instance)

If the firewall allows the request, it reaches the **cloud instance**.

For example:

- AWS EC2
- Google Cloud VM
- Azure Virtual Machine

This server is responsible for running the backend application.

---

## 5. Reverse Proxy (Nginx)

Inside the server, the request often first reaches a **reverse proxy** such as **Nginx**.

A reverse proxy sits between the client and the backend application.

Responsibilities of a reverse proxy include:

- Routing traffic to the correct service
- Handling HTTPS (SSL termination)
- Load balancing
- Redirecting HTTP → HTTPS
- Improving performance through caching

Example configuration:example.com → localhost:3001


This means Nginx receives the request and forwards it to the application server running locally.

---

## 6. Application Server

Finally, the request reaches the backend application itself.

Examples:

- Node.js server
- Java Spring Boot application
- Python Django server
- Go backend service

The application:

1. Processes the request
2. Interacts with databases or other services
3. Generates a response
4. Sends it back to the client

---

## Process Management

Application servers need to remain running continuously.

Tools such as **PM2**, **systemd**, or **Docker containers** are used to manage these processes.

Their responsibilities include:

- Restarting crashed applications
- Running services in the background
- Monitoring resource usage

Example: pm2 start app.js


