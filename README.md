# Reinforcement-Activity-2.2---IoT-Connectivity
# Diseño y Validación de Conectividad IoT para la Manufactura de Piezas Automotrices T-Alem

## 1. Diseño del Componente de Conectividad

### 1.1 Comprensión de Nuestro Proceso

Nuestro proyecto se centra en dos procesos críticos en T-Alem, una empresa especializada en la fabricación y distribución de componentes de caucho para vehículos:

1. **Gestión de Inventario**: Actualmente, el conteo de materiales es manual, lo que genera ineficiencias y errores humanos. Se implementarán **sensores de peso** en cada contenedor, enviando datos a una **Raspberry Pi central** que gestiona el inventario en tiempo real.

2. **Control de Calidad**: Actualmente depende de inspecciones visuales. Implementaremos **cámaras de visión artificial** conectadas a la **Raspberry Pi principal**, ejecutando modelos de **IA para detección de defectos**.

Ambos procesos se beneficiarán de la conectividad IoT mediante:
- Transmisión en **tiempo real** de datos desde sensores y cámaras.
- Reducción de la intervención humana y el error.
- Generación de alertas instantáneas para inventario bajo o defectos detectados.
- Registro de datos históricos para **optimización y análisis**.
- Mejora en **eficiencia operativa** y **reducción de desperdicio**.

---

## 1.2 Selección de la Red IoT

Dado que ahora **Raspberry Pi** será el **nodo central de procesamiento**, hemos adoptado una **arquitectura híbrida de conectividad IoT** usando **Wi-Fi y Zigbee**:

- **Wi-Fi (IEEE 802.11)**: Conexión principal para el servidor, cámaras y acceso a la nube.
- **Zigbee (IEEE 802.15.4)**: Para sensores de peso y otros dispositivos de bajo consumo.

### 📌 Ventajas de la integración de Zigbee:
✅ **Menor consumo energético**: Ideal para sensores con batería.  
✅ **Comunicación en malla**: Mejora la cobertura sin infraestructura extra.  
✅ **Alta escalabilidad**: Permite conectar cientos de sensores sin congestión.  

### 📌 Implementación:
- **Sensores de peso con Zigbee**: Módulos **CC2530/XBee** conectados a la Raspberry Pi.
- **Coordinador Zigbee**: La **Raspberry Pi actuará como coordinador Zigbee** con un **Zigbee HAT**.
- **Cámaras de inspección**: Conectadas directamente a la Raspberry Pi mediante **Wi-Fi o Ethernet**.

---

## 1.3 Protocolos de Comunicación

| Protocolo | Capa | Propósito | 
|----------|------|-----------|
| **CoAP** | Aplicación | Comunicación eficiente con sensores | 
| **MQTT** | Aplicación | Comunicación entre Raspberry Pi y la nube | 
| **Wi-Fi (802.11)** | Física/Enlace | Conexión de cámaras y backend | 
| **Zigbee (IEEE 802.15.4)** | Física/Enlace | Sensores de peso | 
| **JSON** | Formato de datos | Estructuración de datos |

### 📌 Implementación de CoAP

CoAP complementará a MQTT en la comunicación con sensores Zigbee, permitiendo:
- ✅ **Menor consumo de ancho de banda** al usar UDP.
- ✅ **Modelo RESTful** (`GET`, `POST`, `PUT`, `DELETE`).
- ✅ **Menor latencia** en comparación con MQTT.

📌 **En la Raspberry Pi:**  
- Instalación de **aiocoap**: `pip install aiocoap`
- Sensores enviarán datos **CoAP → Coordinador Zigbee → Raspberry Pi**
- Raspberry Pi retransmitirá **CoAP → MQTT → Backend**

---

## 1.4 Arquitectura de Hardware

Nuestra infraestructura de hardware basada en **Raspberry Pi + Zigbee**:

### 📌 **1. Nodo Central - Raspberry Pi (Servidor IoT)**
- **Modelo:** Raspberry Pi 4B (4GB o 8GB RAM).
- **Función:** Procesar datos de sensores, cámaras e integrarse con la nube.
- **Conexiones:** Wi-Fi para acceso remoto y Zigbee HAT para comunicación con sensores.

### 📌 **2. Sensores de Peso**
- **Hardware:** ESP32 + Zigbee CC2530 + HX711 + Celdas de carga.
- **Función:** Medir peso y enviar datos a la Raspberry Pi a través de CoAP.
- **Alimentación:** Batería LiPo con panel solar.

### 📌 **3. Cámaras de Inspección de Calidad**
- **Hardware:** Raspberry Pi Camera Module v2 o USB Camera.
- **Función:** Captura y análisis de imágenes con modelos de visión artificial.

### 📌 **4. Coordinador Zigbee**
- **Hardware:** Raspberry Pi + Zigbee HAT.
- **Función:** Actúa como puente entre sensores Zigbee y el backend.

### 📌 **5. Gateway Wi-Fi-Zigbee**
- **Hardware:** ESP32 + módulo Zigbee.
- **Función:** Convertir mensajes CoAP de los sensores en MQTT para la nube.

---

## 2. Validación del Diseño

### 📌 2.1 Simulación en Cisco Packet Tracer

Probamos la infraestructura de red en **Cisco Packet Tracer**, validando:
1. **Conectividad entre nodos Zigbee y la Raspberry Pi.**
2. **Tiempo de respuesta de CoAP y MQTT.**
3. **Consumo energético de sensores de peso.**

## Anexo de Cisco Packet Tracer : 
---

## 3. Selección de Opciones

| Componente | Opción  | 
|------------|---------------|
| **Nodo Central** | Raspberry Pi 4B |
| **Red** | Zigbee (IEEE 802.15.4) + Wi-Fi |
| **Protocolo** | CoAP + MQTT |
| **Consumo Energético** | Bajo con Zigbee |
| **Cobertura** | Expansible con malla Zigbee |
| **Procesamiento** |Alto (Raspberry Pi) |

Con esta optimización, logramos:

✅ **Mayor potencia de procesamiento con Raspberry Pi.**  
✅ **Menor consumo en sensores con Zigbee.**  
✅ **Menor latencia y uso de red con CoAP.**  

---

## 4. Resultados Esperados

Con la integración basada en **Raspberry Pi + Zigbee + CoAP**, esperamos:

- 📉 **Reducción del 90%** en tiempo de conteo manual de inventario.
- 📊 **Precisión del 85%+** en monitoreo de inventario.
- 🤖 **Detección de defectos del 60%+** en inspección de calidad.
- 🔄 **Optimización en procesos** con monitoreo en tiempo real.

---

## 5. Referencias de Apoyo

1. Raspberry Pi Foundation. (2023). **Raspberry Pi 4 Model B Technical Specifications**. Retrieved from [Raspberry Pi](https://www.raspberrypi.org/documentation/)
2. Texas Instruments. (2023). **CC2530 Zigbee Module Datasheet**.
3. Naik, N. (2017). **Choice of effective messaging protocols for IoT systems**. IEEE.

---
# Acta

## Distribución de Actividades  
Las tareas fueron distribuidas **equitativamente** entre los tres integrantes, asegurando una contribución balanceada en cada fase del trabajo.  

| Fecha | Actividad | Responsable |
|-------|-----------|------------|
| **23/03/2025** | Revisión del problema y planteamiento del proyecto | Oscar, John, Santiago |
| **24/03/2025** | Investigación sobre protocolos de red IoT (MQTT, CoAP, Zigbee) | Oscar, John, Santiago |
| **25/03/2025** | Definición de arquitectura: Nodo central (Raspberry Pi), Sensores (ESP32+Zigbee) | Oscar, John, Santiago |
| **26/03/2025** | Desarrollo de la wiki con especificaciones técnicas | Oscar, John, Santiago |
| **27/03/2025** | Implementación del prototipo en **Cisco Packet Tracer** | Oscar, John, Santiago |
| **28/03/2025** | Revisión final, ajustes y documentación | Oscar, John, Santiago |

---

## Desarrollo del Trabajo  

### 📌 Investigación y Diseño de Red  
- Se analizaron las opciones de conectividad para IoT, comparando **Wi-Fi, Zigbee y LPWAN**.  
- Se optó por **Raspberry Pi como nodo central**, eliminando el ESP32 como controlador principal.  
- Se adoptó un modelo híbrido con **Zigbee para sensores de bajo consumo y Wi-Fi para la conexión al backend**.  

### 📌 Documentación y Desarrollo de la Wiki  
- Se redactó la **wiki técnica** con la arquitectura propuesta, protocolos y hardware seleccionado.  
- Se detallaron las ventajas de **Zigbee sobre Wi-Fi** para los sensores de peso.  
- Se implementó el uso de **CoAP** para comunicación eficiente con sensores IoT.  

### 📌 Simulación en Cisco Packet Tracer  
- Se construyó un **modelo de red** en **Cisco Packet Tracer** para validar la conectividad. 

---
# Integrantes
Oscar David Vergara Moreno

Santiago Gavilán Páez

John Jairo Rojas Vergara
