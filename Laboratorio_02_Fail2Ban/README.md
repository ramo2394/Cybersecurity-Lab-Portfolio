# 🛡️ Laboratorio 02: Protección contra Fuerza Bruta con Fail2Ban

## 📝 Descripción
Implementación y configuración de un sistema de prevención de intrusiones (IPS) utilizando **Fail2Ban** en un entorno Linux. El objetivo es mitigar ataques de fuerza bruta sobre el servicio SSH mediante el bloqueo dinámico de direcciones IP sospechosas.

---

## 🛠️ Fases del Proyecto

### 1. Instalación y Troubleshooting de Configuración
* Se procedió con la instalación del servicio y la creación del archivo de personalización `jail.local`.
* **Error detectado**: El servicio presentó un estado de `failed` inicialmente debido a conflictos en la sintaxis de configuración.
* **Resolución**: Se utilizó la herramienta de testeo `fail2ban-server -t`, logrando validar la configuración y corregir los errores de carga del socket.

### 2. Configuración de la "Cárcel" (Jail) SSH
Se establecieron políticas estrictas de seguridad para la detección de intrusos:
* **maxretry = 3**: Límite de intentos de inicio de sesión antes del baneo.
* **findtime = 10m**: Ventana de tiempo para contabilizar los fallos.
* **bantime = 1h**: Duración del castigo/bloqueo de la IP.
* **backend = systemd**: Configuración para una monitorización nativa de logs.

### 3. Simulación de Ataque de Fuerza Bruta
Para verificar la eficacia del sistema, se simuló un ataque utilizando **Hydra**:
* **Comando ejecutado**: `hydra -l victim -p password123 ssh://10.124.62.194`.
* **Resultado del ataque**: Tras superar los 3 intentos fallidos, el servidor cortó la conexión automáticamente, mostrando un error de `Connection refused` en la consola del atacante.

---

## 📊 Evidencias de Funcionamiento

### Estado de la Cárcel tras el Ataque
Al verificar el estado de la celda `sshd`, se confirma la captura de la IP atacante:

| Métrica | Resultado |
| :--- | :--- |
| **Intentos Fallidos Totales** | 3 |
| **IPs Baneadas Actualmente** | 1 |
| **IP Identificada** | `10.124.62.194` (Ataque Loopback) |

> **Nota de Seguridad**: Se observó que el sistema es altamente reactivo, bloqueando el tráfico a nivel de firewall (IPTables/NFTables) inmediatamente tras el tercer fallo.

---

## 💡 Lecciones Aprendidas
* **Gestión de Whitel
