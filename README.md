# 🛡️ Mi Portfolio de Ciberseguridad

Bienvenido a mi repositorio de laboratorios prácticos. Aquí documento mi progreso en seguridad ofensiva y defensiva.

## 🚀 Proyectos Completados

### 1. Hardening SSH con MFA
Configuración de seguridad en servidores Ubuntu 24.04 utilizando Google Authenticator.
* 🔗 [Ver Laboratorio Detallado](./Laboratorio_01_MFA_SSH/README.md)

### 2. Prevención de Fuerza Bruta con Fail2Ban
Detección y bloqueo automático de ataques de diccionario mediante análisis de logs.
* 🔗 [Ver Laboratorio Detallado](./Laboratorio_02_Fail2Ban/README.md)

### 3. Monitorización de Integridad (HIDS) y SIEM con Wazuh
Implementación de un sistema de detección de intrusos basado en host (HIDS) para monitorizar cambios en archivos críticos y ataques de red en tiempo real. 
* 🔗 [Ver Laboratorio Detallado](./Laboratorio_03_HIDS/README.md)

### 4. Auditoría de Seguridad y Hardening con Lynis

Configuración y gestión de memoria virtual (Swap) en caliente para garantizar la estabilidad del sistema ante picos de carga de trabajo.
* 🔗 [Ver Laboratorio Detallado](./Laboratorio_04_Lynis/README.md)

### 5. Resiliencia, Automatización y Alertas con Telegram

Implementación de un sistema de observabilidad proactiva mediante bots de Telegram, control de retención de logs (Journald) y backups automatizados con verificación de integridad.
* 🔗 [Ver Laboratorio Detallado](./Laboratorio_05_Backup/README.md)

---

## 🛠️ Tecnologías y Herramientas Utilizadas

* **Sistemas Operativos & Kernel:** Ubuntu Server 24.04 LTS, Gestión de Memoria Virtual (Swap), Estructura de archivos de Linux.
* **Seguridad & Hardening:** Lynis (Auditoría de Seguridad), Wazuh (SIEM/HIDS), Fail2Ban, Google Authenticator (MFA), Syscheck (FIM).
* **Automatización & Scripting:** Bash Scripting (Automatización de tareas), Crontab (Programación de procesos), API de Telegram (Bots para notificaciones push).
* **Gestión de Almacenamiento & Logs:** Systemd-journald (Control de retención de registros), Tar/Gzip (Estrategias de compresión y backup), Gestión de cuotas de disco.
* **Redes & Monitorización:** SSH (Hardening de acceso), Curl (Comunicación con APIs externas), Análisis forense de logs en tiempo real.
