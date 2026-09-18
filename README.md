# 🌦️ Weather Station Supervision Dashboard 

![Header](https://capsule-render.vercel.app/api?type=waving&color=0:0D1117,100:1a4fa0&height=200&section=header&text=Supervision%20Dashboard&fontSize=38&fontColor=FFFFFF&animation=fadeIn)

> A full admin dashboard for real-time monitoring of weather stations, built during my technical internship (2026).
> **Note:** for confidentiality reasons, the source code and database schema are not published in this repository. This page documents the project, the stack, the architecture, and what I learned while building it.

[![PHP](https://img.shields.io/badge/PHP-777BB4?style=flat&logo=php&logoColor=white)](https://www.php.net/)
[![MySQL](https://img.shields.io/badge/MySQL-4479A1?style=flat&logo=mysql&logoColor=white)](https://www.mysql.com/)
[![XAMPP](https://img.shields.io/badge/XAMPP-FB7A24?style=flat&logo=xampp&logoColor=white)](https://www.apachefriends.org/)
[![Adminer](https://img.shields.io/badge/Adminer-34567C?style=flat&logoColor=white)](https://www.adminer.org/)
[![FileZilla](https://img.shields.io/badge/FileZilla-BF0000?style=flat&logo=filezilla&logoColor=white)](https://filezilla-project.org/)
[![SQL](https://img.shields.io/badge/SQL-4479A1?style=flat&logoColor=white)](#)

---

## Screenshots
 
| Global Overview & Map | Stations List | Alarms Management |
|---|---|---|
| ![Dashboard overview with station map](Screenshots/dashboard-map.png) | ![Stations list](Screenshots/stations-list.png) | ![Alarms management](Screenshots/alarms.png) |

---

## Table of Contents

- [Context](#context)
- [Overview](#overview)
- [Screenshots](#screenshots)
- [Features](#features)
- [Technical Challenges & Learnings](#technical-challenges--learnings)
- [Tech Stack](#tech-stack)
- [Confidentiality Note](#confidentiality-note)
- [Author](#author)

---

## Context

This project was developed during my technical internship, for a company operating in the **weather monitoring / IoT sensor network** field. The goal was to build a centralized **web-based supervision dashboard** for technical teams to monitor a fleet of remote weather stations in real time: sensor readings, alarms, station health, and user access management.

I worked on this project end-to-end: from understanding the existing multi-database backend, to designing new PHP pages, to building the front-end dashboard (maps, KPIs, alarms, notifications, role-based navigation).

---

## Overview

The dashboard is the single entry point for the technical/support team. From it, an operator can:

- See at a glance how many stations are online, how many alarms are active, and whether all backend services are healthy
- Locate every station on an interactive map, color-coded by status
- Drill down into a specific station's variables, history, and alarms
- Manage users, roles, and permissions
- Track notifications in real time (new alarms, events)

---

## Features

### 📊 Global Dashboard (KPIs)
- Live counters: number of stations, variables tracked, active alarms, registered users, projects
- Database health indicator (X/Y services operational, as % uptime)
- Auto-refresh and "last generated at" timestamp

### 🗺️ Interactive Station Map
- Built with **Leaflet.js**, plots every geolocated station with a color-coded marker:
  - 🟢 Active (last seen < 1h)
  - 🟠 Recently active (< 24h)
  - ⚪ Inactive
  - 🔴 Has an active alarm
- Click a station for a popup with quick links to its detailed graphs and attributes
- Auto-fit bounds so the map always frames all visible stations

### 🚨 Alarms & Notifications
- Real-time active alarms table, linked to the station that triggered them
- Top-navigation notification bell with unread count, mark-as-read (single or bulk), fetched asynchronously
- Dedicated alarms management pages (create, edit, delete)

### 👥 Users, Roles & Permissions
- Role-based access control (RBAC): each user is assigned a role, and each role is granted access to specific modules/pages
- The sidebar navigation is **dynamically filtered** server-side based on the logged-in user's permissions — a user only ever sees the modules they're allowed to access
- A "super admin" fallback role sees everything

### 📈 Calculated Variables & Auto-validation
- A separate service handles derived/calculated variables (formulas applied to raw sensor data), each with a trigger type
- A separate auto-validation service flags anomalies in incoming data automatically

### 🖥️ Admin Tools
- System configuration page
- Data extraction / reporting tools
- Reference data ("referentiels") management

---

A single `DatabaseManager`-style class (singleton pattern, one PDO connection per database, lazily instantiated) centralizes all connections and exposes a `testAllConnections()` health-check used to power the "X/Y databases operational" KPI on the dashboard.

Each page follows a simple **template/layout pattern**: a page script builds its own data and HTML fragment (via `ob_start()`), then hands it off to a shared layout file that renders the sidebar, top navigation, permissions check, and footer around it — keeping shared UI logic in one place instead of duplicating it across dozens of pages.

---

## Technical Challenges & Learnings

- **Working across five separate databases instead of one.** I had to design a clean connection layer so every page could pull from the right database without duplicating connection logic, and make sure a single slow/down service didn't break the whole dashboard — each data-fetching block is wrapped defensively so the page still renders (with partial data) even if one service fails.

- **Building a real role-based permission system.** Rather than hardcoding "if admin show X", permissions are loaded from the database per role and used to filter *both* what's visible in the sidebar *and* what's actually accessible if someone tries to hit a page URL directly. Getting the "super admin sees everything, everyone else sees only what their role allows" logic right, without duplicating checks everywhere, took a few iterations.

- **Real-time-feeling notifications without a websocket stack.** Notifications (new alarms/events) are polled and marked read/unread through lightweight AJAX calls, updating the badge and dropdown instantly without a full page reload — a good lesson in doing "real-time enough" UX with simple tools when a full push-based architecture isn't justified.

- **Rendering a live, color-coded map performantly.** With potentially hundreds of stations, I had to be careful about how markers, popups, and status colors were computed and injected, and make sure the map gracefully handles the case of zero geolocated stations instead of showing a broken/empty map.

- **Keeping the UI consistent across dozens of pages.** Using a shared layout with page-specific content injected into it forced me to think about which state belongs in each page (title, breadcrumbs, extra CSS/JS) versus what's truly global — a useful lesson in separating concerns in a language (PHP) that doesn't enforce it for you.

---

## Tech Stack

| Layer | Technology |
|---|---|
| Backend | PHP (PDO, prepared statements) |
| Database | MySQL (5 separate service databases) |
| Frontend | Bootstrap, jQuery, Font Awesome |
| Maps | Leaflet.js |
| Auth | PHP Sessions, role-based permissions |
| Tooling | XAMPP (local dev environment) |

---

## Confidentiality Note

This project was developed for a company during my internship. **The source code, database schema, and any real data are intentionally not published here** to respect the company's confidentiality and intellectual property. This README describes the system, my role, the architecture, and the technical challenges I worked through, without exposing proprietary code or data.

---

## Author

- GitHub: [@El-Tousy](https://github.com/El-Tousy)
- Contact: via [Email](https://mail.google.com/mail/?view=cm&fs=1&to=leilaeltousy@gmail.com)

---

## License

This project is distributed under the MIT License – see the [LICENSE](LICENSE) file for more details.

---

⭐ If you like this project, feel free to leave it a star!
