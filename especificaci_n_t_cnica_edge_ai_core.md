# DR-EDGE-01 | Dispositivo Embebido para Computación en el Borde (Edge AI Core)
**DIEEC - Grupo de Investigación en Sistemas Embebidos y Computación Inteligente**  
*Manizales, Caldas, Colombia*  

---

## 1. Descripción del Problema de Diseño

En el desarrollo de sistemas electrónicos modernos para la Internet Industrial de las Cosas (IIoT), visión artificial e inspección automatizada, existe una necesidad crítica de procesar algoritmos de redes neuronales convolucionales (CNN) y modelos de TinyML directamente en el extremo de la red (*Edge*). El procesamiento centralizado en la nube presenta serias limitaciones relacionadas con la latencia no determinista, el consumo excesivo de ancho de banda, los costos recurrentes de infraestructura y los riesgos asociados a la privacidad y seguridad de los datos.

El problema de diseño consiste en concebir, documentar y especificar la arquitectura de un **dispositivo embebido de computación en el borde** que resuelva la triada fundamental de restricciones:

1. **Aceleración por hardware dedicada (NPU):** Capacidad de ejecutar modelos de aprendizaje profundo (ej. YOLO-Tiny, MobileNet, ResNet-18) de manera eficiente.
2. **Bajo consumo energético:** Operación pasiva con un consumo total inferior a $2.5\text{ W}$ a máxima carga, permitiendo alimentación vía PoE (Power over Ethernet), baterías o paneles solares.
3. **Bajo costo de producción:** Un costo objetivo de lista de materiales (BOM) inferior a **USD $25** para escala de prototipo y producción en lote.

El reto principal radica en integrar un Sistema en Chip (SoC) con Unidad de Procesamiento Neuronal (NPU) integrada, diseñar una etapa de alimentación de alta eficiencia con múltiples dominios de potencia (*power gating*), acondicionar interfaces de captura de alta velocidad (MIPI-CSI) y garantizar robustez electromagnética e industrial.

---

## 2. Definición de Aplicación

### 2.1 Necesidad
Existe una acelerada demanda industrial y comercial de desplegar capacidades de inferencia en tiempo real en ubicaciones remotas o físicamente restringidas. Las soluciones tradicionales basadas en tarjetas tipo Single Board Computer (SBC) de consumo general o GPUs embebidas sufren de un consumo térmico elevado, costos prohibitivos e incapacidad de integrarse de forma compacta en gabinetes industriales.

### 2.2 Problema
Las soluciones de hardware actuales en el mercado fallan al intentar balancear la triada de diseño:
* **GPUs embebidas (ej. NVIDIA Jetson):** Ofrecen alto rendimiento NPU/GPU pero a un costo muy elevado ($> \text{USD } 150$) y consumo superior a $10\text{ W}$, requiriendo disipación activa.
* **SBCs convencionales (ej. Raspberry Pi 4/5):** Requieren módulos aceleradores USB externos (ej. Coral TPU), incrementando el volumen, el costo total ($> \text{USD } 100$) y la fragilidad del enlace físico.
* **Microcontroladores estándar (ej. ESP32 / STM32F4):** Tienen muy bajo costo y bajo consumo, pero carecen de una NPU dedicada, limitando la inferencia a modelos excesivamente reducidos (TinyML básico) con baja precisión y fotogramas por segundo (FPS) insuficientes.

### 2.3 Propiedad Intelectual (IP)
La estrategia de Propiedad Intelectual del proyecto se organiza en tres capas:
1. **Capa de Hardware:** Utilización de un SoC comercial con IP de NPU licenciada (ej. arquitectura ARM Cortex-A7 + NPU IP propietaria o RISC-V NPU IP). El diseño esquemático y el layout de la PCB se mantendrán bajo licencia *CERN Open Hardware License (CERN-OHL-S)* para fomentar la adopción en el ámbito académico y permitir derivaciones comerciales.
2. **Capa de Firmware/Driver:** Integración de bibliotecas de aceleración de código abierto (ej. *TFLite Micro*, *RKNN-Toolkit*, *ONNX Runtime Edge*). Los algoritmos de cuantización (INT8/FP16) y la canalización (*pipeline*) de memoria serán protegidos como secreto industrial o registrados bajo derechos de autor de software.
3. **Protección de Marca y Diseño Industrial:** Registro del diseño de gabinete industrial y de la marca del dispositivo ante las autoridades nacionales de propiedad industrial.

### 2.4 Oportunidad de Negocio
El mercado global de *Edge AI Hardware* experimenta un crecimiento compuesto anual (CAGR) superior al $20\%$. Las oportunidades inmediatas incluyen:
* **Inspección de Calidad Industrial:** Módulos de visión incrustados en líneas de ensamble para detección de defectos en tiempo real ($> 30\text{ FPS}$).
* **Agrotecnología (AgriTech):** Detección temprana de plagas y conteo de frutos en maquinaria agrícola operada por baterías.
* **Ciudades Inteligentes y Tráfico:** Conteo y clasificación de vehículos/peatones en intersecciones con alimentación solar.
* **Mantenimiento Predictivo (IIoT):** Análisis de espectro de vibración y visión térmica en motores industriales.

---

## 3. Requerimientos del Producto y del Sistema

### 3.1 Requerimientos del Producto

| ID | Descripción del Requerimiento | Tipo |
| :--- | :--- | :--- |
| **F-01** | El sistema debe incluir una NPU dedicada con una capacidad de cómputo mínima de $0.5\text{ TOPS}$ @ INT8. | Funcional |
| **F-02** | El dispositivo debe contar con un puerto MIPI-CSI2 de 2 carriles para la interfaz directa con sensores de cámara CMOS. | Funcional |
| **F-03** | El sistema debe incluir conectividad industrial cableada (Ethernet 10/100M y RS485 aislado) e inalámbrica (Wi-Fi 4 / BLE 5.0). | Funcional |
| **F-04** | El dispositivo debe aceptar un rango de alimentación amplio de $9\text{ VDC}$ a $24\text{ VDC}$ industrial y opción de alimentación USB-C ($5\text{ VDC}$). | Funcional |
| **F-05** | La plataforma debe ofrecer al menos 4 entradas digitales aisladas y 2 salidas de relé/SSR para actuación en tiempo real. | Funcional |
| **F-06** | El firmware debe soportar ejecución de modelos en formatos ONNX, TensorFlow Lite y Caffe mediante cuantización a INT8. | Funcional |
| **F-07** | La tarjeta debe incorporar un temporizador en tiempo real (RTC) con respaldo de batería de litio para estampado de tiempo local. | Funcional |
| **NF-01** | El consumo de potencia del sistema completo no debe superar $2.5\text{ W}$ en inferencia continua y $100\text{ mW}$ en modo *sleep*. | No Funcional |
| **NF-02** | El costo total de la lista de materiales (BOM) no debe superar **USD $25** para un volumen de 100 unidades. | No Funcional |
| **NF-03** | La latencia de inferencia para modelos tipo MobileNetV2 ($224 \times 224$) no debe superar los $35\text{ ms}$. | No Funcional |
| **NF-04** | Las dimensiones físicas del circuito impreso (PCB) no deben exceder $70 \times 50\text{ mm}$. | No Funcional |
| **NF-05** | El dispositivo debe operar de forma confiable en un rango térmico industrial de $-20^\circ\text{C}$ a $70^\circ\text{C}$ sin disipación activa (sin ventilador). | No Funcional |
| **NF-06** | Las líneas de comunicación y alimentación deben incluir protección contra descargas electrostáticas (ESD) e impulsos (TVS). | No Funcional |

### 3.2 Dominio de Aplicación Específico
El dominio específico de esta aplicación corresponde a la **Sistemas Embebidos para Inteligencia Artificial en el Borde (Edge AI / TinyML) e Internet Industrial de las Cosas (IIoT)**, caracterizado por restricciones severas de espacio, energía y costo, operando bajo entornos con interferencia electromagnética y variaciones térmicas.

### 3.3 Especificación de Requerimientos de Sistema (SRD)

| ID | Parámetro / Descripción | Especificación / Valor | Método de Verificación |
| :--- | :--- | :--- | :--- |
| **SR-01** | Rango de entrada DC principal | $9 - 24\text{ VDC} \pm 10\%$ | Fuente DC programable y barrido de tensión |
| **SR-02** | Entrada de energía secundaria | $5\text{ VDC} \pm 5\%$ vía USB-C | Verificación funcional con cargador estándar USB |
| **SR-03** | Rendimiento NPU | $\ge 0.5\text{ TOPS}$ (INT8), $\ge 250\text{ GOPS}$ (FP16) | Benchmark de sintaxis en *RKNN-Toolkit / TFLite* |
| **SR-04** | Memoria RAM del sistema | $128\text{ MB} - 512\text{ MB}$ LPDDR3/DDR3 (SiP integrado) | Prueba de prueba de estrés de memoria (*memtester*) |
| **SR-05** | Almacenamiento no volátil | $16\text{ MB}$ NOR Flash SPI / Zócalo MicroSD | Verificación de tiempos de lectura/escritura |
| **SR-06** | Interfaz de Cámara | MIPI-CSI2 (2 lanes, $1.2\text{ Gbps/lane}$) | Captura de video a $1080\text{p} @ 30\text{ FPS}$ |
| **SR-07** | Transceptor RS485 | Aislamiento galvánico $2.5\text{ kV}$, protocolo Modbus RTU | Ensayo con analizador de protocolo industrial |
| **SR-08** | Eficiencia etapa de potencia | $\ge 88\%$ a carga nominal ($1.5\text{ W}$) | Medición con osciloscopio y analizador de potencia |
| **SR-09** | Estabilidad de rizado (Power Rails) | $< 30\text{ mVpp}$ en rail NPU ($1.1\text{ V}$) y RAM ($1.35\text{ V}$) | Osciloscopio con punta coaxial (límite $20\text{ MHz}$) |
| **SR-10** | Rango térmico de trabajo | $-20^\circ\text{C a } +70^\circ\text{C}$ sin degradación de clock | Prueba en cámara ambiental cerrada durante 8 horas |
| **SR-11** | Dimensiones Mecánicas | $70 \times 50 \times 18\text{ mm}$ (con conectores) | Inspección física con calibrador pie de rey |
| **SR-12** | Costo de Componentes (BOM) | $\le \text{USD } 25.00$ | Cotización consolidada con distribuidores mayoristas |

### 3.4 Priorización de Requerimientos (MoSCoW)

| Requerimiento | Prioridad |
| :--- | :--- |
| SoC con NPU dedicada ($\ge 0.5\text{ TOPS}$) e integración de memoria SiP | **Must** |
| Consumo energético total $< 2.5\text{ W}$ en carga plena | **Must** |
| Costo de la BOM $< \text{USD } 25$ en volumen de prototipo | **Must** |
| Interfaz de cámara MIPI-CSI2 de 2 carriles | **Must** |
| Entrada amplia $9-24\text{ VDC}$ con protecciones (TVS, fusible rearmable) | **Must** |
| Módulo de conectividad Wi-Fi 4 / BLE 5.0 | **Should** |
| Puerto Ethernet 10/100M e interfaz RS485 aislada | **Should** |
| Salidas digitales optoacopladas y leds de diagnóstico | **Should** |
| Pantalla OLED de $0.96''$ vía I2C para telemetría local | **Could** |
| Soporte para Power over Ethernet (PoE) mediante *hat* add-on | **Could** |
| Procesamiento de modelos FP32 sin cuantización | **Won't** (Por ahora) |
| Aceleración gráfica 3D (GPU avanzada dedicada) | **Won't** (Por ahora) |

---

## 4. Idea de Diseño

### 4.1 Análisis de Dispositivos Previos y Actuales

```
+-----------------------------------------------------------------------------------+
|                            MATRIZ DE COMPARACIÓN DE HARDWARE                       |
+----------------------+--------------------+--------------------+------------------+
| Dispositivo          | Capacidad NPU      | Consumo Típico     | Costo Estimado   |
+----------------------+--------------------+--------------------+------------------+
| NVIDIA Jetson Nano   | 0.47 TFLOPS (GPU)  | 5 W - 10 W         | USD $150         |
| Raspberry Pi 4 + Coral| 4.0 TOPS (USB)    | 7 W - 12 W         | USD $110         |
| ESP32-S3 (TinyML)    | No NPU (Vector)    | 0.5 W              | USD $6           |
| PROPUESTA (Edge-AI)  | 0.5 - 1.0 TOPS     | 1.2 W - 2.2 W      | USD $22 - $25    |
+----------------------+--------------------+--------------------+------------------+
```

El dispositivo propuesto cubre el vacío técnico existente: proporciona la potencia matemática de una NPU real necesaria para visión artificial, manteniendo el consumo energético en rangos propios de un microcontrolador de bajo consumo y un costo compatible con despliegues masivos.

---

### 4.2 Mapeo de Conocimientos Fundamentales del Diseño Electrónico

Tomando como referencia el modelo integral de **'Conocimientos Fundamentales del Diseño Electrónico'**, la arquitectura del dispositivo se desglosa en sus 6 bloques funcionales de hardware y sus 4 dimensiones transversales:

```
+-----------------------------------------------------------------------------------+
|                ESTRUCTURA CONCEPTUAL DEL DISPOSITIVO EDGE-AI                      |
+-----------------------------------------------------------------------------------+
|                                                                                   |
|  [GENERACIÓN DE NUEVOS PRODUCTOS / MODELOS DE NEGOCIO]                            |
|  * Modelo de Hardware Abierto + Software de Monetización por Gestión de Modelos   |
|                                                                                   |
|  +-----------------------------------------------------------------------------+  |
|  | [INGENIERÍA DE SOFTWARE]      | [BLOQUES DE HARDWARE]   | [DISEÑO MECÁNICO] |  |
|  | * Linux Embebido (Buildroot)  | 1. Fuentes de Aliment.  | * Enclosure ABS / |  |
|  | * RKNN Runtime / TFLite       | 2. Estructura Computac. |   Aluminio        |  |
|  | * Drivers MIPI/V4L2           | 3. Entradas (MIPI/ADC)  | * Disipación      |  |
|  | * Stack MQTT/Modbus           | 4. Salidas (PWM/Relés)  |   Pasiva Radiante |  |
|  |                               | 5. HMI / MMI            |                   |  |
|  |                               | 6. M2MC Comunicación    |                   |  |
|  +-----------------------------------------------------------------------------+  |
|                                                                                   |
|  [CONTEXTO / TRABAJO EN EQUIPO Y HABILIDADES COMUNICATIVAS]                       |
|  * Integración multidisciplinar: HW, FW, Data Science y Mecatrónica               |
+-----------------------------------------------------------------------------------+
```

#### A. Bloques Funcionales Internos (Hardware Core):

1. **Fuentes de Alimentación:**
   * *Entrada:* Dual ($9-24\text{ VDC}$ industrial y $5\text{ VDC}$ vía USB-C).
   * *Regulación:* Integración de PMIC (Power Management IC) o reguladores Buck síncronos de alta frecuencia ($1.5\text{ MHz}$) para generar las líneas de tensión:
     * $1.1\text{ V}$ para el Core del SoC y la NPU (hasta $1.5\text{ A}$).
     * $1.35\text{ V}$ / $1.8\text{ V}$ para memoria LPDDR3/DDR3.
     * $3.3\text{ V}$ para periféricos, transceptores y Wi-Fi/BLE.
     * $5.0\text{ V}$ para alimentación de la cámara y puertos externos.
   * *Protección:* Diodo de polaridad inversa, supresor de picos TVS y fusible rearmable PTC.

2. **Estructura Computacional:**
   * *SoC Principal:* Rockchip RV1106 o similar (Procesador ARM Cortex-A7 @ $1.2\text{ GHz}$ + Co-procesador RISC-V de ultra bajo consumo).
   * *NPU:* Acelerador vectorial integrado con soporte para operaciones INT8/INT16 y potencia de $0.5\text{ a } 1.0\text{ TOPS}$.
   * *Memoria:* $128\text{ MB}$ de DDR3 integrada dentro del propio encapsulado del SoC (System-in-Package - SiP), reduciendo drásticamente la complejidad del layout PCB y el costo de ruteo de memoria de alta velocidad.
   * *Almacenamiento:* Flash SPI NOR de $16\text{ MB}$ para el Bootloader/Kernel y zócalo MicroSD para modelos de IA y almacenamiento local.

3. **Entradas:**
   * *Captura de Video:* Interfaz MIPI-CSI2 de 2 carriles differentiales ($100\ \Omega$ impedancia).
   * *Acondicionamiento Analógico/Digital:* Entradas digitales industriales optoacopladas ($24\text{ V}$ tolerantes) con filtro pasa-bajo RC para inmunidad a rebotes y ruido.

4. **Salidas:**
   * *Actuación:* Salidas de transistor MOSFET de potencia N-Channel o relés de estado sólido (SSR) con supresión de flyback mediante diodos Schottky.
   * *Señales Controladas:* Salidas PWM para control de intensidad de iluminación LED de visión o motores paso a paso.

5. **HMI/MMI (Interface Hombre-Máquina):**
   * *Indicadores LED:* LED de Power Good, System Status (Heartbeat), NPU Activity y Network Link.
   * *Interfaz Visual Opcional:* Cabezal de pines I2C/SPI para conectar pantallas OLED de $0.96''$ o TFT de $1.3''$.
   * *Interfaz Botón:* Botón de Reset y botón configurable por el usuario (*User Key*).

6. **M2MC - Comunicación Máquina a Máquina:**
   * *Inalámbrica:* Módulo Wi-Fi 802.11 b/g/n + BLE 5.0 (vía interfaz SDIO/UART).
   * *Cableada Industrial:* Transceptor RS485 aislado con protección contra sobretensiones para transmisión via Modbus RTU.
   * *Red Local:* PHY Ethernet 10/100M (RTL8201F) con conector RJ45 con magnetos integrados.

---

#### B. Perspectivas Exteriores (Entorno de Desarrollo):

* **Ingeniería de Software e Información:** Sistema operativo Linux Embebido generado vía *Buildroot* (tiempo de arranque $< 3\text{ segundos}$). Pipeline de video optimizado con V4L2 y aceleración NPU vía runtime optimizado C++.
* **Diseño Mecánico e Industrial:** Chasis modular compacto fabricado en ABS ignífugo con placa de disipación térmica de aluminio en contacto con el SoC a través de un *thermal pad*.
* **Generación de Productos / Modelos de Negocio:** Formato de venta *Hardware-as-a-Service* (HaaS) o venta directa del módulo con licenciamiento de suite de entrenamiento de modelos personalizados en la nube.
* **Contexto y Trabajo en Equipo:** Trabajo colaborativo mediante control de versiones de hardware (Git/KiCAD) y metodologías ágiles de hardware (*Hardware Sprints*).

---

### 4.3 Patrones de Diseño Aplicados

1. **Patrón Hardware "Power Domain Separation":** Aislamiento de las etapas de potencia de la NPU y periféricos de radio. La NPU se mantiene en estado de apagado (*Power Gating*) hasta que la etapa de captura MIPI o una interrupción externa detecta un evento relevante.
2. **Patrón de Pipeline Cómputo Doble Buffer:** En software/firmware, mientras el DMA captura el frame $N+1$ desde la cámara MIPI, la NPU procesa la inferencia sobre el frame $N$ en memoria RAM compartida zero-copy.
3. **Patrón "Impedance Matching & Differential Routing":** En el diseño de la PCB, las líneas MIPI-CSI y Ethernet se rutean como pares diferenciales con impedancia controlada de $100\ \Omega$ y $90\ \Omega$ respectivamente, asegurando la integridad de señal sin emisiones electromagnéticas (EMI).

---

## 5. Esquema NABC (Need, Approach, Benefits, Competition)

### 5.1 Need (Necesidad)
Las industrias de automatización, agricultura de precisión y seguridad urbana requieren procesar video y datos de sensores en tiempo real mediante Inteligencia Artificial en el sitio exacto de captura. Las soluciones existentes en la nube sufren de **latencia alta ($> 200\text{ ms}$)**, consumo masivo de ancho de banda y dependencia de conectividad. Por otro lado, los dispositivos Edge actuales son demasiado costosos ($> \text{USD } 100$) o consumen demasiado calor/energía ($> 10\text{ W}$) para ser viables en despliegues masivos alimentados por baterías o paneles.

### 5.2 Approach (Enfoque)
Nuestra solución es el **Edge AI Core (DR-EDGE-01)**: un dispositivo embebido monolítico basado en un SoC con NPU dedicada de $0.5 - 1.0\text{ TOPS}$, memoria SiP integrada y una arquitectura de alimentación de bajo consumo ($< 2.5\text{ W}$). El enfoque combina:
* Hardware altamente integrado de bajo costo ($\text{BOM} < \text{USD } 25$).
* Sistema operativo Linux ultra-ligero de arranque rápido.
* Cadena de herramientas (*Toolchain*) para convertir directamente modelos PyTorch/TensorFlow a ejecutables INT8 de NPU.

### 5.3 Benefits (Beneficios)
* **Reducción de Costo:** Disminución del $75\%$ en costo de hardware comparado con un módulo NVIDIA Jetson Nano o Raspberry Pi + Coral TPU.
* **Eficiencia Energética:** Reducción del $80\%$ en consumo de potencia, permitiendo operación continua en entornos aislados con paneles solares pequeños.
* **Procesamiento Local Determinista:** Inferencia en menos de $35\text{ ms}$ sin necesidad de conexión a internet.
* **Inmunidad Industrial:** Operación en rango extended ($-20^\circ\text{C a } 70^\circ\text{C}$) sin partes móviles (sin ventiladores).

### 5.4 Competition (Competencia)

| Criterio | NVIDIA Jetson Orin Nano | Raspberry Pi 4 + Coral | ESP32-S3 Cam | **Nuestra Propuesta (DR-EDGE-01)** |
| :--- | :--- | :--- | :--- | :--- |
| **Costo HW** | High ($\sim \text{USD } 200$) | Medium ($\sim \text{USD } 110$) | Ultra Low ($\sim \text{USD } 10$) | **Low ($\sim \text{USD } 25$)** |
| **Consumo** | High ($7 - 15\text{ W}$) | High ($5 - 9\text{ W}$) | Ultra Low ($< 0.5\text{ W}$) | **Low ($1.2 - 2.5\text{ W}$)** |
| **Potencia NPU**| Ultra High ($20 - 40\text{ TOPS}$) | High ($4\text{ TOPS}$) | Nula (Solo CPU SIMD) | **Media-Alta ($0.5 - 1.0\text{ TOPS}$)** |
| **Latencia CNN**| $< 10\text{ ms}$ | $< 15\text{ ms}$ | $> 300\text{ ms}$ (o inviable) | **$< 35\text{ ms}$** |
| **Formato** | Grande con Disipador | Mediano (múltiples tarjetas)| Compacto | **Compacto Monolítico** |

---

## 6. Diagrama de Arquitectura del Sistema

### 6.1 Diagrama de Ámbito / Contexto (Sistema General)

```
                       +-----------------------------------+
                       |    RED ELÉCTRICA / PANEL SOLAR    |
                       |      (9 - 24 VDC / USB 5V)        |
                       +-----------------------------------+
                                         |
                                         v
+-----------------------+     +--------------------+     +-----------------------+
|  CÁMARA CMOS MIPI-CSI |---->|                    |---->| ACTUADORES / RELÉS    |
| (Captura 1080p Video) |     |  DISPOSITIVO EDGE  |     | (Control de Proceso)  |
+-----------------------+     |     AI CORE        |     +-----------------------+
| SENSORES INDUSTRIALES |---->|    (DR-EDGE-01)   |---->| PANTALLA OLED / LEDS  |
|  (I2C / ADC / 24V DI) |     |                    |     | (Diagnóstico Local)   |
+-----------------------+     +--------------------+     +-----------------------+
                                   ^          ^
                                   |          |
                                   v          v
                       +-----------------------------------+
                       | CONECTIVIDAD M2M (Wi-Fi/BLE/RS485)|
                       |   Servidor SCADA / Broker MQTT    |
                       +-----------------------------------+
```

### 6.2 Diagrama de Bloques Funcional de Hardware

```
 +-----------------------------------------------------------------------------------+
 |                             ARQUITECTURA DE HARDWARE                              |
 +-----------------------------------------------------------------------------------+
 |                                                                                   |
 |  [ENTRADA ALIMENTACIÓN]                                                           |
 |  9-24VDC / USB-C 5V --> [Filtro EMI/Protección TVS] --> [PMIC / Buck Converters]  |
 |                                                              |                    |
 |                                    +-------------------------+                    |
 |                                    | TENSIONES: 1.1V, 1.35V, 3.3V, 5.0V          |
 |                                    v                                              |
 |  [ENTRADAS DE DATOS]        [SOC COMPUTACIONAL]              [INTERFACES SALIDA]  |
 |  * Cámara MIPI-CSI2 ------> | * CPU ARM Cortex-A7 @ 1.2GHz  | -> RS485 Aislado    |
 |  * Acondicionador 24V ----> | * NPU Aceleradora 1.0 TOPS    | -> Ethernet 10/100M |
 |  * Sensores I2C/SPI -------> | * Memoria SiP 128MB DDR3      | -> Wi-Fi 4 / BLE 5  |
 |                             | * Co-Procesador RISC-V         | -> SSR / PWM Out    |
 |                             +--------------------------------+ -> LEDs Status      |
 |                                            |                                      |
 |                                            v                                      |
 |                                  [ALMACENAMIENTO LOCAL]                           |
 |                                  * Flash NOR SPI 16MB (Boot)                      |
 |                                  * Zócalo MicroSD (Modelos IA)                    |
 +-----------------------------------------------------------------------------------+
```

---

## 7. Comentarios, Conclusiones y Control de Revisión

### 7.1 Comentarios y Conclusiones
El presente documento consolida la especificación técnica, los requerimientos formales y la arquitectura conceptual para un dispositivo embebido de computación en el borde orientado a acelerar modelos de redes neuronales bajo restricciones de costo y energía. 

Se logró estructurar una solución que responde de manera óptima a los tres pilares fijados: **NPU dedicada ($0.5-1.0\text{ TOPS}$)**, **bajo consumo ($< 2.5\text{ W}$)** y **bajo costo ($\text{BOM} < \text{USD } 25$)**. La integración de un SoC con memoria DDR en el mismo encapsulado (SiP) reduce la complejidad de ruteo en la PCB, disminuyendo el número de capas requeridas a 4 y garantizando la viabilidad de manufactura local e internacional.

### 7.2 Riesgos de Diseño Identificados
1. **Gestión Térmica Focalizada:** Aunque el consumo global es bajo ($< 2.5\text{ W}$), la NPU concentra disipación de potencia en un área pequeña del die durante la ejecución de inferencia sostenida.
   * *Mitigación:* Ruteo de vías térmicas (*thermal vias*) bajo el pad expuesto del SoC conectando con los planos de tierra internos.
2. **Disponibilidad de componentes:** Sensibilidad en la cadena de suministro para transceptores y el SoC principal.
   * *Mitigación:* Selección de componentes con reemplazos directos *pin-to-pin* de segundos proveedores.

### 7.3 Control de Revisión y Aprobación

| Revisión | Responsable | Fecha | Estado / Notas |
| :--- | :--- | :--- | :--- |
| **0.1** | Equipo de Diseño Embebido | Septiembre 1, 2026 | Propuesta inicial y arquitectura conceptual |
| **0.2** | Coordinador de Proyecto | Septiembre 15, 2026 | Revisión de requerimientos y viabilidad BOM |
| **1.0** | Comité Técnico DIEEC | Septiembre 25, 2026 | Aprobado para diseño esquemático y PCB Layout |
