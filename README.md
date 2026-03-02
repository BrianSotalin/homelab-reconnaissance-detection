🛡️ Cybersecurity Home Lab: Network Reconnaissance & Detection
Este proyecto documenta la implementación de un laboratorio de monitoreo de seguridad diseñado para identificar y analizar actividades de reconocimiento (escaneo de puertos) mediante el análisis de logs del sistema en un entorno controlado.

🚀 Escenario del Laboratorio
El objetivo es simular un ataque de reconocimiento desde una máquina host externa hacia un servidor víctima, configurando un firewall para registrar la actividad y analizando los logs resultantes.

Atacante: macOS (Host)

Víctima: Kali Linux (VirtualBox VM)

Herramientas: Nmap, UFW (Uncomplicated Firewall), Journalctl.

🛠️ Configuración de Red e Infraestructura
Para lograr la conectividad entre el Host y la VM, se utilizó un Bridged Adapter (Adaptador Puente).

💡 Lección Aprendida (Troubleshooting)
Durante la fase de configuración, se identificó que el tráfico era bloqueado al utilizar una red pública (Biblioteca), debido a restricciones de seguridad del router (aislamiento de clientes). La solución fue cambiar a un Personal Hotspot, permitiendo la asignación correcta de IPs en el mismo segmento y la comunicación directa entre dispositivos.

IP Atacante (macOS): 172.20.10.3

IP Víctima (Kali): 172.20.10.7

🔍 Implementación del SOC
1. Hardening de la Víctima
Se configuró el firewall en Kali Linux para bloquear y registrar intentos de conexión no autorizados:

Bash
sudo ufw enable
sudo ufw logging medium
2. Simulación de Ataque (Reconnaissance)
Desde el Host (macOS), se ejecutó un escaneo sigiloso de puertos comunes:

Bash
nmap -F 172.20.10.7
3. Análisis de Evidencias (Log Analysis)
Se utilizó journalctl para monitorizar el kernel en tiempo real y capturar la evidencia del escaneo:

Bash
sudo journalctl -kf | grep -i ufw
Evidencia capturada:
Mar 02 14:28:53 kali kernel: [UFW BLOCK] IN=eth0 OUT= MAC=... SRC=172.20.10.3 DST=172.20.10.7 ... PROTO=TCP DPT=80 ... SYN

📊 Interpretación Técnica
Evento: Intento de conexión TCP bloqueado.

Análisis del Flag: La presencia del flag SYN sin un ACK posterior indica un intento de inicio de conexión, característico de un escaneo TCP SYN Stealth.

Puerto de Destino (DPT): El objetivo fue el puerto 80 (HTTP), indicando una fase de enumeración de servicios web.

🎓 Conclusiones
Este laboratorio demuestra la capacidad de configurar perímetros de seguridad básicos y la importancia de la visibilidad de logs para la detección temprana de intrusiones en la fase de reconocimiento del Cyber Kill Chain.
