<div align="center">

# GLPI 10 → GLPI 11 Docker-Migration

**Eine Docker-basierte GLPI-11-Installation und die Migration einer bestehenden GLPI-10-Datenbank — vollständig unter Windows mit Docker Desktop und ohne Linux-VM.**

Projekt aus dem technischen Praktikum · 2026

[**📸 Screenshots**](#screenshots) · [**🏗️ Architektur**](#architektur) · [**🇬🇧 English**](README.en.md)

[![Docker](https://img.shields.io/badge/Docker-2496ED?style=flat&logo=docker&logoColor=white)](https://www.docker.com/)
[![MariaDB](https://img.shields.io/badge/MariaDB_10.6-003545?style=flat&logo=mariadb&logoColor=white)](https://mariadb.org/)
[![GLPI](https://img.shields.io/badge/GLPI_11-0B3D91?style=flat&logoColor=white)](https://glpi-project.org/)
[![phpMyAdmin](https://img.shields.io/badge/phpMyAdmin-6C78AF?style=flat&logo=phpmyadmin&logoColor=white)](https://www.phpmyadmin.net/)
[![PowerShell](https://img.shields.io/badge/PowerShell-5391FE?style=flat&logo=powershell&logoColor=white)](https://learn.microsoft.com/powershell/)
[![License](https://img.shields.io/badge/License-MIT-green?style=flat)](LICENSE)

</div>

> **Hinweis zur Vertraulichkeit.** Aus Gründen der Vertraulichkeit enthält dieses Repository keine echten Unternehmensdaten (Datenbank-Dumps, Screenshots mit echten Datensätzen). Dokumentiert werden ausschließlich der technische Aufbau und die Migrationsmethodik.

---

## Inhaltsverzeichnis

- [Überblick](#überblick)
- [Screenshots](#screenshots)
- [Funktionen](#funktionen)
- [Architektur](#architektur)
- [Tech-Stack](#tech-stack)
- [Projektstruktur](#projektstruktur)
- [Erste Schritte](#erste-schritte)
- [Migrationsablauf](#migrationsablauf)
- [Autor](#autor)
- [Lizenz](#lizenz)

---

## Überblick

Dieses Projekt wurde während eines technischen Praktikums durchgeführt. Das Unternehmen setzte bereits **GLPI 10** für die Verwaltung von IT-Assets und Services ein. Ziel war es, mit **Docker** eine neue **GLPI-11**-Umgebung bereitzustellen und anschließend die bestehende Datenbank von Version 10 auf Version 11 zu migrieren — vollständig unter Windows, mit Docker Desktop und PowerShell / CMD, ohne virtuelle Linux-Maschine.

Die Arbeit dauerte etwa einen Monat und umfasste die Einrichtung, die Migration selbst und die dabei anfallende Fehlersuche.

---

## Screenshots

<table width="100%">
  <tr>
    <td width="33%"><img src="./screenshots/Image1.jpg" width="100%" alt="Screenshot 1" /></td>
    <td width="33%"><img src="./screenshots/Image2.jpg" width="100%" alt="Screenshot 2" /></td>
    <td width="33%"><img src="./screenshots/Image3.jpg" width="100%" alt="Screenshot 3" /></td>
  </tr>
</table>

👉 [Alle Screenshots ansehen](./screenshots)

---

## Funktionen

### Infrastruktur

- **Vollständig mit Docker betrieben** — drei isolierte, orchestrierte Container
- **Persistente Speicherung** — benannte Volumes sowohl für die Datenbank als auch für die GLPI-Dateien
- **Webbasierte Datenbankverwaltung** — phpMyAdmin für Inspektion und Fehlersuche enthalten

### Migration

- **Upgrade von Version zu Version** — eine GLPI-10.0.7-Datenbank wurde in ein frisches GLPI-11-Schema migriert
- **Automatische Update-Erkennung** — der integrierte Upgrade-Assistent von GLPI startet beim ersten Zugriff
- **Manuelle Schema-Korrekturen** — SQL-Anpassungen dort, wo die automatische Migration unvollständig war

---

## Architektur

```mermaid
flowchart LR
    U([Administrator]) -->|http://localhost:8080| G[GLPI 11<br/>diouxx/glpi]
    U -->|http://localhost:8081| P[phpMyAdmin]
    G --> D[(MariaDB 10.6)]
    P --> D
    D --- V1[(Benanntes Volume<br/>Datenbank)]
    G --- V2[(Benanntes Volume<br/>GLPI-Dateien)]
```

Drei Container werden mit Docker Compose orchestriert: die GLPI-Anwendung, die MariaDB-Datenbank und phpMyAdmin. Die Datenbank und die GLPI-Dateien liegen in benannten Volumes und bleiben daher bei Neustarts und beim Neuerstellen der Container erhalten.

---

## Tech-Stack

| Technologie | Verwendung |
|---|---|
| Docker Desktop | Container-Laufzeitumgebung unter Windows |
| Docker Compose | Konfiguration mehrerer Container |
| MariaDB 10.6 | Datenbank-Engine |
| GLPI (Image `diouxx/glpi`) | ITSM-Anwendung |
| phpMyAdmin | Datenbankverwaltung |
| PowerShell / CMD | Ausführung von Befehlen und Migrationsbefehlen |

---

## Projektstruktur

```
glpi-docker-migration/
│
├── docker-compose.yml     # Dienste für GLPI, MariaDB und phpMyAdmin
├── screenshots/           # Screenshots der migrierten Instanz
├── .gitignore
├── LICENSE
└── README.md
```

---

## Erste Schritte

### Voraussetzungen

- Docker Desktop (Windows)
- Grundkenntnisse in PowerShell / CMD

### Installation

1. Repository klonen:

   ```bash
   git clone https://github.com/El-Tousy/glpi-docker-migration.git
   cd glpi-docker-migration
   ```

2. Container starten:

   ```powershell
   docker compose up -d
   ```

3. Dienste öffnen:
   - GLPI: [http://localhost:8080](http://localhost:8080)
   - phpMyAdmin: [http://localhost:8081](http://localhost:8081)

---

## Migrationsablauf

```mermaid
flowchart LR
    A[1. GLPI 11 bereitstellen<br/>leeres Schema] --> B[2. GLPI-10-Datenbank<br/>importieren]
    B --> C[3. Schema-Update<br/>auslösen]
    C --> D[4. Manuelle<br/>SQL-Korrekturen]
    D --> E[5. Migration<br/>überprüfen]
```

1. **Neuen Stack bereitstellen.** GLPI 11 frisch starten, damit das leere Schema angelegt wird.
2. **GLPI-10-Datenbank importieren** in den neuen Datenbank-Container (der Dump ist nicht Teil dieses Repositorys):

   ```powershell
   docker exec -i glpi-db mysql -u glpi -p glpi < glpi_backup_10.0.7.sql
   ```

3. **Schema-Update auslösen.** Beim Öffnen von GLPI im Browser wird die Versionsabweichung erkannt und der Upgrade-Prozess automatisch gestartet.
4. **Manuelle SQL-Korrekturen anwenden.** Einige Schemaunterschiede zwischen GLPI 10 und 11 erforderten manuelle Anpassungen, die über CMD vorgenommen wurden.
5. **Migration überprüfen.** Die Datenbankstruktur wurde in phpMyAdmin geprüft und die Funktionalität über das GLPI-Dashboard validiert.

---

## Autor

**El-Tousy** — Informatikstudentin, Marokko

[![GitHub](https://img.shields.io/badge/GitHub-181717?style=flat&logo=github&logoColor=white)](https://github.com/El-Tousy)
[![Email](https://img.shields.io/badge/Email-D14836?style=flat&logo=gmail&logoColor=white)](mailto:leilaeltousy@gmail.com)

---

## Lizenz

Veröffentlicht unter der MIT-Lizenz. Siehe [LICENSE](LICENSE) für Details.

---

<div align="center">

⭐ Wenn dir dieses Projekt nützlich erscheint, hinterlasse gerne einen Stern.

</div>
