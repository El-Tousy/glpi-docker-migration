<div align="center">

# GLPI 10 → GLPI 11 Docker Migration

**A Dockerized GLPI 11 installation and the migration of an existing GLPI 10 database — entirely on Windows, with Docker Desktop and no Linux VM.**

Technical Internship Project · 2026

[**📸 Screenshots**](#screenshots) · [**🏗️ Architecture**](#architecture) · [**🇩🇪 Deutsch**](README.de.md)

[![Docker](https://img.shields.io/badge/Docker-2496ED?style=flat&logo=docker&logoColor=white)](https://www.docker.com/)
[![MariaDB](https://img.shields.io/badge/MariaDB_10.6-003545?style=flat&logo=mariadb&logoColor=white)](https://mariadb.org/)
[![GLPI](https://img.shields.io/badge/GLPI_11-0B3D91?style=flat&logoColor=white)](https://glpi-project.org/)
[![phpMyAdmin](https://img.shields.io/badge/phpMyAdmin-6C78AF?style=flat&logo=phpmyadmin&logoColor=white)](https://www.phpmyadmin.net/)
[![PowerShell](https://img.shields.io/badge/PowerShell-5391FE?style=flat&logo=powershell&logoColor=white)](https://learn.microsoft.com/powershell/)
[![License](https://img.shields.io/badge/License-MIT-green?style=flat)](LICENSE)

</div>

> **Confidentiality note.** For confidentiality reasons, no real company data (database dumps, screenshots showing real records) is included in this repository. Only the technical setup and the migration methodology are documented.

---

## Table of Contents

- [Overview](#overview)
- [Screenshots](#screenshots)
- [Features](#features)
- [Architecture](#architecture)
- [Tech Stack](#tech-stack)
- [Project Structure](#project-structure)
- [Getting Started](#getting-started)
- [Migration Process](#migration-process)
- [Author](#author)
- [License](#license)

---

## Overview

This project was carried out during a technical internship. The company was already running **GLPI 10** for IT asset and service management. The goal was to deploy a new **GLPI 11** environment with **Docker**, then migrate the existing database from version 10 to version 11 — entirely on Windows, using Docker Desktop and PowerShell / CMD, without any Linux virtual machine.

The work took about one month, covering the setup, the migration itself and the troubleshooting that came with it.

---

## Screenshots

<table width="100%">
  <tr>
    <td width="33%"><img src="./screenshots/Image1.jpg" width="100%" alt="Screenshot 1" /></td>
    <td width="33%"><img src="./screenshots/Image2.jpg" width="100%" alt="Screenshot 2" /></td>
    <td width="33%"><img src="./screenshots/Image3.jpg" width="100%" alt="Screenshot 3" /></td>
  </tr>
</table>

👉 [View all screenshots](./screenshots)

---

## Features

### Infrastructure

- **Fully Dockerized** — three isolated, orchestrated containers
- **Persistent storage** — named volumes for both the database and the GLPI files
- **Web-based database management** — phpMyAdmin included for inspection and troubleshooting

### Migration

- **Version-to-version upgrade** — a GLPI 10.0.7 database migrated into a fresh GLPI 11 schema
- **Automated update detection** — GLPI's built-in upgrade wizard is triggered on first access
- **Manual schema fixes** — SQL adjustments applied where the automatic migration was incomplete

---

## Architecture

```mermaid
flowchart LR
    U([Administrator]) -->|http://localhost:8080| G[GLPI 11<br/>diouxx/glpi]
    U -->|http://localhost:8081| P[phpMyAdmin]
    G --> D[(MariaDB 10.6)]
    P --> D
    D --- V1[(Named volume<br/>database)]
    G --- V2[(Named volume<br/>GLPI files)]
```

Three containers are orchestrated with Docker Compose: the GLPI application, the MariaDB database and phpMyAdmin. The database and the GLPI files live in named volumes, so they survive container restarts and re-creation.

---

## Tech Stack

| Technology | Usage |
|---|---|
| Docker Desktop | Container runtime on Windows |
| Docker Compose | Multi-container configuration |
| MariaDB 10.6 | Database engine |
| GLPI (`diouxx/glpi` image) | ITSM application |
| phpMyAdmin | Database administration |
| PowerShell / CMD | Command execution and migration commands |

---

## Project Structure

```
glpi-docker-migration/
│
├── docker-compose.yml     # GLPI, MariaDB and phpMyAdmin services
├── screenshots/           # Screenshots of the migrated instance
├── .gitignore
├── LICENSE
└── README.md
```

---

## Getting Started

### Prerequisites

- Docker Desktop (Windows)
- Basic familiarity with PowerShell / CMD

### Installation

1. Clone the repository:

   ```bash
   git clone https://github.com/El-Tousy/glpi-docker-migration.git
   cd glpi-docker-migration
   ```

2. Start the containers:

   ```powershell
   docker compose up -d
   ```

3. Open the services:
   - GLPI: [http://localhost:8080](http://localhost:8080)
   - phpMyAdmin: [http://localhost:8081](http://localhost:8081)

---

## Migration Process

```mermaid
flowchart LR
    A[1. Deploy GLPI 11<br/>empty schema] --> B[2. Import the<br/>GLPI 10 database]
    B --> C[3. Trigger the<br/>schema update]
    C --> D[4. Apply manual<br/>SQL fixes]
    D --> E[5. Verify the<br/>migration]
```

1. **Deploy the new stack.** Start GLPI 11 from scratch so that the empty schema is created.
2. **Import the GLPI 10 database** into the new database container (the dump is not included in this repository):

   ```powershell
   docker exec -i glpi-db mysql -u glpi -p glpi < glpi_backup_10.0.7.sql
   ```

3. **Trigger the schema update.** Opening GLPI in the browser detects the version mismatch and starts the upgrade process automatically.
4. **Apply manual SQL fixes.** Some schema differences between GLPI 10 and 11 required manual adjustments, done from CMD.
5. **Verify the migration.** The database structure was checked in phpMyAdmin and the functionality was validated through the GLPI dashboard.

---

## Author

**El-Tousy** — Computer Science student, Morocco

[![GitHub](https://img.shields.io/badge/GitHub-181717?style=flat&logo=github&logoColor=white)](https://github.com/El-Tousy)
[![Email](https://img.shields.io/badge/Email-D14836?style=flat&logo=gmail&logoColor=white)](mailto:leilaeltousy@gmail.com)

---

## License

Distributed under the MIT License. See [LICENSE](LICENSE) for details.

---

<div align="center">

⭐ If you find this project useful, consider leaving a star.

</div>
