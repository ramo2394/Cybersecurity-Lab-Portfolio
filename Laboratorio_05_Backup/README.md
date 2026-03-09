# 🛡️ Laboratorio 5: Resiliencia, Automatización y Alertas Críticas

En este laboratorio, he transformado un servidor Linux estándar en un sistema **proactivo y resiliente**. El objetivo principal fue solucionar problemas de almacenamiento derivados de logs masivos y establecer un canal de comunicación en tiempo real con el administrador.

## 📋 Objetivos Técnicos
* **Observabilidad**: Recibir alertas push en dispositivos móviles ante eventos críticos.
* **Mantenimiento Autónomo**: Limitar el crecimiento de logs del sistema para proteger el espacio en disco.
* **Disaster Recovery**: Implementar y validar una estrategia de backups automatizados.

---

## 🛠️ 1. Sistema de Alertas (Telegram API Integration)
Se ha desarrollado un "mensajero" para el servidor utilizando la API de bots de Telegram. Esto permite que el servidor informe de su estado sin intervención humana.

* **Script de Alerta (`/usr/local/bin/alertar.sh`)**:
    Este script centraliza el envío de mensajes mediante `curl`.
    ```bash
    #!/bin/bash
    TOKEN="8763886056:AAG3u8wCOGcqiQOOgSTHkVOIuZ9fOC*****"
    ID="24320****"
    MENSAJE=$1
    curl -s -X POST [https://api.telegram.org/bot$TOKEN/sendMessage](https://api.telegram.org/bot$TOKEN/sendMessage) -d chat_id=$ID -d text="🖥️ [SRV-LINUX-01]: $MENSAJE" > /dev/null
    ```

---

## 🧹 2. Gestión de Logs (Journald Optimization)
Para evitar que el disco se llene (incidente previo de 13GB en logs), se ha configurado el demonio de registro del sistema.

* **Configuración en `/etc/systemd/journald.conf`**:
    Se activó el parámetro `SystemMaxUse=500M`.
* **Resultado**: El sistema ahora autogestiona su historial, manteniendo un máximo de 500 MB y rotando los archivos más antiguos automáticamente.



---

## 📦 3. Estrategia de Backup y Notificación
Se ha implementado un sistema de respaldo para las configuraciones (`/etc`) y los datos de usuario (`/home/ramo`).

* **Script de Backup (`/usr/local/bin/hacer_backup.sh`)**:
    Automatiza la compresión y envía una notificación de éxito o error al móvil.
    ```bash
    #!/bin/bash
    FECHA=$(date +%Y-%m-%d_%H-%M)
    ARCHIVO="/var/backups/backup_sistema_$FECHA.tar.gz"

    # Proceso de compresión
    tar -czf $ARCHIVO /etc /home/ramo 2>/dev/null

    # Verificación y Alerta
    if [ -f $ARCHIVO ]; then
        /usr/local/bin/alertar.sh "📦 BACKUP OK: $ARCHIVO generado correctamente."
    else
        /usr/local/bin/alertar.sh "❌ ERROR: Fallo en la creación del backup."
    fi
    ```
* **Automatización**: Tarea programada en `cron` para ejecutarse cada madrugada a las 03:00.

---

## 🔐 4. Monitorización de Accesos (SSH Alerts)
Como medida de seguridad, se ha configurado el servidor para notificar inmediatamente cualquier inicio de sesión SSH.

* **Implementación**: Inserción del script de alerta en el archivo `/etc/profile`.
* **Efecto**: Cada acceso exitoso dispara un mensaje a Telegram con el usuario y la IP de origen, permitiendo detectar intrusiones al instante.
A diferencia de los logs pasivos, se ha implementado un sistema de **alertas activas** para detectar intrusiones en el momento exacto en que ocurren.

* **Mecanismo Técnico**: 
    Linux ejecuta automáticamente los scripts definidos en `/etc/profile` cada vez que una sesión interactiva es iniciada por cualquier usuario. Al insertar el script de alerta en este flujo, la notificación se dispara inmediatamente después de una autenticación exitosa, pero **antes** de que el usuario reciba el control de la terminal (`shell`).
* **Uso de Variables de Entorno**:
    El sistema captura dinámicamente datos críticos mediante variables del sistema:
    * `$USER`: Identifica el nombre de la cuenta que ha accedido.
    * `$SSH_CLIENT`: Extrae la **dirección IP de origen**, el puerto remoto y el puerto local de la conexión.
* **Implementación**:
    ```bash
    # Línea añadida al final de /etc/profile
    /usr/local/bin/alertar.sh "⚠️ ACCESO: Usuario [$USER] conectado desde IP [$SSH_CLIENT]"
    ```
* **Efecto**: Auditoría inmediata. Si un atacante vulnera la contraseña, el administrador recibe una notificación push con la IP del intruso antes de que este pueda manipular los logs locales.
---

## 🧪 Prueba de Recuperación (Restauración Real)
Para validar el sistema, se realizó un simulacro de borrado accidental del archivo `~/tesoro.txt`.

1.  **Borrado**: `rm ~/tesoro.txt`
2.  **Restauración**: Se extrajo el archivo específico desde el último backup generado:
    ```bash
    sudo tar -xzf /var/backups/backup_sistema_[FECHA].tar.gz -C / home/ramo/tesoro.txt
    ```
3.  **Resultado**: El archivo fue recuperado con éxito, validando el **RTO (Recovery Time Objective)** en menos de 1 minuto.



---

**Estado Final**: El servidor es ahora robusto, autogestionado y capaz de alertar ante incidentes de seguridad o mantenimiento.
---

### 📸 Evidencia de Funcionamiento (Telegram Dashboard)

A continuación se muestra una captura real de las notificaciones push recibidas en el dispositivo móvil, confirmando la integración exitosa de los scripts de backup y el sistema de alertas de acceso SSH:

![Captura de Notificaciones del Bot de Telegram](URL_DE_TU_FOTO_AQUÍ)

*En la imagen se pueden observar las alertas de "BACKUP OK" y los avisos de "ACCESO DETECTADO" con la IP de origen.*
