# Reinforcement-Activity-2.2---IoT-Connectivity
# Diseño y Validación de Conectividad IoT para la Manufactura de Piezas Automotrices T-Alem

## 1. Diseño del Componente de Conectividad

### 1.1 Comprensión de Nuestro Proceso

Nuestro proyecto de aplicación se centra en dos procesos críticos en T-Alem, una empresa especializada en la fabricación y distribución de componentes de caucho para vehículos:

1. **Gestión de Inventario**: Actualmente es un proceso manual que requiere tiempo significativo del personal (aproximadamente 3 horas diarias) y es propenso a errores humanos. Nuestra solución implementará sensores de peso debajo de cada contenedor de almacenamiento para rastrear automáticamente los niveles de inventario en tiempo real.

2. **Control de Calidad**: Actualmente depende de la inspección visual subjetiva por parte del personal, lo que lleva a inconsistencias. Nuestra solución implementará cámaras de visión artificial con algoritmos de IA para detectar defectos automáticamente.

Ambos procesos se beneficiarán enormemente de la conectividad IoT mediante:
- Habilitación de transmisión de datos en tiempo real desde sensores a sistemas centrales
- Reducción de la intervención humana y el error
- Provisión de alertas inmediatas para inventario bajo o problemas de calidad
- Creación de datos históricos para análisis y optimización
- Mejora de la eficiencia operativa y la calidad del producto

### 1.2 Selección del Tipo de Red

Para nuestra implementación en T-Alem, hemos seleccionado una **red basada en Wi-Fi con ESP32** como núcleo de nuestra arquitectura:

- **Red Wi-Fi existente**: Utilizaremos la infraestructura Wi-Fi ya disponible en la planta de fabricación
- **Red de dispositivos ESP32**: Aprovecharemos la capacidad Wi-Fi integrada de las placas ESP32 para todos nuestros sensores y dispositivos de control

Este enfoque práctico proporciona los siguientes beneficios:
- Reutilización de infraestructura de red existente, reduciendo costos
- Facilidad de implementación al usar un tipo de microcontrolador unificado (ESP32)
- Flexibilidad para ampliar la red añadiendo más dispositivos ESP32 según sea necesario
- Capacidad de procesamiento local en cada nodo gracias a las características del ESP32
- Implementación rápida y económica, ideal para esta etapa del proyecto 

### 1.3 Protocolos de Red

Después de evaluar diversos protocolos IoT considerando nuestra infraestructura basada en ESP32, hemos seleccionado:

| Protocolo | Capa | Propósito | Implementación |
|----------|-------|---------|----------------|
| **MQTT** | Aplicación | Transmisión de datos principal | Biblioteca PubSubClient para ESP32 |
| **HTTP/REST** | Aplicación | Interfaz de usuario web | Servidor web integrado en ESP32 |
| **Wi-Fi (IEEE 802.11)** | Física/Enlace | Conectividad principal | Módulo Wi-Fi integrado en ESP32 |
| **JSON** | Formato de datos | Estructuración de datos | ArduinoJson para serialización/deserialización |

#### 1.3.1 Implementación de MQTT

MQTT será nuestro protocolo principal debido a que ofrece: 

1. **Soporte nativo en ESP32**: La disponibilidad de bibliotecas como PubSubClient hace que la implementación sea directa y bien documentada.

2. **Uso Eficiente de Recursos**: MQTT está diseñado para ser liviano y consumir poco ancho de banda, ideal para dispositivos con recursos limitados como los ESP32.

Nuestra implementación de MQTT incluye:
- Un broker MQTT Mosquitto ejecutándose en una Raspberry Pi o PC como servidor central
- Clientes MQTT implementados en cada ESP32 utilizando la biblioteca PubSubClient
- Estructura de temas organizada por:
  - `/inventario/[id_contenedor]/peso`
  - `/inventario/[id_contenedor]/alerta`
  - `/calidad/[id_camara]/inspeccion`
  - `/calidad/[id_camara]/defecto`
- Autenticación básica para asegurar las comunicaciones

### 1.4 Arquitectura de Hardware basada en ESP32

Nuestros dispositivos ESP32 se utilizarán de diferentes maneras según la función:

1. **Nodos de Sensores de Peso**:
   - ESP32 + HX711 + Celda de carga
   - Alimentación: Batería LiPo con panel solar pequeño para carga
   - Función: Medir el peso de los contenedores y enviar datos al broker MQTT

2. **Nodos de Control de Calidad**:
   - ESP32-CAM (variante con cámara integrada)
   - Alimentación: Conexión a la red eléctrica
   - Función: Capturar imágenes de las piezas y realizar análisis básico de imagen

3. **Nodo Coordinador**:
   - ESP32 estándar conectado a un Raspberry Pi
   - Alimentación: Conexión a la red eléctrica
   - Función: Actuar como puente entre los sensores y el sistema de gestión

## 2. Validación del Diseño

### 2.1 Simulación en Cisco Packet Tracer

Validamos nuestro diseño de conectividad utilizando Cisco Packet Tracer para simular toda la infraestructura de red antes de la implementación física.

#### Simulación

#### Desafíos Encontrados

Durante la validación, encontramos y resolvimos varios desafíos:

1. **Alcance Wi-Fi limitado**: Algunas áreas del almacén mostraban señal débil.

2. **Consumo de batería**: Los ESP32 enviando datos constantemente agotaban la batería rápidamente.

3. **Limitaciones de procesamiento**: El análisis de imágenes en ESP32-CAM resultó limitado.

### 3.3 Resultados Esperados

Con esta implementación basada en ESP32, esperamos lograr:

- 90% de reducción en el tiempo de conteo manual de inventario
- Precisión del 85%+ en el seguimiento de inventario
- Tasa de detección de defectos básicos del 60%+ en control de calidad
- Visibilidad en tiempo real de niveles de inventario críticos
- Demostración funcional del concepto para una implementación mayor en el futuro

## 4. Referencias de Apoyo

1. Espressif Systems. (2023). ESP32 Series Datasheet. Retrieved from [Espressif Systems](https://www.espressif.com/sites/default/files/documentation/esp32_datasheet_en.pdf)

2. Kodali, R. K., & Mahesh, K. S. (2016, December). A low cost implementation of MQTT using ESP8266. In 2016 2nd International Conference on Contemporary Computing and Informatics (IC3I) (pp. 404-408). IEEE.

3. Light, R. A. (2017). Mosquitto: server and client implementation of the MQTT protocol. Journal of Open Source Software, 2(13), 265.

4. Naik, N. (2017, August). Choice of effective messaging protocols for IoT systems: MQTT, CoAP, AMQP and HTTP. In 2017 IEEE international systems engineering symposium (ISSE) (pp. 1-7). IEEE.

5. Singh, M., Rajan, M. A., Shivraj, V. L., & Balamuralidhar, P. (2015, January). Secure MQTT for Internet of Things (IoT). In 2015 Fifth International Conference on Communication Systems and Network Technologies (pp. 746-751). IEEE.

6. Torres Bermúdez, A., François, J., Blouin, C., & Steinmetz, R. (2022). Power consumption analysis of MQ
