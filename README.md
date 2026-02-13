# Cybersecurity-Lab-Portfolio
Laboratorios prácticos de ciberseguridad, defensa de servidores y análisis de vulnerabilidades.

# 🛡️ Laboratorio de Ciberseguridad: Auditoría y Hardening SSH

Este repositorio documenta un laboratorio completo de seguridad ofensiva y defensiva sobre un servidor Linux (Ubuntu 24.04). El objetivo fue demostrar cómo una configuración débil es vulnerable y cómo implementar medidas de mitigación profesionales.

---

## 🏗️ 1. Fase Ofensiva: Explotación de Vulnerabilidades

### 1.1 Identificación del Objetivo
Se identificó un usuario `victim` con una política de contraseñas débil.

### 1.2 Ataque de Fuerza Bruta con Hydra
Se realizaron varias iteraciones de ataque para validar la seguridad del servicio SSH:

* **Primer Intento (Parámetro `-p`):** Se utilizó el parámetro de contraseña estática para confirmar que la clave `12345` permitía el acceso inmediato.
  
   `hydra -l victim -p 12345 ssh://[IP]`
* **Segundo Intento (Ataque de Diccionario):** Se empleó un archivo de texto con una lista de contraseñas. 
    * **Nota Técnica:** Durante esta fase se corrigió el uso del parámetro `-P` (mayúscula) necesario para cargar archivos de diccionario, frente al parámetro `-p` (minúscula) usado para una única contraseña.
      
   `hydra -l victim -P /ruta/al/diccionario.txt ssh://[IP]`
* **Resultado:** El acceso fue obtenido en **1.05 segundos**.

---

## 🛠️ 2. Fase Defensiva: Implementación de MFA

Para mitigar el riesgo de ataques de diccionario, se implementó la Autenticación de Doble Factor (2FA/MFA).

### 2.1 Configuración de Google Authenticator
Se instaló el módulo PAM y se vinculó un dispositivo físico (Smartphone) mediante un secreto criptográfico único (QR).

Instalación del módulo de Google Authenticator:
`sudo apt update && sudo apt install libpam-google-authenticator`

Configuración del segundo factor para el usuario:
`google-authenticator`
(Aquí es donde respondes "y" a las preguntas y escaneas el código QR).

Configuración del módulo PAM:
`sudo nano /etc/pam.d/sshd`
Línea añadida: `auth required pam_google_authenticator.so nullok`  

**NOTA:** nullok permite que los usuarios sin configuración 2FA puedan loggearse al servidor. Aquellos usuarios con 2FA deberán introducir el codigo de verificación de google. En el caso que solo se quiera permitir a usuarios con el 2FA el parametro nullok se debe eliminar.

Configuración del servidor SSH:
`sudo nano /etc/ssh/sshd_config`
Parámetro modificado: `KbdInteractiveAuthentication yes`

Reinicio del servicio para aplicar cambios:
`sudo systemctl restart ssh`
* **Algoritmo:** TOTP (Time-based One-Time Password).
* **Medida de Seguridad:** Se configuró el servidor para que el token sea de un solo uso y caduque cada 30 segundos.

---

## 🧠 3. Resolución de Incidentes (Troubleshooting)

La implementación no fue directa y requirió la resolución de problemas críticos de administración de sistemas:

1. **Timeout de Red:** Se detectaron bloqueos en la conexión que requirieron la revisión del firewall (**UFW**) y el estado del servicio `sshd`.
2. **Conflicto de Configuración PAM:** Se identificó una redundancia en el archivo `/etc/pam.d/sshd` (línea duplicada) que causaba errores de "Access Denied" persistentes.
3. **Sincronización Horaria (Time-Skew):** Se descubrió que el servidor tenía un desfase de 1 hora respecto al cliente MFA. Al ser un protocolo basado en tiempo (TOTP), el acceso era rechazado. 
    * **Solución:** Sincronización mediante `timedatectl set-ntp true` y ajuste a la zona horaria `Europe/Madrid`.

---

## 🏁 4. Resultado Final y Hardening

El servidor quedó configurado con una política de seguridad híbrida:

* **Protección de Administrador (`ramo`):** Requiere obligatoriamente Password + MFA.
* **Flexibilidad de Usuario (`victim`):** Se utilizó el parámetro `nullok` en el módulo PAM para permitir el acceso a usuarios que aún no han configurado su MFA, evitando el bloqueo total de la operativa.
* **Seguridad de Red:** Firewall UFW activo limitando el tráfico exclusivamente al puerto 22/TCP.

### Validación de Acceso Exitoso:
---
**Laboratorio finalizado con éxito el 13 de febrero de 2026.**
