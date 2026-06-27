---
plataforma: HTB
nombre: Fawn
ip: 10.129.65.217
os: UNIX
dificultad: Very Easy
fecha_inicio: 2026-06-27
fecha_fin: 27/06/2026
estado: Terminada
tags:
  - ctf
---
---
plataforma: HTB
nombre: Fawn
ip: 10.129.65.217
os: UNIX
dificultad: Very Easy
fecha_inicio: 2026-06-27
fecha_fin: 27/06/2026
estado: Terminada
tags:
  - ctf
---

# Fawn HTB

**IP objetivo:** `10.129.65.217`
**OS:** UNIX | **Dificultad:** Very Easy

---
## 1. Reconocimiento (Recon)

### Nmap
```bash
nmap -p- --min-rate=1000 -T4 -Pn 10.129.65.217
```
**Qué busco:** puertos abiertos, versiones de servicio, posibles vectores iniciales.
**Resultado / hallazgos:**
- Podemos ver que el puerto 21 (FTP) se encuentra abierto, por lo que vamos a atacarlo.

### Enumeración de servicios detectados

#### Puerto 21 - FTP
**Comando:**
```bash
nmap -p 21 -sC -sV -A -oN nmap_detallado_fawn.txt 10.129.65.217
```
**Por qué lo hago:** Analizamos específicamente el puerto 21 (FTP) para recabar información sobre este servicio y ver si supone un buen punto de intrusión.
**Qué encuentro:** El puerto 21 (FTP) se encuentra abierto y tiene el Anonymous FTP login allowed.

---
## 2. Enumeración (Enumeration)
### Otros servicios
```bash
PORT   STATE SERVICE VERSION
21/tcp open  ftp     vsftpd 3.0.3
| ftp-anon: Anonymous FTP login allowed (FTP code 230)
|_-rw-r--r--    1 0        0              32 Jun 04  2021 flag.txt
```

La versión del servicio es vsftpd 3.0.3, no es vulnerable per se; la vulnerabilidad radica en la configuración del login anónimo. Por lo tanto, el problema no se encuentra en el software en sí, sino en la configuración.

**Vector de entrada identificado:**
- Vamos a entrar y descargar el archivo encontrado, `flag.txt`, a través del anonymous FTP login.

---
## 3. Explotación / Foothold

**Vulnerabilidad explotada:** Anonymous FTP login allowed

**CVE / referencia (si existe):** N/A — vulnerabilidad de configuración, no de software

**Comando / exploit usado:**
```bash
ftp 10.129.65.217
Name (10.129.65.217:kali): anonymous
ls
get flag.txt
bye
```

**Por qué funciona esto:** Ha sido un proceso bastante simple, ya que al estar el anonymous FTP login activado no necesitamos credenciales. Esto permitiría a un atacante listar y descargar todos los archivos accesibles por el usuario `ftp` en el servidor.

**Captura – Acceso inicial**
ftp 10.129.65.217  
Connected to 10.129.65.217.  
220 (vsFTPd 3.0.3)  
Name (10.129.65.217:kali): anonymous  
331 Please specify the password.  
Password:  
230 Login successful.  
Remote system type is UNIX.  
Using binary mode to transfer files.

	**Usuario obtenido:** anonymous

---
## 4. Escalada de Privilegios (PrivEsc)

No aplica — la flag se obtiene directamente vía FTP anónimo, sin necesidad de escalar privilegios.

---
## 5. Flags

`HTB{redacted}` *(no se publica la flag real, por política de HackTheBox)*

---
## 6. Lecciones aprendidas / Mitigación

**Qué aprendí técnicamente:**
- Dejar el puerto FTP abierto es un riesgo, pero lo es mucho más si se deja el anonymous login allowed, ya que esto supone un riesgo de seguridad enorme: sería posible acceder al servidor y descargar sus archivos de forma anónima, sin credenciales.

**Cómo se mitigaría esto en un entorno real (esto es lo que un cliente paga por leer):**
- Desactivar el anonymous login en el servicio FTP y, si no es estrictamente necesario, mantener cerrado el puerto FTP (21). Como medida extra, activar Fail2Ban o PortSentry para banear IPs potencialmente dañinas.

**Conceptos del ciclo que entraron en juego:**
- Bastionado de Redes y Sistemas, Hacking Ético, Incidentes de Ciberseguridad

---
## 7. Tiempo invertido
- Recon: 2-3 mins
- Enumeración: <1 min
- Explotación: 2-3 mins
- PrivEsc: No aplica
- **Total:** 4-6 mins