# Laboratorio 4: Auditoría de Seguridad y Hardening con Lynis

Este laboratorio describe el proceso de endurecimiento (hardening) de un servidor Linux, utilizando la herramienta de auditoría **Lynis**. A través de tres fases de mejora, se logró elevar el **Hardening Index de 56 a 64**.

---

## 📊 Resumen de Evolución
* **Fase 1 (Inicio):** 56 puntos.
* **Fase 2 (Actualización y Acceso):** 61 puntos.
* **Fase 3 (Seguridad Activa):** 64 puntos.

---

## 🛠️ Fase 1: Auditoría Inicial (Index: 56)
En la primera ejecución de Lynis, el sistema presentaba vulnerabilidades críticas en el software y configuraciones de acceso por defecto.

### **Hallazgos principales:**
* **Vulnerabilidades de software:** Presencia de paquetes desactualizados con fallos de seguridad conocidos.
* **Configuración de red:** Firewall inactivo o sin reglas aplicadas.
* **Acceso:** Ausencia de banners legales y límites de autenticación laxos.

---

## 🔧 Fase 2: Parcheo y Fortalecimiento de Acceso (Index: 61)
En esta etapa, nos centramos en eliminar las vulnerabilidades de los paquetes y asegurar el acceso remoto.

### **1. Gestión de Vulnerabilidades**
* **Comando:** `sudo apt update && sudo apt upgrade -y`
* **¿Qué es?:** Actualización del repositorio y de los paquetes instalados.
* **¿Para qué sirve?:** Instala las últimas versiones de seguridad del software. Se resolvieron **84 paquetes vulnerables** identificados por Lynis.

### **2. Hardening de SSH**
* **Comando:** `MaxAuthTries 3` (en `/etc/ssh/sshd_config`)
* **¿Qué es?:** Configuración del servicio de acceso remoto Secure Shell.
* **¿Para qué sirve?:** Limita el número de intentos fallidos de contraseña. Al ponerlo en 3, dificultamos los ataques de "fuerza bruta" que intentan adivinar contraseñas.

### **3. Implementación de Banners Legales**
* **Comando:** `echo "Aviso Legal" | sudo tee /etc/issue`
* **¿Qué es?:** Archivo de configuración que muestra un mensaje antes del login.
* **¿Para qué sirve?:** Desde un punto de vista legal y de cumplimiento, advierte que el acceso está restringido a personal autorizado, lo cual es fundamental en auditorías profesionales.

### **4. Herramientas de Auditoría Auxiliares**
* **Comandos:** `apt-listchanges`, `apt-listbugs`, `debsums`.
* **¿Para qué sirven?:** Permiten verificar la integridad de los paquetes instalados y avisar de fallos críticos antes de realizar futuras instalaciones.

---

## 🛡️ Fase 3: Monitorización y Seguridad de Red (Index: 64)
La fase final consistió en activar las defensas del sistema y asegurar que los parches de seguridad estén operativos.

### **1. Activación del Kernel Parcheado**
* **Comando:** `sudo reboot`
* **¿Qué es?:** Reinicio completo del sistema operativo.
* **¿Para qué sirve?:** Durante la Fase 2 se instaló un nuevo núcleo (Kernel) del sistema, pero no entró en funcionamiento hasta el reinicio. Esto resolvió el warning de discrepancia de versión del Kernel.

### **2. Configuración del Firewall (UFW)**
* **Comandos:** `sudo ufw allow ssh` y `sudo ufw enable`
* **¿Qué es?:** Uncomplicated Firewall (cortafuegos).
* **¿Para qué sirve?:** Cierra todos los puertos del servidor excepto el de SSH. Esto impide que cualquier otro servicio no autorizado pueda recibir conexiones desde el exterior.

### **3. Auditoría de Eventos (Auditd)**
* **Comando:** `sudo apt install auditd acct -y`
* **¿Qué es?:** Sistema de auditoría del Kernel de Linux.
* **¿Para qué sirve?:** Registra de forma detallada quién hace qué en el sistema (cambios de archivos, intentos de login, ejecución de comandos), proporcionando una trazabilidad completa.

### **4. Escaneo de Malware (ClamAV)**
* **Comando:** `sudo apt install clamav -y`
* **¿Qué es?:** Motor antivirus de código abierto.
* **¿Para qué sirve?:** Permite realizar escaneos periódicos en busca de archivos maliciosos o troyanos, cumpliendo con uno de los requisitos de seguridad activa de Lynis.

---

## 🏁 Conclusión
El proceso de Hardening permitió elevar el nivel de seguridad del servidor en un **14%**, pasando de un estado vulnerable a un entorno controlado, monitorizado y con políticas de acceso restrictivas.
