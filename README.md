# ExamenFinalRedes
Examen Final Redes
https://github.com/Pizarman13/ExamenFinalRedes.git

# Parte 1 – Misiones de Conocimiento Teórico

## Misión 1: Reconexión en la Base Eco (Hoth) – Direccionamiento IP y Subredes 

### Esquema de Subredes para la Base Eco (Hoth)

Partiendo de la red **172.16.0.0/24**, aplicamos VLSM para cubrir las necesidades de cada departamento y el enlace troncal:

| Segmento                      | Subred            | Máscara               | Hosts útiles | Rango de hosts               |
|-------------------------------|-------------------|-----------------------|--------------|------------------------------|
| **Comando Central** (≈50 h.)  | 172.16.0.0 /26    | 255.255.255.192       | 62           | 172.16.0.1 – 172.16.0.62     |
| **Defensa Perimetral** (≈30 h.) | 172.16.0.64 /27   | 255.255.255.224       | 30           | 172.16.0.65 – 172.16.0.94    |
| **Centro Médico** (≈20 h.)    | 172.16.0.96 /27   | 255.255.255.224       | 30           | 172.16.0.97 – 172.16.0.126   |
| **Hangar y Taller** (≈14 h.)  | 172.16.0.128 /28  | 255.255.255.240       | 14           | 172.16.0.129 – 172.16.0.142  |
| **Enlace Troncal** (2 h.)     | 172.16.0.144 /30  | 255.255.255.252       | 2            | 172.16.0.145 – 172.16.0.146  |

### Detalle de cada subred

1. **Comando Central**  
   - **Subred**: `172.16.0.0/26`  
   - Soporta hasta 62 hosts (ideal para ~50 dispositivos).  
   - Broadcast: `172.16.0.63`.  

2. **Defensa Perimetral**  
   - **Subred**: `172.16.0.64/27`  
   - Soporta hasta 30 hosts (para ~30 dispositivos).  
   - Broadcast: `172.16.0.95`.  

3. **Centro Médico**  
   - **Subred**: `172.16.0.96/27`  
   - Soporta hasta 30 hosts (para ~20 dispositivos).  
   - Broadcast: `172.16.0.127`.  

4. **Hangar y Taller**  
   - **Subred**: `172.16.0.128/28`  
   - Soporta hasta 14 hosts (para ~14 dispositivos).  
   - Broadcast: `172.16.0.143`.  

5. **Enlace Troncal Interplanetario**  
   - **Subred**: `172.16.0.144/30`  
   - Soporta 2 hosts (enlace punto a punto).  
   - Broadcast: `172.16.0.147`.  

> El rango **172.16.0.148 – 172.16.0.255** queda libre para futuras expansiones o VLANs adicionales.

---

## Misión 2: Sabiduría de Yoda – Algoritmos de Enrutamiento y Rutas

### 1. Enrutamiento Estático  
En el enrutamiento estático, el administrador configura manualmente cada ruta en el router.

**Ventajas**  
- **Control total**: Cada camino está definido explícitamente.  
- **Simplicidad**: Sin cálculos de protocolos ni anuncios periódicos.  
- **Bajo uso de recursos**: CPU y memoria mínimas.

**Inconvenientes**  
- **Escalabilidad limitada**: Cada nuevo cambio exige intervención manual.  
- **Falta de tolerancia a fallos**: No se actualiza ante caídas de enlace.  
- **Propenso a errores**: Un dígito mal puede romper toda la ruta.

### 2. Enrutamiento Dinámico  
Los routers intercambian información de topología para descubrir y mantener rutas automáticamente.

**Protocolos comunes**  
- **RIP (Routing Information Protocol)**: Vector de distancia que usa saltos como métrica.  
- **OSPF (Open Shortest Path First)**: Estado de enlace que usa el algoritmo de Dijkstra.

**Ventajas**  
- **Adaptabilidad**: Recalcula rutas al caer nodos.  
- **Escalabilidad**: Crece bien en redes medianas/grandes.  
- **Menor gestión manual**: Menos errores humanos.

**Inconvenientes**  
- **Consumo de recursos**: CPU, memoria y ancho de banda para anuncios.  
- **Complejidad**: Configuración de temporizadores, métricas y áreas.  
- **Convergencia**: Tiempo hasta estabilizar tras un cambio (RIP lento, OSPF más rápido).

### 3. Vector de Distancia vs. Estado de Enlace  

| Característica       | Vector de Distancia (RIP)            | Estado de Enlace (OSPF)                      |
|----------------------|--------------------------------------|----------------------------------------------|
| **Visión de la red** | Conoce solo vecinos y distancia       | Mapa completo de la topología                |
| **Métrica**          | Saltos (máx. 15 en RIP)               | Costo (ancho de banda, retardo, carga…)      |
| **Intercambio**      | Actualizaciones completas periódicas  | Solo LSAs ante cambios                       |
| **Convergencia**     | Lenta; “count-to-infinity” posible    | Rápida; Dijkstra recalcula eficientemente    |
| **Recursos**         | Bajo CPU/mem, alto ancho de banda     | Alto CPU/mem, ancho de banda optimizado      |
| **Escalabilidad**    | Redes pequeñas (≤15 saltos)           | Medianas/grandes (usa áreas en OSPF)         |

### 4. Reacción ante caída de un nodo

- **Estático**: Sin reconfiguración, las rutas siguen intentando el camino caído → **pérdida de tráfico**.  
- **RIP**: Detecta la inaccesibilidad tras varios intervalos (hasta 180 s) → **convergencia lenta**.  
- **OSPF**: Envía un LSA indicando enlace muerto → todos los routers recalculan en segundos → **convergencia rápida**.  

> **Conclusión:**  
> - Para **enlaces fijos y pocas modificaciones**, el enrutamiento estático es sencillo y fiable.  
> - Para **redes grandes o dinámicas**, el enrutamiento dinámico (RIP u OSPF) ofrece resiliencia y escalabilidad.  
> “El camino estático, seguro es; el dinámico, poderoso y adaptable es.” — Maestro Yoda.  

---

## Misión 3: Los Nombres del Holonet – DNS y Resolución de Nombres

### 1. ¿Qué es el DNS?  
El **Sistema de Nombres de Dominio** (DNS) es la “guía telefónica” de Internet (o de la HoloRed): traduce nombres de dominio legibles (como `holonet.rebelion.org`) en direcciones IP numéricas (por ejemplo, `203.0.113.42`) que entienden los routers y servidores.

### 2. ¿Cómo funciona la resolución de nombres?  
1. **Cliente DNS (resolver)**  
   - Un equipo (ordenador, router o servidor) que necesita saber la IP de un dominio envía una **consulta** a un servidor DNS.
2. **Servidor DNS recursivo**  
   - Recibe la petición y, si no tiene la respuesta en caché, pregunta a los servidores de nivel superior (root → TLD → autoritativo).
3. **Servidor DNS autoritativo**  
   - Contiene los **registros** para un dominio concreto (p. ej. la zona `rebelion.org`).
4. **Respuesta**  
   - La IP encontrada se devuelve al cliente y, opcionalmente, se guarda en caché por un tiempo (TTL).

### 3. ¿Qué es un servidor DNS y un registro A?  
- **Servidor DNS**: Máquina que almacena y responde consultas sobre nombres de dominio.  
- **Registro A**: Apunta un nombre de dominio a una dirección IPv4.

| Campo     | Ejemplo                     | Descripción                                      |
|-----------|-----------------------------|--------------------------------------------------|
| Nombre    | `holonet.rebelion.org.`     | Dominio cuya IP queremos conocer                 |
| Tipo      | `A`                         | Indica que es un registro de dirección IPv4      |
| Dirección | `203.0.113.42`              | IP a la que apunta el nombre                    |
| TTL       | `3600`                      | Tiempo (en segundos) que el registro se puede cachear |

#### Ejemplo de consulta  
$ dig holonet.rebelion.org A

; <<>> DiG 9.16.1 <<>> holonet.rebelion.org A
;; ANSWER SECTION:
holonet.rebelion.org. 3600 IN A 203.0.113.42

### 4. ¿Qué ocurre si el servidor DNS no está disponible?

- Sin resolución de nombres, la Alianza no puede traducir dominios a IPs.
- Las aplicaciones que usan nombres de host (navegadores, SSH, API REST) no conectan, aunque la red física exista.
- Sólo funcionarán las conexiones directas por IP, reduciendo drásticamente la flexibilidad y escalabilidad de las comunicaciones rebeldes.

---

## Misión 4: “Es una trampa… de protocolos!” – TCP vs UDP en las transmisiones

### 1. Protocolo TCP (Transmission Control Protocol)  
- **Orientado a conexión**: Antes de enviar datos, cliente y servidor realizan un “handshake” de tres vías (SYN, SYN-ACK, ACK).  
- **Fiabilidad**:  
  - Control de flujo y congestión (ventana deslizante).  
  - Numeración de secuencias y acuse de recibo (ACK).  
  - Retransmisión de paquetes perdidos.  
- **Entrega ordenada**: Los segmentos se reordenan en destino.  
- **Rendimiento**: Mayor latencia y overhead (cabeceras más grandes, retransmisiones), pero ideal cuando la integridad de datos es crítica.

### 2. Protocolo UDP (User Datagram Protocol)  
- **Sin conexión**: No hay handshake previo.  
- **No fiable**:  
  - No hay ACK ni retransmisiones.  
  - Los datagramas pueden llegar duplicados, desordenados o perderse.  
- **Ligero y rápido**: Cabecera mínima (8 bytes), sin control de flujo ni congestión.  
- **Rendimiento**: Latencia muy baja y overhead reducido, perfecto cuando la velocidad prima sobre la fiabilidad.

### 3. Comparativa TCP vs UDP  

| Característica            | TCP                                      | UDP                            |
|---------------------------|------------------------------------------|--------------------------------|
| **Conexión**              | Orientado a conexión (handshake)         | Sin conexión (stateless)       |
| **Fiabilidad**            | Garantiza entrega completa y ordenada    | No garantiza entrega ni orden  |
| **Control de flujo**      | Sí (ventana deslizante)                  | No                             |
| **Retransmisiones**       | Sí, automática ante pérdidas             | No                             |
| **Cabecera**              | 20–60 bytes                              | 8 bytes                        |
| **Latencia**              | Mayor, por ACKs y retransmisiones        | Muy baja                       |
| **Uso típico**            | Transferencia de ficheros, correo, SSH   | Voz/​vídeo en tiempo real, juegos |

### 4. Ejemplos galácticos de uso  

- **TCP**  
  - Transmisión de los **planos de la Estrella de la Muerte**: cada bit debe llegar íntegro y en orden.  
  - **Envío de informes tácticos** al Almirante Ackbar o actualizaciones de inventario de suministros.  

- **UDP**  
  - **Coordenadas de combate en tiempo real** desde el X-Wing de Luke: si se pierde uno de cada cien paquetes, la formación sigue operativa.  
  - **Streaming de vídeo** de la cámara de un droide astromecánico durante una misión de reconocimiento: prioridad máxima a la latencia baja.  

### 5. Conclusión  
- Usare **TCP** cuando la **integridad y el orden** de los datos importen (planes, archivos, mensajes críticos).  
- Usare **UDP** cuando la **velocidad y la baja latencia** sean vitales (telemetría de combate, voz/vídeo en vivo).

---


## Misión 5: Comunicación Segura o Lado Oscuro – Criptografía y Seguridad de la Red

### 1. Cifrado Simétrico  
- **Definición**: Usa **una misma clave secreta compartida** para cifrar y descifrar mensajes.  
- **Funcionamiento**:  
  1. Leia y Luke acuerdan una frase clave (p. ej. “RebeliónViva!”).  
  2. Leia cifra su mensaje con esa clave y envía el texto cifrado.  
  3. Luke recibe el mensaje y, usando la misma frase clave, lo descifra.  
- **Ventajas**:  
  - Muy rápido y eficiente en CPU.  
  - Ideal para comunicaciones frecuentes entre partes que ya comparten la clave.  
- **Inconvenientes**:  
  - El canal de intercambio de claves debe ser seguro (si el Imperio la descubre, todo queda comprometido).  
  - Difícil de escalar a muchos aliados (¿cómo repartir la clave con cientos de bases?).

### 2. Cifrado Asimétrico (Pública/Privada)  
- **Definición**: Cada usuario genera un **par de claves**:  
  - **Clave pública** (se comparte abiertamente)  
  - **Clave privada** (se guarda en secreto)  
- **Funcionamiento**:  
  1. Un nuevo aliado genera su par de claves y publica su clave pública en el directorio rebelde.  
  2. La Alianza cifra el mensaje con la clave pública del aliado.  
  3. Solo el aliado, con su clave privada, puede descifrarlo.  
- **Ventajas**:  
  - **No se requiere canal secreto** para intercambio de claves.  
  - Escalable: cualquiera puede cifrar usando la clave pública sin exponer secretos.  
- **Inconvenientes**:  
  - Más lento que el cifrado simétrico; se suele usar combinado (p.ej. intercambio de una clave simétrica vía RSA, luego AES para la comunicación).

### 3. Autenticación y No Repudio  
- **Autenticación**: Garantiza que **quien envía** el mensaje sea realmente quien dice ser.  
  - Se puede lograr mediante **firmas digitales** (el emisor firma el mensaje con su clave privada; cualquiera verifica con su clave pública).  
- **No repudio**: Impide que el emisor **niegue** haber enviado un mensaje firmado.  
  - La firma digital también sirve para no repudio: solo el poseedor de la clave privada pudo haberla generado.

### 4. Uso de Protocolos Seguros  
- **Por qué SSH en lugar de Telnet**:  
  - **SSH** cifra toda la sesión de administración remota, protegiendo credenciales y comandos.  
  - **Telnet** transmite en texto claro, de modo que el Imperio podría capturar usuario/contraseña y órdenes.  
- **Recomendación Rebelde**: Siempre usar **canales cifrados** (SSH, HTTPS, VPN) al administrar routers, servidores y enlaces críticos de la red.

> “Con la Fuerza de la criptografía, el Imperio doblegado será. Sin seguridad, en la oscuridad caeremos.” — Mon Mothma  

---

# Parte II – Misiones Prácticas con Cisco Packet Tracer 




