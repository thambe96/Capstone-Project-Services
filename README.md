#### Student Name: Oshadha Sankalpa Thambavita
#### Student Number: 241711043
#### Slack Handle: Oshadha Thambavita
#### GCP Project ID: project-84bf5412-62ae-4b65-919


# Capstone Project — Services

This repository manages the **microservices** of the Capstone Project. It acts as the parent repository for the core business services required by the application.

The services are developed and maintained as **separate GitHub repositories** and are included in this repository as **Git submodules**.

The services currently managed by this repository are:

* **Student Service** — Manages and registers student details.
* **Program Service** — Manages and registers academic program details.
* **Enrollment Service** — Manages student enrollments in available programs.

This repository provides a single location from which the services can be cloned, managed, and deployed together, while allowing each microservice to maintain its own independent development and version history.

---

## Services Architecture

The services repository contains the following microservices:

```text
services/
├── student-service/
├── program-service/
├── enrollment-service/
└── README.md
```

Each service is an independent Git repository that has been added to this parent repository as a Git submodule.

The overall service architecture can be represented as:

```text
                         ┌──────────────────────┐
                         │      API Gateway     │
                         └──────────┬───────────┘
                                    │
              ┌─────────────────────┼─────────────────────┐
              │                     │                     │
              ▼                     ▼                     ▼
       ┌──────────────┐      ┌──────────────┐      ┌───────────────┐
       │   Student    │      │   Program    │      │   Enrollment  │
       │   Service    │      │   Service    │      │    Service    │
       └──────┬───────┘      └──────┬───────┘      └───────┬───────┘
              │                     │                      │
              ▼                     ▼                      ▼
       Student Details       Program Details        Enrollment Details
```

The services communicate within the platform infrastructure and use the platform-level components such as the **Config Server**, **Service Registry**, and **API Gateway**.

---

## Microservices

### Student Service

The **Student Service** is responsible for managing student-related information.

Its responsibilities include:

* Registering students.
* Managing student details.
* Providing student information to other services when required.
* Registering the service with the Service Registry.
* Fetching its configuration from the Config Server.

The Student Service is maintained in its own GitHub repository and included in this repository as a Git submodule.

---

### Program Service

The **Program Service** manages the academic programs available within the system.

Its responsibilities include:

* Registering programs.
* Managing program details.
* Providing program information to other services.
* Registering the service with the Service Registry.
* Fetching its configuration from the Config Server.

The Program Service is independently maintained in its own GitHub repository and included here as a Git submodule.

---

### Enrollment Service

The **Enrollment Service** manages the relationship between students and programs.

Its primary responsibility is to register students to available programs.

The service can:

* Create student enrollments.
* Associate students with programs.
* Manage enrollment information.
* Discover other services through the Service Registry.
* Retrieve configuration from the Config Server.

The Enrollment Service is maintained as a separate GitHub repository and included in this repository as a Git submodule.

---

## Relationship Between Services

The services work together to manage students, programs, and enrollments.

A simplified relationship is:

```text
                  ┌─────────────────┐
                  │  Student Service│
                  │                 │
                  │ Student Details │
                  └────────┬────────┘
                           │
                           │
                           ▼
                  ┌─────────────────┐
                  │Enrollment Service│
                  │                 │
                  │ Student →       │
                  │ Program         │
                  └────────┬────────┘
                           │
                           │
                           ▼
                  ┌─────────────────┐
                  │ Program Service │
                  │                 │
                  │ Program Details │
                  └─────────────────┘
```

The **Student Service** manages students, while the **Program Service** manages programs. The **Enrollment Service** connects these two domains by managing which students are enrolled in which programs.

---

## Git Submodules

The services are maintained independently and included in this parent repository using Git submodules.

```text
services/
│
├── student-service/       → Separate GitHub repository
├── program-service/       → Separate GitHub repository
└── enrollment-service/    → Separate GitHub repository
```

This approach provides:

* Independent development of each microservice.
* Separate version control for each service.
* Easier maintenance of individual services.
* The ability to deploy services independently.
* A single parent repository for managing the complete set of application services.
* Easier deployment to cloud virtual machines.

The parent repository tracks the specific commit of each submodule, allowing a particular version of each service to be deployed consistently.

---

## Auto-Scaling Infrastructure

The microservices are deployed using an **auto-scaling virtual machine group**.

The auto-scaling configuration allows additional virtual machine instances to be created when demand increases and instances to be reduced when demand decreases.

A simplified deployment architecture is:

```text
                         ┌─────────────────────┐
                         │    Load Balancer    │
                         └──────────┬──────────┘
                                    │
                    ┌───────────────┼───────────────┐
                    │               │               │
                    ▼               ▼               ▼
              ┌───────────┐   ┌───────────┐   ┌───────────┐
              │    VM 1   │   │    VM 2   │   │    VM 3   │
              │           │   │           │   │           │
              │ Services  │   │ Services  │   │ Services  │
              └───────────┘   └───────────┘   └───────────┘
                    │               │               │
                    └───────────────┼───────────────┘
                                    │
                             Auto-Scaling VM Group
```

The auto-scaling VM group provides the infrastructure required to run multiple instances of the microservices and scale the application according to workload.

---

## Process Manager

**PM2** is used as the process manager for running and managing the microservices on the virtual machine instances.

The services are managed using an `ecosystem.config.js` configuration file.

The ecosystem configuration defines the applications that PM2 should run and manage.

A simplified structure is:

```text
services/
├── student-service/
├── program-service/
├── enrollment-service/
├── ecosystem.config.js
└── README.md
```

PM2 provides process management capabilities such as:

* Starting microservices.
* Keeping services running.
* Restarting services when they fail.
* Managing multiple service processes.
* Monitoring running applications.
* Managing application instances.
* Simplifying service startup and deployment.

---

## PM2 Ecosystem Configuration

The `ecosystem.config.js` file is used to define the services managed by PM2.

A simplified example is:

```javascript
module.exports = {
  apps: [
    {
      name: "student-service",
      script: "./student-service/target/student-service.jar"
    },
    {
      name: "program-service",
      script: "./program-service/target/program-service.jar"
    },
    {
      name: "enrollment-service",
      script: "./enrollment-service/target/enrollment-service.jar"
    }
  ]
};
```

The actual configuration may vary depending on the project's build process, deployment structure, ports, environment variables, and Java runtime configuration.

---

## Service Deployment

A typical deployment process is:

```text
                   GitHub
                      │
                      ▼
              Services Repository
                      │
             Git Submodules
                      │
       ┌──────────────┼──────────────┐
       ▼              ▼              ▼
 Student Service  Program Service  Enrollment Service
       │              │              │
       └──────────────┼──────────────┘
                      │
                      ▼
              Cloud VM Instance
                      │
                      ▼
                   PM2
                      │
       ┌──────────────┼──────────────┐
       ▼              ▼              ▼
 Student Service  Program Service  Enrollment Service
```

The services can be pulled from the parent repository, built, and managed using PM2 on the VM instances.

When the auto-scaling infrastructure creates additional VM instances, the required services can be deployed and started on those instances using the configured deployment process.

---

## Cloning the Repository

Since this repository contains Git submodules, clone it together with its submodules:

```bash
git clone --recurse-submodules <repository-url>
```

If the repository has already been cloned without the submodules, initialize them using:

```bash
git submodule init
git submodule update
```

Alternatively:

```bash
git submodule update --init --recursive
```

---

## Updating Services

Each microservice is maintained in its own repository.

To update the submodules to their latest available commits:

```bash
git submodule update --remote
```

After updating the services, the parent repository will detect the updated submodule references.

Commit the changes to the parent repository:

```bash
git add .
git commit -m "Update service submodules"
git push
```

---

## Running Services with PM2

After the services have been built and the required configuration is available, PM2 can be used to start the services using the ecosystem configuration.

For example:

```bash
pm2 start ecosystem.config.js
```

To view the running services:

```bash
pm2 status
```

To view service logs:

```bash
pm2 logs
```

To restart the services:

```bash
pm2 restart ecosystem.config.js
```

The exact commands may depend on the PM2 configuration used by the deployment environment.

---

## Configuration and Service Discovery

The microservices are integrated with the platform components managed by the separate **platform repository**.

The services use:

* **Config Server** — Provides centralized configuration to the microservices.
* **Service Registry** — Allows microservices to register themselves and discover other services.
* **API Gateway** — Provides a centralized entry point for client requests.

The overall architecture can therefore be represented as:

```text
                         ┌────────────────────┐
                         │      Clients       │
                         └─────────┬──────────┘
                                   │
                                   ▼
                         ┌────────────────────┐
                         │    API Gateway     │
                         └─────────┬──────────┘
                                   │
             ┌─────────────────────┼─────────────────────┐
             │                     │                     │
             ▼                     ▼                     ▼
      ┌──────────────┐      ┌──────────────┐      ┌───────────────┐
      │   Student    │      │   Program    │      │   Enrollment  │
      │   Service    │      │   Service    │      │    Service    │
      └──────┬───────┘      └──────┬───────┘      └───────┬───────┘
             │                     │                      │
             └─────────────────────┼──────────────────────┘
                                   │
                    ┌──────────────┴──────────────┐
                    │                             │
                    ▼                             ▼
             ┌──────────────┐             ┌────────────────┐
             │Config Server │             │Service Registry│
             └──────────────┘             └────────────────┘
```

---

## Benefits of This Structure

This architecture provides several benefits:

* **Independent service repositories** — Each microservice can be developed and maintained independently.
* **Git submodules** — The parent repository provides centralized management of the services.
* **Scalability** — Auto-scaling VM groups allow service infrastructure to scale based on demand.
* **Process management** — PM2 manages the running service processes.
* **Centralized configuration** — Services retrieve configuration from the Config Server.
* **Service discovery** — Services can discover one another through the Service Registry.
* **Simplified deployment** — The complete set of services can be pulled through the parent repository.
* **Version control** — The parent repository tracks specific versions of each service.
* **Cloud deployment** — The services can be deployed across instances within the cloud infrastructure.

---

## Technologies

The services architecture uses technologies and infrastructure such as:

* Java
* Spring Boot
* Microservices Architecture
* Git
* GitHub
* Git Submodules
* PM2
* `ecosystem.config.js`
* Cloud Virtual Machines
* Auto-Scaling VM Groups
* Load Balancing
* Service Discovery
* Centralized Configuration

---

## Related Repositories

The services managed by this repository are:

* `student-service`
* `program-service`
* `enrollment-service`

Each service is maintained as an independent GitHub repository and included in this repository as a Git submodule.

---

## Purpose

The purpose of this repository is to provide a **single parent repository for managing and deploying the Capstone Project's business microservices**.

By using Git submodules, each service maintains its own independent repository while the parent repository provides a convenient way to manage and deploy the complete set of services.

Combined with an auto-scaling VM group and PM2 process management, this structure provides a scalable and manageable environment for running the Capstone Project microservices in the cloud.
