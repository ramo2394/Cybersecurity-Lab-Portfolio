cat << 'EOF' > Laboratorio3_HIDS_Completo.md
# Laboratorio 3: Implementación de HIDS y Detección de Intrusiones con Wazuh

## 1. 🏗️ Arquitectura y Preparación del Sistema
En este laboratorio se ha desplegado un sistema de monitorización basado en la arquitectura **Wazuh (SIEM/HIDS)** sobre un entorno virtualizado.

### 💻 Especificaciones del Entorno
* **Servidor de Monitorización:** Wazuh Dashboard/Manager.
* **Agente:** Ubuntu Server (`srv-linux-01`).
* **Hipervisor:** VirtualBox.

### 🛠️ Resolución de Conflictos (Troubleshooting de Recursos)
Uno de los principales retos fue la limitación de memoria RAM para ejecutar el agente y el manager simultáneamente. 
* **Problema:** El servicio de Wazuh consumía la totalidad de la RAM, provocando inestabilidad en el sistema.
* **Solución:** Se implementó una configuración de **Memoria Swap** adicional para permitir que el motor de integridad funcionara sin colapsar el kernel.
* **Configuración:** Ajuste de parámetros en `/etc/fstab` y activación de espacio de intercambio manual.

---

## 2. 🔍 Monitorización de Integridad (HIDS - FIM)
La monitorización de integridad de archivos (**File Integrity Monitoring**) se configuró para vigilar cambios en tiempo real en los directorios de configuración del sistema.

### Configuración de Syscheck
Se editó el archivo `/var/ossec/etc/ossec.conf` en el agente para añadir las siguientes directivas:
* Monitoreo en tiempo real de `/etc`, `/usr/bin` y `/var/log`.
* Comprobación de sumas de verificación (checksum) y cambios en los permisos de archivos.

### Prueba de Concepto (PoC)
1. **Inyección de archivo:** Se creó el archivo `/etc/test_intruso.sh` mediante `sudo touch`.
2. **Detección:** El SIEM generó automáticamente una alerta de **Nivel 5/7** bajo la descripción **"File added to the system"**.
3. **Validación:** Se confirmó que Wazuh identifica no solo la creación, sino la identidad del usuario que escaló privilegios para realizar la acción.

---

## 🛡️ 3. Simulación de Intrusión y Honeypot
El objetivo de esta fase era capturar ataques de red externos. Durante el proceso, nos enfrentamos a desafíos técnicos que documentamos a continuación.

### Incidencias en la Obtención de Herramientas
Intentamos desplegar **Pentbox** para actuar como Honeypot, pero nos encontramos con errores críticos de red:
* **Error 404/403:** El repositorio oficial de GitHub no estaba disponible o denegaba la conexión.
* **Decisión Técnica:** Ante la imposibilidad de descargar software externo, se procedió a realizar un **Ataque de Fuerza Bruta Manual** sobre el servicio SSH para simular el comportamiento de un atacante real.

### Análisis de Alertas de Red (Forense Digital)
Al realizar múltiples intentos de acceso fallidos desde una IP externa, el Dashboard de Wazuh reportó una cadena de eventos de alta criticidad:

| Regla (Rule ID) | Nivel de Riesgo | Descripción del Evento |
| :--- | :---: | :--- |
| **5760** | 5 | **sshd: authentication failed**: Registro de intento fallido individual. |
| **5758** | 8 | **Maximum attempts exceeded**: Bloqueo del servicio por seguridad. |
| **2502** | **10 (CRÍTICO)** | **User missed password more than once**: Detección de patrón de ataque. |



---

## 🚀 4. Conclusiones Técnicas
1. **Efectividad del SIEM:** Wazuh ha demostrado ser capaz de correlacionar eventos simples (un fallo de login) para identificar amenazas complejas (fuerza bruta de Nivel 10).
2. **Importancia de la Monitorización de Integridad:** Sin FIM, un atacante podría modificar scripts en `/etc` sin dejar rastro aparente en los logs estándar.
3. **Resiliencia de Infraestructura:** El uso de **Swap** fue determinante para mantener la visibilidad de seguridad en un entorno de bajos recursos.

EOF
