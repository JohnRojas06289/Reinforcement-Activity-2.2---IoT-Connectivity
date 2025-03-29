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

Para nuestra implementación en T-Alem, hemos seleccionado una **arquitectura de red híbrida** que combina:

- **Red de Área Local (LAN)**: Para conexiones de alto ancho de banda y baja latencia dentro de la planta de fabricación
- **Red de Área Amplia de Baja Potencia (LPWAN)**: Para conectividad energéticamente eficiente de los sensores de peso en el almacén

Este enfoque híbrido proporciona los siguientes beneficios:
- Conectividad confiable y de alta velocidad para cámaras de control de calidad basadas en IA
- Conectividad energéticamente eficiente y de larga duración de batería para sensores de inventario
- Escalabilidad a medida que el sistema se expande a líneas de producción o áreas de almacén adicionales
- Redundancia a través de múltiples métodos de conexión

### 1.3 Protocolos de Red

Después de una investigación exhaustiva sobre protocolos IoT, hemos seleccionado los siguientes para nuestra implementación en T-Alem:

| Protocolo | Capa | Propósito | Implementación |
|----------|-------|---------|----------------|
| **MQTT** | Aplicación | Transmisión de datos principal | Conexión de sensores y cámaras al broker central |
| **CoAP** | Aplicación | Alternativa ligera | Respaldo para sensores de peso si MQTT falla |
| **IPv6/6LoWPAN** | Red | Direccionamiento para todos los dispositivos | Permitir expansión futura con direccionamiento único |
| **IEEE 802.11 (Wi-Fi)** | Física/Enlace | Transmisión de alto ancho de banda | Conexión de cámaras de control de calidad |
| **IEEE 802.15.4 (ZigBee)** | Física/Enlace | Red de malla de bajo consumo | Sensores de peso en almacén |

#### 1.3.1 Implementación de MQTT

MQTT (Message Queuing Telemetry Transport) sirve como la piedra angular de nuestro diseño de conectividad por varias razones clave:

1. **Arquitectura Publicar/Suscribir**: Perfectamente adecuada para nuestro escenario donde múltiples sensores (publicadores) necesitan enviar datos a sistemas centrales (suscriptores) en una relación de uno a muchos.

2. **Uso Mínimo de Ancho de Banda**: La naturaleza ligera de MQTT lo hace ideal para nuestros sensores de peso, que tienen potencia de procesamiento y vida útil de batería limitadas, pero necesitan enviar frecuentemente pequeñas cargas de datos (mediciones de peso).

3. **Niveles de Calidad de Servicio (QoS)**: Nos permite priorizar mensajes críticos:
   - QoS 0 para actualizaciones regulares de peso (donde los mensajes ocasionalmente perdidos son aceptables)
   - QoS 1 para alertas de inventario bajo (asegurando que lleguen al sistema al menos una vez)
   - QoS 2 para fallos críticos de control de calidad (garantizando la entrega exactamente una vez)

4. **Testamento y Última Voluntad**: Permite al sistema detectar cuando los sensores se desconectan inesperadamente, activando alertas de mantenimiento.

Nuestra implementación de MQTT incluye:
- Un broker MQTT central alojado en el servidor principal de la fábrica
- Clientes MQTT integrados en cada sensor de peso y cámara de control de calidad
- Estructura de temas organizada por:
  - `/inventario/[id_contenedor]/peso`
  - `/inventario/[id_contenedor]/alerta`
  - `/calidad/[id_camara]/inspeccion`
  - `/calidad/[id_camara]/defecto`
- Cifrado TLS para todas las comunicaciones MQTT

## 2. Validación del Diseño

### 2.1 Simulación en Cisco Packet Tracer

Validamos nuestro diseño de conectividad utilizando Cisco Packet Tracer para simular toda la infraestructura de red antes de la implementación física.

#### 2.1.1 Configuración de la Simulación

Nuestra simulación incluyó:
- 20 sensores de peso IoT (representados como dispositivos IoT en Packet Tracer)
- 5 cámaras de control de calidad (representadas como cámaras IP)
- 1 servidor central (ejecutando el broker MQTT)
- 2 puntos de acceso inalámbrico (para conectividad Wi-Fi)
- 1 router (conectando a redes externas)
- 1 panel de monitoreo central (representado como un cliente PC)

La configuración de red incluyó:
- Segregación VLAN entre sistemas de inventario y control de calidad
- Servidor DHCP para asignación de direcciones IP
- Diseño apropiado de subred IP para escalabilidad futura
- Reglas de firewall para proteger la infraestructura IoT

#### 2.1.2 Resultados de las Pruebas

Las pruebas de conectividad arrojaron los siguientes resultados:

| Caso de Prueba | Resultado | Notas |
|-----------|--------|-------|
| Sensor de peso a broker MQTT | ✓ Éxito | Latencia promedio: 12ms |
| Cámara de calidad a broker MQTT | ✓ Éxito | Latencia promedio: 8ms |
| Propagación de alertas | ✓ Éxito | Alerta recibida en 230ms |
| Transmisión simultánea de datos | ✓ Éxito | La red manejó 25 conexiones concurrentes |
| Recuperación ante fallos de red | ✓ Éxito | Reconexión automática en 5 segundos |
| Pruebas de penetración de seguridad | ✓ Éxito | Intentos de acceso no autorizado bloqueados |

#### 2.1.3 Desafíos Encontrados

Durante la validación, encontramos y resolvimos varios desafíos:

1. **Congestión de Red**: Las pruebas iniciales mostraron problemas de ancho de banda cuando todas las cámaras de calidad transmitían simultáneamente.
   - Solución: Implementamos políticas de QoS para priorizar el tráfico y programamos ventanas de transmisión.

2. **Autenticación de Sensores**: Algunos sensores fallaron al autenticarse correctamente con el broker MQTT.
   - Solución: Actualizamos la gestión de certificados TLS e implementamos un sistema de autenticación más robusto.

3. **Pérdida de Paquetes**: En ciertas áreas del almacén, la simulación mostró potencial interferencia de señal.
   - Solución: Añadimos puntos de acceso inalámbrico adicionales y ajustamos la configuración de potencia de transmisión.

### 2.2 Tecnologías Alternativas

Si bien nuestro diseño principal utiliza Wi-Fi y ZigBee para conectividad física, también validamos tecnologías alternativas:

- **Ethernet**: Para cámaras de control de calidad estacionarias, probamos conexiones Ethernet directas, que proporcionaron mayor estabilidad y mayor rendimiento que las opciones inalámbricas.

- **Bluetooth Low Energy (BLE)**: Para escenarios de consumo extremadamente bajo, validamos conexiones BLE para sensores en áreas de inventario raramente accedidas, extendiendo la vida de la batería a más de 12 meses.

No tuvimos acceso para probar conectividad celular o satelital, pero nuestra arquitectura está diseñada para acomodar estas tecnologías si es necesario para expansión futura a almacenes remotos.

## 3. Plan de Implementación

### 3.1 Fases de Despliegue de Red

Nuestra implementación seguirá un enfoque por fases:

1. **Fase 1 (Mes 1)**: Configuración de infraestructura central
   - Instalación del servidor central y broker MQTT
   - Despliegue de equipos de red (router, switches, puntos de acceso)
   - Configuración de políticas de seguridad y firewalls

2. **Fase 2 (Mes 2)**: Despliegue del sistema de inventario
   - Instalación y configuración de sensores de peso en área piloto (20% del inventario)
   - Configuración de sistema de recolección de datos y alertas
   - Prueba y validación del rendimiento en el mundo real

3. **Fase 3 (Mes 3)**: Despliegue del sistema de control de calidad
   - Instalación y configuración de cámaras de control de calidad en primera línea de producción
   - Entrenamiento de algoritmos de IA con conjunto de datos inicial
   - Integración con sistema de monitoreo central

4. **Fase 4 (Mes 4)**: Despliegue completo y optimización
   - Expansión a todas las áreas de inventario y líneas de producción
   - Optimización de parámetros de red basados en datos del mundo real
   - Capacitación del personal en operación y mantenimiento del sistema

### 3.2 Resultados Esperados

Tras la implementación exitosa, esperamos lograr:

- Reducción del 95% en el tiempo de conteo manual de inventario
- Precisión del 90%+ en el seguimiento de inventario
- Tasa de detección de defectos del 70%+ en control de calidad
- Visibilidad en tiempo real de los niveles de inventario y calidad de producción
- Información basada en datos para mejora de procesos

## 4. Referencias de Apoyo

1. Al-Fuqaha, A., Guizani, M., Mohammadi, M., Aledhari, M., & Ayyash, M. (2015). Internet of things: A survey on enabling technologies, protocols, and applications. IEEE Communications Surveys & Tutorials, 17(4), 2347-2376.

2. Naik, N. (2017, August). Choice of effective messaging protocols for IoT systems: MQTT, CoAP, AMQP and HTTP. In 2017 IEEE international systems engineering symposium (ISSE) (pp. 1-7). IEEE.

3. Sethi, P., & Sarangi, S. R. (2017). Internet of things: architectures, protocols, and applications. Journal of Electrical and Computer Engineering, 2017.

4. Zaslavsky, A., & Medvedev, A. (n.d.). Internet of Things and Big Data: A Review on Applications and Security Challenges.

5. Cisco Systems. (2023). Cisco Packet Tracer - Simulation Tool. Retrieved from [Cisco Networking Academy](https://www.netacad.com/courses/packet-tracer)

6. MQTT.org. (2023). MQTT Specification. Retrieved from [MQTT.org](https://mqtt.org/mqtt-specification/)
