# MYO-RECON-01 | Especificación de Sistema y Arquitectura Técnica
## Brazalete Inteligente de Bioseñales (sEMG) e Inerciales (IMU) para Detección de Gestos por Machine Learning y Reconstrucción Cinemática Completa de la Mano
**Departamento de Ingeniería Electrónica y Computación**  
*Documento de Diseño y Requerimientos de Producto (Rev. 0.1 - Septiembre 2026)*

---

## 1. Definición de la Aplicación

### 1.1 Necesidad
La interacción hombre-máquina (HMI) natural, el control de prótesis biónicas de miembro superior, la teleoperación de sistemas robóticos y la inmersión en realidad aumentada y virtual (AR/VR) requieren capturar los movimientos detallados de la mano sin el uso de guantes estorbosos, cámaras externas limitadas por oclusión visual o marcadores ópticos. Existe una creciente necesidad de un dispositivo wearable miniaturizado tipo brazalete que se adapte al antebrazo y sea capaz de inferir la intención motora directamente desde la actividad mioeléctrica de la musculatura antebraquial y la orientación espacial inercial.

### 1.2 Problema
El desarrollo de un sistema wearable de electromiografía de superficie (sEMG) e inercial (IMU) enfrenta severos desafíos interdisciplinarios:
1. **Naturaleza Estocástica y Baja Amplitud de la Señal sEMG:** La actividad mioeléctrica captured en la piel presenta amplitudes ultra-bajas ($10\ \mu\text{V}$ a $5\text{ mV}$) con un ancho de banda de $10\text{ Hz}$ a $500\text{ Hz}$, altamente susceptible a ruido de red eléctrica ($50/60\text{ Hz}$), artefactos de movimiento del cable/electrodo, impedancia variable de la piel por sudoración y diafonía (*crosstalk*) entre músculos adyacentes del antebrazo.
2. **Desplazamiento del Dispositivo y Variabilidad Inter-Usuario:** La colocación física del brazalete varía en cada sesión. Las firmas mioeléctricas cambian según la fatiga muscular, la postura del brazo y las características anatómicas de cada individuo.
3. **Reconstrucción Cinemática Causal y en Tiempo Real:** Mapear la combinación de señales sEMG y datos inerciales hacia la cinemática de $21$ grados de libertad (DOF) de los dedos de la mano requiere una arquitectura de inferencia con latencia imperceptible ($< 30\text{ ms}$) y consumo energético restringido ($< 250\text{ mW}$) para garantizar autonomía en la batería.

### 1.3 Propiedad Intelectual (PI) y Estado del Arte
- **Estado del Arte:** Dispositivos comerciales descontinuados como el *Myo Armband* (Thalmic Labs) demostraron la viabilidad del concepto pero sufrían de baja resolución de muestreo ($200\text{ Hz}$ sEMG de 8 bits), deriva inercial y baja tasa de clasificación de gestos estáticos. Proyectos de investigación (como Meta CTRL-labs) emplean arreglos de sEMG de alta densidad (*HD-sEMG*) con procesamiento en la nube, inaccesibles para soluciones ultra-portátiles y de bajo costo.
- **Estrategia de PI:** Desarrollo de un esquema de **Acondicionamiento Analógico Dinámico de Ganancia Adaptativa y Supresión Activa de Modo Común (Driven Right Leg - RLD)**, acoplado a un pipeline de **Extracción de Características en Dominio del Tiempo-Frecuencia (TD-FD)** integrado en hardware TinyML (ARM Cortex-M7 con aceleración DSP/Vectorial) para la estimación continua de los ángulos articulares metacarpofalángicos e interfalángicos.

### 1.4 Oportunidad de Negocio
El mercado de la robótica asistencial, prótesis mioeléctricas, interfaces para videojuegos y control industrial en entornos peligrosos demanda dispositivos de captura de intención motora no invasivos. Un brazalete con costo de BOM de $\sim USD\ 170$ y un precio de venta objetivo de $USD\ 450 - 600$ abre oportunidades masivas en rehabilitación física digital, traducción automática de lengua de señas y control HMI contextual.

---

## 2. Casos de Uso del Sistema

| Caso | Descripción | Requerimientos Derivados |
| :--- | :--- | :--- |
| **1** | **Clasificación de Gestos Discretos Tácticos/HMI:** Reconocimiento en tiempo real de gestos estáticos (puño cerrado, mano abierta, pellizco/pinch, victoria, apuntar). | Algoritmo de clasificación TinyML (SVM/Random Forest/TCN), latencia $< 20\text{ ms}$, precisión $> 95\%$. |
| **2** | **Reconstrucción Continua de Ángulos Articulares de la Mano:** Estimación en tiempo real de los $21$ ángulos de las articulaciones de la mano para avatars en VR/AR. | Muestreo sEMG a $1000\text{ Hz}$, vector de características RMS/MAV/WL, red neuronal convolucional liviana (1D-CNN / GRU). |
| **3** | **Control Prostético Mioeléctrico Multi-Agarre:** Transmisión de comandos de fuerza y tipo de agarre a una mano robótica biónica. | Salida analógica/digital o BLE de alta confiabilidad, tolerancia a fallas de contacto de electrodos secos. |
| **4** | **Rehabilitación Neuromuscular Post-Ictus (Stroke):** Monitoreo de la activación muscular del paciente durante ejercicios de terapia ocupacional. | Almacenamiento continuo de métricas de fatiga (Frecuencia Media $MNF$ y Frecuencia Central $MDF$), streaming BLE 5.3. |
| **5** | **Traducción Dinámica de Lengua de Señas:** Captura combinada del movimiento del antebrazo (IMU) y la micro-gesticulación de los dedos (sEMG). | Sensor Fusion 9-DOF (Acelerómetro + Giroscopio + Magnetómetro/Quaternion) a $100\text{ Hz}$ sincronizado con sEMG. |
| **6** | **Teleoperación de Manipuladores Robóticos Industriales:** Manejo intuitivo de garras o pinzas robóticas a distancia. | Retroalimentación háptica en tiempo real mediante motor LRA para indicar contacto del robot con un objeto. |
| **7** | **Control de Dispositivos Domóticos sin Contacto:** Encendido de luces, volumen o navegación en pantallas mediante micro-gestos. | Filtrado de artefactos de movimiento mientras el usuario camina o mueve los brazos sin intención de gesticular. |
| **8** | **Estimación Proporcional de Fuerza de Agarre:** Evaluación de la intensidad de contracción isométrica para manipular objetos frágiles. | Calibración de la métrica RMS de la señal sEMG mapeada dinámicamente a newtons de fuerza ($N$). |
| **9** | **Autocalibración Rápida Inter-Sesión:** Adaptación rápida del modelo ML a la anatomía y posición actual del brazalete en $< 30\text{ segundos}$. | Transfer Learning ligero ejecutable on-device o mediante aplicación complementaria móvil. |
| **10**| **Grabación de Datos Biomecánicos para Data Mining:** Registro sincrónico de señal sEMG cruda ($RAW$) de 8 canales para investigación médica. | Modo USB Streaming UVC/CDC a $2\text{ MB/s}$ sin pérdidas de paquetes. |

---

## 3. Requerimientos del Producto

| ID | Descripción del Requerimiento | Tipo | Prioridad (MoSCoW) |
| :--- | :--- | :--- | :--- |
| **F-01** | El sistema debe capturar señales biopotenciales de sEMG de 8 canales diferenciales distribuidos circularmente en el antebrazo. | Funcional | **Must** |
| **F-02** | El sistema debe incorporar un sensor IMU de 6 ejes para medir aceleración triaxial y velocidad angular del antebrazo. | Funcional | **Must** |
| **F-03** | El circuito analógico debe integrar un sistema de circuito de pierna derecha impulsada (RLD/DRL) para la supresión activa de ruido de modo común. | Funcional | **Must** |
| **F-04** | El firmware debe ejecutar extracción de características en el dominio del tiempo (MAV, RMS, WL, ZC, SSC) a intervalos de $10\text{ ms}$. | Funcional | **Must** |
| **F-05** | El dispositivo debe realizar inferencia on-device con un modelo TinyML para clasificar al menos 8 gestos de mano independientes. | Funcional | **Must** |
| **F-06** | El sistema debe transmitir la postura estimada de la mano (puntos clave 3D o ángulos) vía BLE 5.3 o USB a $60\text{ Hz}$. | Funcional | **Must** |
| **F-07** | El dispositivo debe incluir un actuador háptico (LRA) para proveer retroalimentación táctil al usuario ante eventos o confirmación de gestos. | Funcional | **Should** |
| **F-08** | El sistema debe disponer de una batería recargable Li-Po integrada con circuito de gestión PMIC y carga USB-C. | Funcional | **Must** |
| **F-09** | La unidad debe permitir la grabación y exportación de datos en bruto (sEMG @ 1 kHz, IMU @ 100 Hz) para desarrollo de modelos ML. | Funcional | **Should** |
| **F-10** | El software debe ofrecer un modo de calibración asistida para corregir el sesgo por rotación del brazalete en el brazo. | Funcional | **Could** |
| **NF-01**| La latencia total desde la contracción muscular hasta la salida del gesto infalible debe ser $\le 25\text{ ms}$. | No Funcional | **Must** |
| **NF-02**| La tasa de muestreo por canal sEMG debe ser de al menos $1000\text{ muestras/s}$ con una resolución $\ge 16\text{ bits}$. | No Funcional | **Must** |
| **NF-03**| La relación de rechazo de modo común (CMRR) del sistema analógico sEMG debe ser $\ge 110\text{ dB}$. | No Funcional | **Must** |
| **NF-04**| El nivel de ruido referido a la entrada (RTI noise) del AFE sEMG debe ser $< 1.5\ \mu\text{V}_{RMS}$ en el ancho de banda $10-500\text{ Hz}$. | No Funcional | **Must** |
| **NF-05**| La autonomía continua de la batería debe ser de al menos $6\text{ horas}$ en modo streaming inalambrico activo. | No Funcional | **Must** |
| **NF-06**| El peso total del dispositivo no debe superar los $110\text{ gramos}$. | No Funcional | **Should** |
| **NF-07**| La envolvente física debe contar con grado de protección IP67 y electrodos secos resistentes a la corrosión por sudor. | No Funcional | **Should** |
| **NF-08**| El costo de fabricación del prototipo (BOM) no debe superar los $USD\ 200$. | No Funcional | **Must** |

---

## 4. Requerimientos del Sistema (SRD)

| ID | Parámetro / Descripción | Especificación / Valor Target | Método de Verificación |
| :--- | :--- | :--- | :--- |
| **SR-01**| Batería Principal | Li-Po $3.7\text{ V}$ nominal, $450\text{ mAh}$ en formato curvado/flexible | Prueba de descarga continua bajo carga máxima del microcontrolador y BLE. |
| **SR-02**| Sub-rieles de Potencia | $+3.3\text{V}$ (Digital MCU/BLE), $+3.3\text{V}_{AVDD}$ (Analógico Ultra-Low Noise) | Medición de rizado con osciloscopio en PCB ($< 5\text{ mV}_{pp}$). |
| **SR-03**| AFE sEMG (Front-End Analógico) | Texas Instruments ADS1298 (8 canales, ADC 24-bit Delta-Sigma) | Medición con generador de funciones biopotenciales ($10\ \mu\text{V} - 5\text{ mV}$). |
| **SR-04**| Electrodos Secos | Matriz de 8 pares diferenciales de latón dorado / Ag-AgCl seco | Prueba de impedancia de contacto piel-electrodo ($< 100\text{ k}\Omega$ a $100\text{ Hz}$). |
| **SR-05**| Sensor Inercial (IMU) | ICM-42688-P de 6 ejes ($\pm 16\text{g}$, $\pm 2000^\circ/\text{s}$) | Calibración en mesa giratoria e integración cinemática. |
| **SR-06**| Procesador Principal | STM32H723VGT6 (ARM Cortex-M7 @ $550\text{ MHz}$, $1\text{ MB}$ Flash, $564\text{ KB}$ RAM) | Prueba de benchmarks DSP / CMSIS-NN y tiempos de ejecución del vector de características. |
| **SR-07**| Coprocesador Inalámbrico | Nordic nRF52840 (ARM Cortex-M4F @ $64\text{ MHz}$) vía SPI/UART | Verificación de Throughput BLE 5.3 ($> 1\text{ Mbps}$). |
| **SR-08**| Interfaz Táctil / Actuación | Driver DRV2605L acoplado a un actuador LRA | Medición de aceleración de vibración y tiempos de respuesta háptica ($< 10\text{ ms}$). |
| **SR-09**| Ancho de Banda Filtro Analógico/Digital | Filtro Pasa-Banda Chebyshev/Butterworth $10\text{ Hz} - 450\text{ Hz}$ + Notch $50/60\text{ Hz}$ | Análisis de respuesta en frecuencia con barrido sinusoidal. |
| **SR-10**| Algoritmo Inercial | Filtro de Madgwick / Mahony a $100\text{ Hz}$ para estimación de Cuaterniones | Validación en simulador 3D de orientación angular. |
| **SR-11**| Formato Flex-Rígido de PCB | PCB de 4 capas Flex-Rígido con refuerzo (stiffener) en zonas de componentes | Ensayos de flexión mecánica ($> 10,000$ ciclos a $45^\circ$). |
| **SR-12**| Seguridad Eléctrica Médica | Aislamiento dieléctrico y límites de corriente de fuga a tierra $< 10\ \mu\text{A}$ (IEC 60601-1) | Medición con probador de corriente de fuga bajo fallas simuladas. |

---

## 5. Arquitectura Técnica y Pilares del Diseño Electrónico

```
                  =====================================================================
                  |                     SISTEMA EXTERNO (HOST / VR / ROBOT)           |
                  |   - Reconstrucción 3D de la Mano (OpenXR / Unreal / Unity / ROS) |
                  |   - Visualización de Actividad sEMG y Telemetría Háptica           |
                  =====================================================================
                                                   ^
                                                   | (BLE 5.3 Low Latency / USB-C CDC)
                                                   v
====================================================================================================
| BRAZALETE INTELIGENTE sEMG + IMU (MYO-RECON-01)                                                   |
|                                                                                                  |
|  +-----------------------+     +-------------------------------+     +------------------------+  |
|  | FUENTES DE POTENCIA   |     | ESTRUCTURA COMPUTACIONAL      |     | M2MC & HMI             |  |
|  | - Bat Li-Po 3.7V 450mAh|---->| - MCU STM32H723 (550MHz M7)   |<--->| - SoC nRF52840 (BLE 5.3)|  |
|  | - PMIC BQ25120A       |     | - DSP/CMSIS-NN Features ML    |     | - Actuador Háptico LRA |  |
|  | - LDO TPS7A20 (3.3V)  |     | - Fusion IMU + sEMG Kinematics|     | - LED RGB de Estado    |  |
|  +-----------------------+     +-------------------------------+     +------------------------+  |
|                                     |                     |                                      |
|                                     v (SPI 20MHz)         v (I2C / PWM)                          |
|                        +----------------------+ +-------------------------+                      |
|                        | ENTRADAS             | | SALIDAS                 |                      |
|                        | - 8x Electrodos Secos| | - Driver Háptico DRV2605|                      |
|                        |   sEMG Diferenciales | |   con Patrones Táctiles |                      |
|                        | - AFE ADS1298 24-bit | | - Control de Carga PMIC |                      |
|                        | - IMU ICM-42688 6-Axis| |                         |                      |
|                        +----------------------+ +-------------------------+                      |
|                                   ^                                                              |
|                                   |                                                              |
|                         [ Captura Biopotencial & Músculo ]                                       |
|                                   \                                                              |
|                                    +------------------------+                                    |
|                                    | ANTEBRAZO DEL USUARIO  |                                    |
|                                    +------------------------+                                    |
====================================================================================================
```

### 5.1 Desglose de Dominios

1. **Fuentes de Alimentación:**
   - Pack de batería Li-Po curvada de $3.7\text{ V}$ ($450\text{ mAh}$) con circuito integrado de protección (PCM) contra sobrecarga, sobredescarga y cortocircuito.
   - PMIC ultrabajo consumo **BQ25120A** que integra cargador lineal por USB-C, regulador Buck con eficiencia del $90\%$ para el dominio digital ($1.8\text{V}-3.3\text{V}$) e indicador de carga I2C.
   - Regulador **LDO TPS7A20** de ultra-bajo ruido ($9\ \mu\text{V}_{RMS}$) y elevado PSRR ($> 65\text{ dB}$ a $1\text{ kHz}$) dedicado exclusivamente a alimentar los rieles analógicos del AFE ADS1298 y los búferes de electrodos para prevenir el acople de ruido digital.

2. **Entradas (Acondicionamiento Analógico y Sensórica):**
   - **Matriz de Electrodos Secos:** 8 pares de electrodos de contacto seco en latón chapado en oro de alta durabilidad, integrados en la cara interna del brazalete.
   - **Front-End Analógico (AFE):** **ADS1298**, un integrado especializado de 8 canales biopotenciales diferenciales de 24 bits con amplificadores de ganancia programable (PGA $1\times$ a $12\times$).
   - **Circuito RLD (Right Leg Drive):** Módulo de retroalimentación en bucle cerrado que amplifica de forma invertida la señal de modo común de los 8 canales sEMG y la inyecta al tejido mediante un noveno electrodo de referencia, logrando un $CMRR > 110\text{ dB}$.
   - **Sensor Inercial:** **ICM-42688-P** montado en el PCB principal para registrar la aceleración y velocidad angular del antebrazo con resolución de 16 bits.

3. **Salidas (Actuación y Feedback Háptico):**
   - **Retroalimentación Táctil:** Driver de hápticos **DRV2605L** acoplado a un motor lineal resonante (LRA) que genera patrones de vibración configurables (confirmación de gesto registrado, alerta de baja batería, fatiga muscular o límites mecánicos en prótesis).

4. **Estructura Computacional:**
   - **Procesador de Alto Rendimiento:** Microcontrolador **STM32H723VGT6** (ARM Cortex-M7 a $550\text{ MHz}$, con unidad de punto flotante de doble precisión FPU y extensiones de instrucción vectoriales DSP). Ejecuta el filtrado digital en tiempo real, extracción de características sEMG y el modelo TinyML.
   - **Memoria Interna:** $1\text{ MB}$ Flash de alta velocidad y $564\text{ KB}$ de SRAM dividida en bloques TCM (*Tightly Coupled Memory*) para ejecución determinista sin estados de espera.

5. **HMI / MMI (Interfaz Hombre-Máquina):**
   - LED RGB discreto de indicación de estado (Azul: Emparejando BLE, Verde: Operación Normal, Amarillo: Modo Calibración, Rojo: Error/Batería Baja).
   - Botón capacitivo sellado sobre la carcasa para encendido/apagado y disparo manual del ciclo de autocalibración rápida.

6. **M2MC (Comunicación Máquina a Máquina):**
   - Coprocesador inalámbrico dedicado **nRF52840** interconectado vía SPI de alta velocidad con el STM32H723. Implementa el stack BLE 5.3 con perfil custom para transmisión de streaming con bajo consumo y soporte PHY $2\text{ Mbps}$.
   - Puerto físico USB-C con protección ESD y supresión TVS para carga de batería y transferencia de datos raw a $1\text{ MB/s}$.

7. **Ingeniería de Software / Pipeline de Datos:**
   - **Firmware Embebido:** Real-Time Operating System (FreeRTOS) gestionando 4 tareas críticas priorizadas:
     1. *Adquisición DMA:* Captura sEMG a $1000\text{ Hz}$ por canal e IMU a $100\text{ Hz}$.
     2. *DSP Filtering:* Filtro pasa-banda Butterworth 4to orden ($10-450\text{ Hz}$) y Notch IIR ($50/60\text{ Hz}$).
     3. *Feature Extraction Windowing:* Ventanas deslizantes de $200\text{ ms}$ con traslape de $10\text{ ms}$ (paso de $10\text{ ms}$).
     4. *TinyML Inferencia:* Ejecución de red neuronal convolucional 1D o Temporal Convolutional Network (TCN) optimizada mediante TensorFlow Lite for Microcontrollers (TFLM) / CMSIS-NN.

8. **Diseño Mecánico y Conformado Flex-Rígido:**
   - Construcción en PCB Flex-Rígido de 4 capas dispuesto en forma de anillo cerrado autoajustable con bandas de silicona médica de grado biomecánico (ISO 10993).
   - Envolvente hermética IP67 resistente al sudor, agua y limpieza higiénica continua.

---

## 6. Pipeline de Procesamiento de Bioseñales y Machine Learning

El mapa funcional desde la captura de microvoltios en la piel hasta la reconstrucción cinemática continua de los $21$ grados de libertad (DOF) de la mano se articula en 4 etapas principales:

```
+-----------------------------------------------------------------------------------+
| 1. ACONDICIONAMIENTO ANALÓGICO Y CAPTURA                                          |
|    - 8 Canales sEMG Dif. (ADS1298 @ 1000 SPS, PGA=6) + RLD Active Common Mode      |
|    - IMU 6-Ejes (ICM-42688-P @ 100 SPS)                                            |
+-----------------------------------------------------------------------------------+
                                         |
                                         v
+-----------------------------------------------------------------------------------+
| 2. FILTRADO DIGITAL Y EXTRACCIÓN DE CARACTERÍSTICAS (DSP @ STM32H723)            |
|    - Band-pass 10-450 Hz + Notch IIR 50/60 Hz                                     |
|    - Ventana Deslizante = 200 ms, Overlap = 190 ms (Actualización cada 10 ms)     |
|    - Métricas sEMG: MAV, RMS, WL, ZC, SSC, V-Order                                |
|    - Métricas IMU: Quaternions (q0, q1, q2, q3) vía Filtro de Madgwick            |
+-----------------------------------------------------------------------------------+
                                         |
                                         v
+-----------------------------------------------------------------------------------+
| 3. INFERENCIA ON-DEVICE (TinyML / CMSIS-NN)                                       |
|    - Entradas: Vector de 8 canales x 5 características + 4 Quaternions = 44 vars      |
|    - Clasificador de Gestos Discretos: CNN 1D / Random Forest Quantized (INT8)    |
|    - Estimación Proporcional de Fuerza: Mapping no lineal MAV -> Fuerza (N)       |
+-----------------------------------------------------------------------------------+
                                         |
                                         v
+-----------------------------------------------------------------------------------+
| 4. RECONSTRUCCIÓN CINEMÁTICA CONTINUA DE LA MANO (On-Device / Host)               |
|    - Modelo de Regresión Multivariada / TCN (Temporal Convolutional Network)      |
|    - Predicción de Ángulos Articulares 21-DOF (MCP, PIP, DIP de 5 dedos)           |
|    - Inserción en Malla 3D Cinemática Inversa (Inverse Kinematics Solver)        |
+-----------------------------------------------------------------------------------+
```

### 6.1 Ecuaciones Matemáticas de Extracción de Características

Para cada canal $i \in \{1, \dots, 8\}$ dentro de una ventana de $N$ muestras ($N = 200$ a $1000\text{ Hz}$):

1. **Valor Absoluto Medio (MAV):**
   $$MAV_i = \frac{1}{N} \sum_{k=1}^{N} |x_{i}[k]|$$

2. **Raíz Cuadrada Media (RMS):**
   $$RMS_i = \sqrt{\frac{1}{N} \sum_{k=1}^{N} x_{i}[k]^2}$$

3. **Longitud de Onda (Waveform Length - WL):**
   $$WL_i = \sum_{k=1}^{N-1} |x_{i}[k+1] - x_{i}[k]|$$

4. **Cruces por Cero (Zero Crossings - ZC):**
   $$ZC_i = \sum_{k=1}^{N-1} f(x_{i}[k], x_{i}[k+1]), \quad f(a,b) = \begin{cases} 1 & \text{si } a \cdot b < 0 \text{ y } |a - b| \ge \epsilon \\ 0 & \text{en otro caso} \end{cases}$$

5. **Cambios de Signo de la Pendiente (Slope Sign Changes - SSC):**
   $$SSC_i = \sum_{k=2}^{N-1} g(x_{i}[k-1], x_{i}[k], x_{i}[k+1])$$
   $$g(a,b,c) = \begin{cases} 1 & \text{si } (b - a)(b - c) > 0 \text{ y } |b - a| \ge \epsilon \text{ o } |b - c| \ge \epsilon \\ 0 & \text{en otro caso} \end{cases}$$

---

## 7. Esquema NABC (Need, Approach, Benefits, Competition)

### **N - Need (Necesidad)**
Las aplicaciones de robótica asistencial, prótesis mioeléctricas modernas, entornos inmersivos de AR/VR y la comunicación accesible requieren capturar con alta fidelidad la postura y gesticulación de la mano sin depender de cámaras externas sujetas a oclusión ni de guantes voluminosos que limitan la destreza del usuario.

### **A - Approach (Enfoque / Solución)**
Proponemos un **Brazalete Integrado sEMG + IMU Ultra-Portátil** que combina:
1. Front-End Analógico de 24 bits (ADS1298) con 8 electrodos secos adaptativos y supresión activa de modo común (RLD).
2. Sensor Inercial de 6 ejes con filtro de orientación Madgwick integrado a $100\text{ Hz}$.
3. Microcontrolador ARM Cortex-M7 de $550\text{ MHz}$ con aceleración DSP para la extracción de características en ventanas de $10\text{ ms}$ e inferencia de gestos TinyML on-device.
4. Conectividad inalámbrica BLE 5.3 con protocolo de baja latencia y canal USB-C streaming directo.

### **B - Benefits (Beneficios)**
- **Destreza Sin Oclusiones:** Permite la reconstrucción del movimiento de los dedos sin guantes ni cámaras.
- **Ultra-Baja Latencia ($< 25\text{ ms}$):** Procesamiento totalmente local en la MCU que garantiza una respuesta instantánea.
- **Económico y Accesible:** Prototipado realizable con costo BOM $< USD\ 180$, ofreciendo rendimiento comparable a sistemas médicos e industriales de miles de dólares.

### **C - Competition (Competencia y Diferenciadores)**

| Solución / Competidor | Ventajas Competidor | Desventajas Competidor | Diferenciador de Nuestra Solución |
| :--- | :--- | :--- | :--- |
| **Thalmic Labs Myo Armband (Descontinuado)** | Pionero en la industria, diseño estético. | ADC de 8 bits a $200\text{ Hz}$, electodemia ruidosa, baja tasa de reconocimiento. | **ADC 24-bit @ 1000 Hz**, AFE de grado médico ADS1298, TinyML para cinemática continua. |
| **Guantes de Captura de Movimiento (ej. Manus VR / CyberGlove)** | Alta precisión angular de dedos mediante flexores. | Incomodidad de uso prolongado, sudoración, fragilidad mecánica, costo elevado ($> \$2,500$). | **Formato brazalete no invasivo**, mano completamente libre sin cables en los dedos. |
| **Cámaras Tracking Óptico (Leap Motion / Meta Quest Hand Tracking)** | Sin componentes en el cuerpo. | Oclusión visual cuando la mano se voltea o se entrelaza, fallo en oscuridad total. | **Inmune a oclusiones ópticas**, opera en oscuridad total o bajo ropas. |

---

## 8. Comentarios y Conclusiones

El presente documento establece la especificación técnica y la arquitectura del brazalete **MYO-RECON-01**. El diseño integra instrumentación analógica de ultra-bajo ruido con cómputo heterogéneo TinyML, superando los límites de los dispositivos mioeléctricos tradicionales.

**Riesgos Técnicos Identificados:**
1. **Variación de la Impedancia Piel-Electrodo:** El sudor o la sequedad epidérmica alteran la ganancia de la señal sEMG. Se mitiga mediante un filtro adaptativo de ganancia automática (AGC) en software y el uso de electrodos secos con recubrimiento de oro biocompatible.
2. **Crosstalk Muscular en el Antebrazo:** La contracción de un músculo profundo genera interferencia en múltiples electrodos contiguos. Se resolverá mediante la matriz de desarmado de características por componentes independientes (ICA) o PCA en el pipeline TinyML.

---

## 9. Control de Revisión y Aprobación

| Revisión | Responsable | Fecha | Estado / Observaciones |
| :--- | :--- | :--- | :--- |
| **0.1** | Equipo de Ingeniería Biomédica y Firmware | 15 de Septiembre, 2026 | Borrador inicial de arquitectura de bioseñales sEMG y pipeline ML. |
| **0.2** | Líder de Hardware y Diseño Electrónico | - | Aprobación de esquemático analógico AFE y PCB Flex-Rígido. |
| **1.0** | Director de I+D | - | Aprobado para fabricación del primer prototipo físico. |
