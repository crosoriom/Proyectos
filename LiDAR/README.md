# DR-LiDAR-01 | Especificación de Sistema y Arquitectura Técnica
## Sensor LiDAR 3D con Escaneo por MEMS / Galvanómetros Ópticos y Reconstrucción Externa
**Departamento de Ingeniería Electrónica y Computación**  
*Documento de Diseño y Requerimientos de Producto (Rev. 0.1 - Septiembre 2026)*

---

## 1. Definición de la Aplicación

### 1.1 Necesidad
En aplicaciones de robótica autónoma (AGVs/AMRs), inspección industrial de calidad, conservación del patrimonio cultural y mapeo arquitectónico BIM, existe una creciente demanda de digitalización tridimensional de alta precisión, tiempo real y bajo costo. Los sistemas de visión estéreo y luz estructurada sufren bajo condiciones de iluminación variable o ambientes con desorden, mientras que las soluciones LiDAR convencionales ofrecen o bien alta precisión a un costo prohibitivo o lecturas unidimensionales/bidimensionales insuficientes para la reconstrucción volumétrica completa.

### 1.2 Problema
Los sensores LiDAR 3D de grado industrial basados en cabezales rotativos mecánicos (como los de tipo *spinning*) son voluminosos, propensos al desgaste mecánico por partes móviles, costosos ($> USD 2,000 - 10,000$) y consumen elevada potencia. Por otro lado, los sensores ToF (Time-of-Flight) directos matriciales o 1D poseen un ángulo de visión limitado o baja densidad de puntos.  
El desafío de este proyecto consiste en diseñar e integrar un **sistema de escaneo óptico mediante espejos (MEMS o Galvanómetros Ópticos)** acoplado a un **módulo ToF de alta velocidad**, capaz de coordinar la deflexión angular precisa en dos ejes $(x, y)$, adquirir mediciones de distancia $r$, y transmitir la nube de puntos $P(x, y, z)$ o $P(r, \theta, \phi)$ con baja latencia hacia un sistema de cómputo externo para la reconstrucción de superficies 3D.

### 1.3 Propiedad Intelectual (PI) y Estado del Arte
- **Patentes y Estado del Arte:** El campo de Solid-State LiDAR (SSL) y escaneo direccional cuenta con patentes clave en algoritmos de sincronización entre disparo del pulso de luz y posición instantánea del espejo (ej. patentes de Mirrorcle Technologies, Palmer Scanners, Velodyne LiDAR y Texas Instruments).
- **Estrategia de PI para el Proyecto:** Se focaliza en el desarrollo de un algoritmo de sincronización determinista en hardware (FPGA/MCU) entre el barrido no lineal del espejo y el muestreo ToF, sumado a un protocolo liviano de empaquetado y compresión de nubes de puntos para transmisión Ethernet/USB-C. El hardware integrará módulos comerciales sin infringir patentes de topología de micromapeo de espejos MEMS.

### 1.4 Oportunidad de Negocio
El mercado global de sensores 3D para automatización industrial y robótica de servicio crece a una tasa anual compuesta (CAGR) superior al 18%. Un módulo de escaneo 3D embebido basado en ToF + Galvo/MEMS con un costo de lista final $< USD 800-1,000$ (y un costo de fabricación de prototipo $< USD 500$) democratizaría el acceso a la metrología 3D y la navegación robótica SLAM 3D para PYMEs y laboratorios de investigación.

---

## 2. Casos de Uso del Sistema

| Caso | Descripción | Requerimientos Derivados |
| :--- | :--- | :--- |
| **1** | **Navegación y SLAM 3D en Robótica Móvil:** Un AMR escanea el entorno para crear mapas volumétricos y evitar obstáculos suspendidos. | Alta tasa de refresco ($\ge 10\text{ Hz}$ por cuadro), ángulo de visión $FOV \ge 30^\circ \times 30^\circ$, interfaz M2M Ethernet/UDP. |
| **2** | **Inspección dimensional de piezas de manufactura:** Medición de deformación en componentes mecánicos sobre línea de ensamblaje. | Alta resolución angular ($< 0.1^\circ$), precisión de rango $< 5\text{ mm}$, repetibilidad del espejo MEMS/Galvo. |
| **3** | **Digitalización Arquitectónica y Modelado BIM:** Escaneo estático de interiores de habitaciones para generación de planos 3D. | Alcance de $0.2\text{ m}$ a $10\text{ m}$, capacidad de almacenamiento/transmisión de $> 50,000\text{ puntos/s}$. |
| **4** | **Conteo y Cubicación Volumétrica de Cargas:** Escaneo de cajas y estibas en centros logísticos para determinar volumen. | Cobertura uniforme del área, algoritmo de interpolación espacial de baja latencia en el host. |
| **5** | **Monitoreo de Seguridad e Incursión Perimetral:** Detección de presencia física en áreas restringidas de plantas industriales. | Operación continua $24/7$, resiliencia a luz ambiental ($> 30\text{ klx}$), disparo de alarmas en tiempo real. |
| **6** | **Mapeo de Preservación de Patrimonio:** Escaneo no destructivo de esculturas u objetos de arte. | Bajo impacto óptico (láser Clase 1 seguro para la vista), alta densidad de puntos en superficies no reflectivas. |
| **7** | **Laboratorio Universitario / Investigación en Visión:** Banco de pruebas académico para algoritmos de procesamiento de nubes de puntos (PCL/ROS 2). | Protocolo de transmisión estándar (UDP/TCP/ROS2 PointCloud2), APIs en Python/C++. |
| **8** | **Detección de Deformación en Tanques/Estructuras:** Mapeo de paredes cilíndricas para encontrar grietas o abolladuras. | Alta estabilidad térmica del driver del galvo/MEMS, bajo drift de medición en periodos prolongados. |
| **9** | **Escaneo Agrobiológico / Fenotipado de Plantas:** Medición de estructura de follaje e índice de área foliar en invernaderos. | Penetración de haz infrarrojo (850 nm / 940 nm / 1064 nm), tolerancia a condiciones de humedad relativa ($< 85\%$). |
| **10**| **Interacción Hombre-Máquina 3D (HMI Avanzada):** Detección de gestos y seguimiento corporal en espacios cerrados. | Latencia total de transmisión $< 30\text{ ms}$, filtrado de ruido en el frente de onda. |

---

## 3. Requerimientos del Producto

| ID | Descripción del Requerimiento | Tipo | Prioridad (MoSCoW) |
| :--- | :--- | :--- | :--- |
| **F-01** | El sistema debe emitir un haz óptico modulado/pulsado ToF y deflectarlo en 2 ejes ($X, Y$) mediante MEMS o Galvanómetro. | Funcional | **Must** |
| **F-02** | El sistema debe medir distancias dentro del rango de $0.2\text{ m}$ a $10\text{ m}$ con el sensor ToF. | Funcional | **Must** |
| **F-03** | El sistema debe registrar coordinadamente las coordenadas angulares $(\theta_x, \theta_y)$ del espejo con la lectura de distancia $r$. | Funcional | **Must** |
| **F-04** | El sistema debe empaquetar y transmitir los puntos $(x,y,z,I)$ vía Ethernet High-Speed o USB 3.0/HS a una PC externa. | Funcional | **Must** |
| **F-05** | El sistema debe permitir la configuración de patrones de escaneo (Raster Scan, Lissajous o ROI personalizado). | Funcional | **Must** |
| **F-06** | La unidad debe contar con protección óptica para garantizar seguridad láser de Clase 1 (IEC 60825-1). | Funcional | **Must** |
| **F-07** | El sistema debe disponer de un indicador visual LED del estado de sincronía, disparo láser y comunicación. | Funcional | **Should** |
| **F-08** | El sistema debe permitir la calibración y corrección de distorsión geométrica introducida por el ángulo del espejo. | Funcional | **Should** |
| **F-09** | La unidad debe incluir un mecanismo de autochequeo al arranque (Power-On Self-Test - POST) para galvos/MEMS. | Funcional | **Could** |
| **NF-01**| La tasa de adquisición de puntos debe ser $\ge 50,000\text{ puntos/segundo}$. | No Funcional | **Must** |
| **NF-02**| La resolución de medición de distancia debe ser $\le 10\text{ mm}$ a una distancia de $5\text{ m}$. | No Funcional | **Must** |
| **NF-03**| El rango dinámico angular total debe ser de al menos $30^\circ \times 30^\circ$ ópticos. | No Funcional | **Must** |
| **NF-04**| La latencia de transmisión de datos desde la captura hasta la salida en red no debe superar los $20\text{ ms}$. | No Funcional | **Must** |
| **NF-05**| El costo objetivo de componentes (BOM) para la unidad de prototipado no debe superar $USD\ 750$. | No Funcional | **Must** |
| **NF-06**| El consumo de potencia total del dispositivo debe ser $\le 15\text{ W}$ en operación continua. | No Funcional | **Should** |
| **NF-07**| La envolvente mecánica no debe superar las dimensiones de $120 \times 100 \times 90\text{ mm}$. | No Funcional | **Should** |
| **NF-08**| El sistema debe operar en un rango de temperatura ambiente de $0^\circ\text{C}$ a $50^\circ\text{C}$. | No Funcional | **Should** |
| **NF-09**| Se deben incluir APIs/librerías en Python y ROS 2 para la ingesta directa de la nube de puntos en el host. | No Funcional | **Could** |

---

## 4. Requerimientos del Sistema (SRD)

| ID | Parámetro / Descripción | Especificación / Valor Target | Método de Verificación |
| :--- | :--- | :--- | :--- |
| **SR-01**| Alimentación Principal | $12\text{ VDC} \pm 5\%$ / $2\text{ A}$ o entrada PoE ($48\text{ V}$ IEEE 802.3at) | Medición con DMM y osciloscopio bajo carga máx. |
| **SR-02**| Sub-rieles de Potencia | $+5\text{V}$, $+3.3\text{V}$, $\pm15\text{V}$ (drivers Galvo) / High Voltage (MEMS) | Verificación de rizado en PCB ($< 30\text{ mV}_{pp}$) |
| **SR-03**| Longitud de onda del emisor | $850\text{ nm}$ o $905\text{ nm}$ (Infrarrojo cercano) | Espectrómetro óptico / Hoja de datos ToF |
| **SR-04**| Apertura del Espejo | $\ge 3\text{ mm}$ (Galvo) o $\ge 1.2-2.0\text{ mm}$ (MEMS) | Inspección física y tolerancia de haz láser |
| **SR-05**| Ancho de haz láser ($1/e^2$) | $\le 2.0\text{ mm}$ a la entrada del sistema de espejos | Perfilador de haz óptico (Beam profiler) |
| **SR-06**| Frecuencia de Barrido Eje Rápido | $\ge 500\text{ Hz}$ (Galvo) / $\ge 1.3-6\text{ kHz}$ (MEMS resonante) | Osciloscopio sobre señal de drive/feedback |
| **SR-07**| Frecuencia de Barrido Eje Lento | $10\text{ Hz} - 50\text{ Hz}$ (Escaneo Raster continuo) | Generador de funciones / Código de control MCU |
| **SR-08**| Controlador Principal | MCU ARM Cortex-M7 (ej. STM32H743) o FPGA/SoC Zynq-7000 | Verificación de tiempos de ejecución e I/O |
| **SR-09**| DAC de Control Angular | 16 bits SPI/I2S dual channel ($< 2\text{ LSB}$ INL) | Osciloscopio / Medición de voltaje de referencia |
| **SR-10**| Interfaz de Red Externa | Gigabit Ethernet (1000Base-T) o USB 3.0 Micro-B / Type-C | Prueba de rendimiento Iperf3 / Wireshark |
| **SR-11**| Formato de Paquete de Datos | Protocolo binario custom sobre UDP con Timestamping | Captura de paquetes y análisis de integridad |
| **SR-12**| Seguridad Láser | Interlock por software y hardware en caso de falla de galvo | Desconexión del diodo si el espejo se detiene |
| **SR-13**| Dimensiones Mecánicas | $110\times 95 \times 85\text{ mm}$ | Calibrador vernier / Modelo CAD 3D |
| **SR-14**| Rango Térmico de Operación | $0^\circ\text{C}$ a $45^\circ\text{C}$, $HR < 85\%$ sin condensación | Cámara de ensayos térmicos |

---

## 5. Arquitectura Técnica y Pilares del Diseño Electrónico

A partir del marco de conceptos fundamentales del diseño electrónico (Fuentes, Entradas, Salidas, Estructura Computacional, HMI, M2MC, Software, Mecánica y Negocio), se define el diseño modular del sistema:

```
                  =====================================================================
                  |                     SISTEMA EXTERNO (HOST / PC)                   |
                  |   - Reconstrucción 3D (PCL / Open3D / ROS 2)                      |
                  |   - Visualización de Nube de Puntos (x, y, z, I)                  |
                  =====================================================================
                                                   ^
                                                   | (Gigabit Ethernet / UDP Binario)
                                                   v
====================================================================================================
| SENSOR LiDAR 3D EMBEBIDO                                                                         |
|                                                                                                  |
|  +-----------------------+     +-------------------------------+     +------------------------+  |
|  | FUENTES DE POTENCIA   |     | ESTRUCTURA COMPUTACIONAL      |     | M2MC & HMI             |  |
|  | - Entrada 12V DC      |---->| - MCU STM32H743 / Zynq-7000   |<--->| - PHY Ethernet (KSZ9031)|  |
|  | - Reg. Buck 5V/3.3V   |     | - Sincronización ToF/Galvo    |     | - LEDs de Estado       |  |
|  | - Fuente Aux. +/-15V  |     | - Buffer DMA & Filtro Inicial |     | - Botón de Calibración |  |
|  +-----------------------+     +-------------------------------+     +------------------------+  |
|                                     |                     |                                      |
|                                     v (SPI / Trigger)     v (DAC Dual 16-bit / PWM)              |
|                        +----------------------+ +-------------------------+                      |
|                        | ENTRADAS             | | SALIDAS                 |                      |
|                        | - Módulo ToF Directo | | - Driver Galvanómetro /  |                      |
|                        |   (Detección Eco)    | |   High Voltage MEMS     |                      |
|                        | - Feedback Posición  | | - Control de Potencia   |                      |
|                        |   (PD Galvo Sensors) | |   Láser (Interlock)     |                      |
|                        +----------------------+ +-------------------------+                      |
|                                   ^                          |                                   |
|                                   |                          v                                   |
|                         [ Haz Infrarrojo Eco ]     [ Deflexión Óptica 2D ]                       |
|                                   \                          /                                   |
|                                    +------------------------+                                    |
|                                    | OPTOMECÁNICA (Espejos) |                                    |
|                                    +------------------------+                                    |
====================================================================================================
```

### 5.1 Desglose de Dominios
1. **Fuentes de Alimentación:** Riel principal de $12\text{ VDC}$. Regulación conmutada (Buck) para $+5\text{V}$ ($2\text{A}$) y $+3.3\text{V}$ ($1\text{A}$). Convertidor DC-DC elevador bipolar ($\pm15\text{V}$) dedicado al amplificador operacional de alta velocidad y servo-drivers del galvanómetro óptico (o generador HV $0-100\text{V}$ para MEMS electrostáticos).
2. **Entradas (Acondicionamiento y Sensores):** 
   - Sensor de distancia Time-of-Flight (ToF) por pulso láser directo con salida digital de alta velocidad (SPI/UART/Parallel).
   - Canales ADC de 16-bit para lectura del sensor de posición fotosensible (PD/Encoder) del galvanómetro para closed-loop feedback.
3. **Salidas (Actuación y Potencia):**
   - Salida analógica diferencial mediante DAC externo de 16 bits para accionar los galvanómetros $X/Y$ con respuesta paso rápida.
   - Circuito de modulación de pulso del diodo láser ToF con interlock de seguridad por hardware si falla el movimiento del espejo.
4. **Estructura Computacional:** Microcontrolador de alto rendimiento **STM32H743VI** (ARM Cortex-M7 a $480\text{ MHz}$) con doble precisión FPU y controladores DMA directos. Alternativamente, FPGA Xilinx Zynq-7000 para sincronización estricta por hardware a nivel de nanosegundos.
5. **HMI / MMI (Interfaz Hombre-Máquina):** Indicadores LED RGB para "Power Good", "Laser Active", "Scan Sync" y "Link Ethernet". Botón de inicio de calibración offset de plano.
6. **M2MC (Comunicación Máquina a Máquina):** Transceptor Gigabit Ethernet (PHY KSZ9031) sobre conector RJ45 industrial M12 o USB 3.0 Type-C con emulación de dispositivo VCP/RNDIS de alta velocidad.
7. **Ingeniería de Software / Información:**
   - **Firmware Embebido:** Real-Time Operating System (FreeRTOS). Tarea de disparo ToF sincronizada con Timer de hardware a la posición del espejo. Búfer circular DMA en doble bancada.
   - **Software Host (PC):** Pipeline de procesamiento en C++/Python usando la librería **Open3D** o **PCL (Point Cloud Library)**. Recibe paquetes UDP, transforma coordenadas esféricas $(r, \theta, \phi) \rightarrow (x, y, z)$ aplicando la matriz de calibración óptica, y genera la malla de superficie 3D.
8. **Diseño Mecánico e Industrial:** Chasis de aluminio anodizado maquinado en CNC para disipación térmica y alineación óptica rígida. Módulo con montaje en jaula estándar de $30\text{ mm}$ de laboratorio para fácil sustitución de espejos y lentes collimators.

---

## 6. Selección y Análisis Comparativo de Actuación Óptica: MEMS vs. Galvanómetros

Basado en el catálogo de componentes comerciales disponibles (Tabla de entrada):

| Criterio | Opción A: Galvanómetro Óptico (ej. Cambridge 6210H / ScannerMAX Compact 506) | Opción B: MEMS Mirror 2D (ej. Mirrorcle A7M20.2 / Hamamatsu S13989-01H) | Selección y Justificación |
| :--- | :--- | :--- | :--- |
| **Apertura del Espejo** | Grande ($3\text{ mm} - 10\text{ mm}$). Permite haces láser de mayor diámetro y mayor captación de luz reflejada. | Pequeña ($0.8\text{ mm} - 2.6\text{ mm}$). Requiere colimación láser muy fina y alineación crítica. | **Galvanómetro:** Facilita la recolección del haz ToF infrarrojo sin pérdidas severas por difracción. |
| **Ángulo de Escaneo** | Alto ($\pm 20^\circ$ mech / $40^\circ$ óptico). FOV amplio sin necesidad de lentes adicionales. | Moderado ($\pm 4.75^\circ$ a $\pm 15^\circ$ óptico). Requiere lentes expansoras de ángulo. | **Galvanómetro:** Proporciona un FOV directo de $40^\circ \times 40^\circ$ ideal para mapeo 3D. |
| **Velocidad y Frecuencia** | Respuesta paso $200\ \mu\text{s} - 400\ \mu\text{s}$. Frecuencia máxima $100 - 500\text{ Hz}$. | Muy alta en eje resonante ($1.3\text{ kHz} - 29\text{ kHz}$). | **MEMS:** Superior para escaneo ultra rápido, pero más complejo de sincronizar linealmente. |
| **Robustez y Tamaño** | Volumen mayor ($56 \times 36 \times 54\text{ mm}$), masa rotacional detectable. | Ultra compacto (Die $< 7 \times 7\text{ mm}$), consumo $< 100\text{ mW}$. | **MEMS:** Ideal para integración en handheld/drones. **Galvo:** Ideal para estaciones fijas/robótica. |
| **Costo Prototipo** | $USD\ 475$ (Galvo solo) / $USD\ 1,120$ (Kit completo con Driver). | $USD\ 199 - 920$ según módulo/driver. | **Selección del Prototipo:** Se selecciona **ScannerMAX Compact 506 / Cambridge 6210H** o Kit Galvo Industrial 2D de alta precisión para el prototipo V1 por su mayor apertura óptica y control vectorial flexible. |

---

## 7. Esquema NABC (Need, Approach, Benefits, Competition)

### **N - Need (Necesidad)**
Las empresas e investigadoras en automatización requieren una herramienta de escaneo 3D volumétrico que combine la precisión y rango de un LiDAR ToF con la flexibilidad de un patrón de escaneo configurable, a una fracción del costo de los LiDARs giratorios comerciales ($> USD 3,000$). Los sistemas actuales o sufren de baja densidad de puntos (sensores ToF 1D) o son extremadamente frágiles y pesados.

### **A - Approach (Enfoque / Solución)**
Proponemos un **Sensor LiDAR 3D Híbrido** que integra:
1. Módulo de medición de distancia ToF por pulsos de alta tasa ($> 50\text{ kSPS}$).
2. Cabezal de escaneo 2D de galvanómetros ópticos rápidos con espejos recubiertos de plata protegida / oro.
3. Unidad de control determinista (ARM Cortex-M7 / FPGA) que realiza el disparo láser, mapeo angular $X/Y$ mediante DAC de 16 bits y empaquetado directo de nube de puntos en tiempo real.
4. Un pipeline en el Host que convierte el stream UDP de coordinadas esféricas en mallas de reconstrucción 3D (PLY/PCD).

### **B - Benefits (Beneficios)**
- **Relación Costo/Desempeño Excepcional:** Prototipo realizable por $< USD 700$ con calidad metrológica en rangos de $0.2\text{ m} - 10\text{ m}$.
- **Patrón de Escaneo Flexible:** A diferencia de los LiDARs giratorios de patrón fijo, se pueden programar regiones de interés (ROI) con mayor densidad de puntos en zonas críticas.
- **Formato Módulo Embebido:** Sin partes mecánicas externas expuestas; construcción sólida y fácil integración en sistemas de visión robótica.

### **C - Competition (Competencia y Diferenciadores)**

| Solución / Competidor | Ventajas Competidor | Desventajas Competidor | Diferenciador de Nuestra Solución |
| :--- | :--- | :--- | :--- |
| **LiDAR Giratorio (ej. Velodyne VLP-16, Ouster OS1)** | 360° FOV horizontal, muy maduro en el mercado. | Costo elevado ($> \$3,500$), alto peso y consumo, patrón fijo. | **Costo 80% menor**, densidad de puntos programable y enfocable en ROI. |
| **Cámaras ToF / Flash LiDAR (ej. Intel RealSense D435i / O3R IFM)** | Sin partes móviles, de bajo costo ($USD\ 300-600$). | Rango limitado ($< 4\text{ m}$), alta sensibilidad a la luz solar exterior. | **Mayor alcance ($10\text{ m}$)**, resiliencia al ruido ambiental mediante haz concentrado. |
| **Kits LiDAR con Galvo Industrial de Gama Alta** | Ultra alta velocidad y precisión sub-milimétrica. | Costo alto ($> \$8,000$), voluminoso y requiere controladores propietarios. | **Arquitectura abierta**, protocolo de red estándar y controlador compacto de bajo costo. |

---

## 8. Comentarios y Conclusiones

El presente documento establece la especificación técnica completa y los requerimientos del prototipo **DR-LiDAR-01**. La integración de un sistema de deflexión por Galvanómetros / MEMS con un sensor ToF representa una alternativa balanceada entre costo, velocidad de respuesta y densidad espacial de puntos.

**Limitaciones y Riesgos Identificados:**
1. **Pérdida de Potencia Óptica:** La reflectancia de los espejos y el tamaño de la pupila de entrada pueden atenuar la señal de retorno ToF. Se requerirá un lente condensador de entrada de mayor apertura.
2. **Sincronización Térmica del Espejo:** El drift térmico en los drivers analógicos del galvo puede afectar la calibración angular. Se implementará un procedimiento de autocalibración de cero al encendido.

**Pasos Siguientes:**
- Validar esquemáticos de la tarjeta de control de potencia y acondicionamiento DAC/ADC.
- Ejecutar pruebas en banco óptico del acoplamiento entre el haz ToF y el cabezal Galvo seleccionando la lista del BOM.

---

## 9. Control de Revisión y Aprobación

| Revisión | Responsable | Fecha | Estado / Observaciones |
| :--- | :--- | :--- | :--- |
| **0.1** | Equipo de Diseño Electrónico | 14 de Septiembre, 2026 | Borrador inicial de arquitectura y requerimientos. |
| **0.2** | Líder de Proyecto / Revisión | - | Pendiente de validación de simulación óptica. |
| **1.0** | Aprobación Final | - | Aprobado para diseño de PCB y compra de BOM. |
