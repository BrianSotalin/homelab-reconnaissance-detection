
# 🛡️ Cybersecurity Home Lab: Network Reconnaissance & Detection

Este repositorio documenta un laboratorio práctico de **SOC (Security Operations Center) Nivel 1**. El objetivo principal es la detección y análisis de actividades de reconocimiento de red (Port Scanning) mediante la monitorización de logs del sistema en un entorno virtualizado.

## 🚀 Escenario del Laboratorio
Simulación de un ataque de reconocimiento desde una máquina host externa hacia un servidor víctima.

* **Atacante (Host):** macOS (Arquitectura ARM/Intel) ejecutando `Nmap`.
* **Víctima (VM):** Kali Linux v2024.x (VirtualBox) protegida por `UFW`.
* **Software de Seguridad:** UFW (Uncomplicated Firewall) v0.36.2.

## 🛠️ Configuración de Red e Infraestructura
Para asegurar la visibilidad total entre las máquinas, se configuró un **Adaptador Puente (Bridged Adapter)**.

### 💡 Lección de Resolución de Problemas (Troubleshooting)
Durante el despliegue, se identificó que el tráfico era bloqueado al utilizar una red pública (Biblioteca), debido al aislamiento de clientes (Client Isolation) configurado en el router. 
* **Solución:** Se realizó el cambio a un **Personal Hotspot**, permitiendo la comunicación directa de capa 2 entre el Host y la VM.

**Direccionamiento IP:**
* **Atacante (macOS):** `172.20.10.3`
* **Víctima (Kali):** `172.20.10.7`

---

## 🔍 Implementación y Detección

### 1. Hardening de la Víctima
Se activó el firewall en la máquina víctima con una política de denegación por defecto y un nivel de registro medio para capturar los metadatos de los paquetes:
```bash
sudo ufw enable
sudo ufw logging medium
```
### 2. Ejecución del Ataque (Reconnaissance)
Desde el terminal de macOS, se lanzó un escaneo de puertos rápidos para identificar servicios activos:
```bash
nmap -F 172.20.10.7
```
### 3. Análisis de Logs (Perspectiva SOC)
Debido a que las versiones modernas de Kali utilizan systemd-journald, se utilizó journalctl para la extracción de evidencias en tiempo real:

```bash
sudo journalctl -kf | grep -i ufw
```
# 📊 Análisis de Evidencias Capturadas
Log de ejemplo obtenido:
```bash
Mar 02 14:28:53 kali kernel: [UFW BLOCK] IN=eth0 OUT= MAC=08:00:27:0a:fc:6e:3a:ca:80:47:bd:a0:08:00 SRC=172.20.10.3 DST=172.20.10.7 LEN=64 TOS=0x00 PREC=0x00 TTL=255 ID=0 DF PROTO=TCP SPT=50378 DPT=80 WINDOW=65535 RES=0x00 CWR ECE SYN URGP=0
```

Hallazgos clave:
Identificación del Atacante: La dirección IP de origen (172.20.10.3) coincide con el host externo sospechoso.

Protocolo y Técnica: El uso de PROTO=TCP con el flag SYN indica un intento de inicio de conexión ("Handshake"), típico de un escaneo de tipo Stealth SYN Scan.

Objetivo: El puerto destino (DPT=80) revela que el atacante intentaba enumerar un servicio web (HTTP).

## 🎓 Conclusión
Este laboratorio demuestra el ciclo de vida de un incidente en fase de reconocimiento: desde la configuración del perímetro de seguridad y la resolución de problemas de red, hasta la captura e interpretación técnica de logs para la toma de decisiones en un entorno SOC.
