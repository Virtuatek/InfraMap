# InfraMap CMDB – Server & Service Dependency Mapping

A lightweight, standalone web-based CMDB and visual architecture modeling tool designed to map, document, and analyze IT server systems, multi-homed network interfaces, and business service dependencies.

It runs **100% locally in your web browser** with zero installation, no backend server, and complete data privacy.

---

## 🎯 What Problem Does It Solve?

In enterprise IT environments, documenting which server communicates with which backend—and which business services fail when a host goes down—is notoriously difficult:

- **Scattered Excel Lists:** Server inventories in spreadsheets lack relational links, making dependency tracing error-prone and time-consuming.
- **Complex Multi-IP & Interface Setups:** Modern enterprise servers often have multiple network interfaces (LAN, Management/iLO, Cluster VIPs, Backup VLANs, DMZ), which traditional asset lists cannot model cleanly.
- **Impact Analysis Blindspots:** When patching a database or planning maintenance, IT teams struggle to identify all upstream consumers and affected business services.
- **Heavyweight CMDB Tooling:** Traditional enterprise CMDBs (ServiceNow, Jira Service Management) are complex to set up, require extensive licensing, and make quick architectural sketches difficult.

**InfraMap CMDB** bridges this gap: it offers both a searchable spreadsheet-style tabular view and an interactive 2D node graph with automatic layer layout, service filtering, live impact analysis, and bidirectional Excel/CSV & JSON project import/export.

---

## ✨ Key Features

- **🔒 100% Private & Client-Side:** Operates entirely inside your browser using HTML5, Vanilla JavaScript, and SVG. No telemetry, no external server calls, and local storage (`localStorage`) persistence.
- **⚡ Standalone & Zero-Install:** Just open [`src/it_infrastruktur_service_mapping.html`](file:///c:/Users/virtu/Meine%20Ablage/Meine%20Projekte/Projects/InfraMap/src/it_infrastruktur_service_mapping.html) in any modern browser. No build tools, Node.js, or database servers required.
- **🔄 Dual-View Workflows:**
  - **Tabular / List View (`Listenansicht`):** Search, filter, and manage systems across environments (PROD, TEST, DEV), roles, and services.
  - **Interactive Graphical Topology (`Grafische Übersicht & Services`):** Infinite pan/zoom SVG canvas, draggable server cards, and visual flow lines.
- **🌐 1-to-N Multi-IP & Interface Modeling:** Assign multiple IP addresses per server with custom network interface labels (e.g., `Cluster VIP`, `PROD / LAN`, `MGMT / iLO`, `Backup VLAN`, `DMZ / WAN`).
- **🔗 Interactive Port-to-Port Wiring:** Drag connection cables directly from the outgoing port of any server card to a destination host, binding protocols (HTTPS, SQL, LDAPS, SAP HANA) and target interfaces.
- **📐 Layered Auto-Layout:** Organize all systems instantly according to enterprise architectural tiers: `User` ➔ `Web-Server` ➔ `App-Server` ➔ `DB-Server` ➔ `Infrastruktur` ➔ `Anlage`.
- **🎯 Service Isolation & Viewport Grouping:** Select any business service to highlight its entire dependency chain and group the participating servers neatly in view with one click.
- **🔍 Draggable Impact Analysis & Inspector Panel:** Click any server to inspect its incoming connections (upstream consumers), outgoing dependencies (downstream backends), affected business services, assigned owners, and direct `mailto:` links.
- **📊 Real-Time KPI Dashboard:** Instant counters for Total Servers, Production (PROD) Systems, Total Connections, and Defined IT-Services.
- **📂 Flexible Import & Export:**
  - **Project JSON Backup (`.json`):** Complete state backup including graph node coordinates and service mappings.
  - **Excel / CSV Import (`.csv`, `.txt`):** Smart column detection, automatic encoding fallback (UTF-8 / Windows-1252 ANSI), and merge/replace options.
  - **Excel / CSV Export:** One-click export with UTF-8 BOM for seamless Microsoft Excel compatibility.
- **🧪 Built-in Enterprise Demo Data:** Load a realistic Swiss enterprise dataset (SAP S/4HANA ERP, Customer Portal & E-Shop, Document Management, Core IT & Directory) at any time.

---

## 🏛️ Architecture Tiers & Role Schema

InfraMap categorizes infrastructure nodes into six standard architectural layers:

| Role / Tier | Color Code | Tier Order | Typical Systems & Examples | Icon / Purpose |
| :--- | :--- | :---: | :--- | :--- |
| **User** | Cyan | 0 | Client devices, workstations, user portals, edge clients | End users and consumer access points |
| **Web-Server** | Purple | 1 | Reverse proxies, WAF, Nginx, Apache httpd, HAProxy, API Gateways | DMZ ingress, SSL termination, and traffic routing |
| **App-Server** | Blue | 2 | Tomcat, Node.js APIs, SAP NetWeaver, microservices, container hosts | Business logic and application processing |
| **DB-Server** | Emerald | 3 | MS SQL Server, SAP HANA, PostgreSQL, Oracle, MySQL clusters | Core transactional data and storage engines |
| **Infrastruktur** | Amber | 4 | Active Directory (DC), DNS, NTP, LDAP, monitoring, backup appliances | Core IT foundation and directory services |
| **Anlage** | Orange | 5 | Industrial OT, IoT controllers, manufacturing plants, facility systems | Operational technology and smart devices |

---

## 🏷️ Network Interfaces & Multi-IP Color Badges

Each IP address assigned to a system can carry a dedicated interface label:

| Label Classification | Badge Accent | Typical Use Cases |
| :--- | :--- | :--- |
| **PROD / LAN** | Blue | Primary production network interface |
| **Cluster VIP / HA** | Purple | Virtual IP address for failover clusters (AlwaysOn, Pacemaker, Keepalived) |
| **MGMT / iLO / iDRAC** | Amber | Out-of-band management controllers, SSH, administrative interfaces |
| **Backup / Storage / SAN** | Cyan | Dedicated backup network, iSCSI, NAS/NFS mounts, VLAN isolation |
| **DMZ / WAN / Public** | Rose | Public-facing VIPs, external firewall interfaces, WAN links |

---

## 📋 System Object Schema & Data Fields

The system stores each infrastructure object with the following schema, shared across CSV and JSON:

| Field | CSV Column Name | Type | Required | Description & Format |
| :--- | :--- | :--- | :---: | :--- |
| `hostname` | **Hostname** | Text | **Yes** | Unique system name; acts as primary key during CSV import |
| `owners` | **Verantwortliche** | List | No | 1–n email addresses (`firstname.lastname@domain.com`), separated by ` \| ` |
| `desc` | **Beschreibung** | Text | No | Short description, data center rack, or physical location |
| `ips` | **IP-Adressen** | List | **Yes** | 1–n IP addresses separated by ` \| ` |
| `ipLabels` | **Netzwerk-Label** | List | No | 1–n matching labels corresponding to the IPs, separated by ` \| ` |
| `role` | **Typ** | Choice | **Yes** | `User`, `Web-Server`, `App-Server`, `DB-Server`, `Infrastruktur`, `Anlage` |
| `env` | **Umgebung** | Choice | **Yes** | `PROD`, `TEST`, `DEV` |
| `os` | **Betriebssystem** | Text | No | Operating system name and version (e.g. `RHEL 9.4`, `Windows Server 2022`) |
| `apps` | **Applikationen** | List | No | Comma-separated list of installed applications and roles |
| `services` | **Services** | List | No | Assigned business services (new services are auto-created upon import) |
| `connections` | **Verknüpfungen** | List | No | Outgoing links: `TARGETHOST [Protocol / Port] @Target-IP` separated by ` \| ` |
| `comment` | **Kommentar** | Text | No | Internal maintenance notes, licensing info, or contacts |

---

## 📖 How to Use

### 1. Launch the Application
Open [`src/it_infrastruktur_service_mapping.html`](file:///c:/Users/virtu/Meine%20Ablage/Meine%20Projekte/Projects/InfraMap/src/it_infrastruktur_service_mapping.html) directly in any modern web browser.

### 2. Explore or Load Data
- **Try Enterprise Demo Data:** Click **"Beispieldaten"** in the top navigation to load a pre-configured multi-tier architecture with SAP S/4HANA, WAF, databases, and Active Directory.
- **Import from Excel / CSV:** Click **"CSV Importieren"** and drag your CSV file into the drop zone. Choose whether to **merge/update** or **replace** the inventory.
- **Restore a Backup:** Click **"Projekt laden (.json)"** to resume work from a saved project file.
- **Start from Scratch:** Click **"Daten leeren (0)"** to clear all systems for an initial setup.

### 3. Manage Systems in Tabular View (`Listenansicht`)
- Use the **search bar** to filter by hostname, IP address, OS, or application.
- Filter by **Role**, **Environment** (`PROD`, `TEST`, `DEV`), or **Business Service**.
- Click **"Neues System erfassen"** to add a server, enter multiple IPs with labels, and assign services.
- Click **"Bearbeiten"** on any row to modify its properties, or click **"Verknüpfung"** to jump into the dependency builder.

### 4. Interactive Architecture Mapping (`Grafische Übersicht & Services`)
- Switch to the graphical view using the view toggle in the header.
- **Move Systems:** Drag and drop server cards anywhere on the grid canvas.
- **Wire Connections:** Hover over the right port of a server card (blue dot) and drag a line over to any destination server. Enter the port/protocol (e.g. `HTTPS / 443`, `SQL / 1433`) and choose the target IP interface.
- **Auto-Layout:** Click **"Auto-Layout (Schichten)"** to instantly arrange all servers into clean architecture columns.
- **Navigate Canvas:** Click and drag the grid background to pan; use the mouse wheel or zoom buttons to zoom in/out.

### 5. Service Dependency & Impact Analysis
- In the left sidebar under **"IT-Services"**, click any service (e.g., *SAP S/4HANA ERP*).
- The canvas dims unrelated systems and lights up the active service path with animated dependency arrows.
- Click **"Im Sichtfeld gruppieren"** to automatically position all involved servers into view.
- Click any server node to open the **floating Inspector window** to see immediate impact details:
  - Which business services will fail if this server goes down?
  - Who connects to it (upstream)?
  - What does it depend on (downstream)?
  - One-click copy for IP addresses and email links for system owners.

### 6. Export & Save
- **Save Project:** Click **"Projekt speichern (.json)"** to create a complete JSON snapshot including all canvas coordinates.
- **Export to Excel:** Click **"CSV / Vorlage"** ➔ **"Serverliste als CSV"** to download an Excel-ready semicolon-delimited CSV file with UTF-8 BOM.

---

## 🔒 Security & Data Privacy

- **No Server Dependencies:** InfraMap runs purely on the client side without communicating with any external cloud or server.
- **Strict Data Confidentiality:** All infrastructure names, internal IP addresses, network topologies, and passwords/notes remain strictly within your local browser.
- **Zero Installation Footprint:** Can be placed on a secure internal file share, USB drive, or intranet web server without requiring administrative installation rights.

---

## 📁 Repository Structure

```text
InfraMap/
├── README.md                                 # Project documentation and guide (this file)
└── src/
    └── it_infrastruktur_service_mapping.html # Standalone application (HTML + Tailwind + JS + SVG)
```