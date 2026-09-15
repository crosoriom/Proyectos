# NV-SMART-01 | Especificación de Sistema y Arquitectura Técnica
## Sistema de Visión Nocturna Inteligente Tri-Banda (Visible / NIR / LWIR) con Computational Low-Light Imaging y Fusión Multiespectral
**Departamento de Ingeniería Electrónica y Computación**  
*Documento de Diseño y Requerimientos de Producto (Rev. 0.1 - Septiembre 2026)*

---

## 1. Definición de la Aplicación

### 1.1 Necesidad
En operaciones de búsqueda y rescate (SAR), seguridad perimetral nocturna, defensa, conservación de vida silvestre y navegación de vehículos terrestres/marítimos en ausencia total de luz o en condiciones atmosféricas adversas (niebla, humo, lluvia intensa), los sistemas de visión convencionales son insuficientes:
- La visión nocturna analógica por tubos intensificadores (Gen 2+/3) no detecta firmas térmicas pasivas, es vulnerable al deslumbramiento (*blooming*) y carece de capacidad de registro o análisis de video.
- Las cámaras térmicas puras (LWIR - *Long-Wave Infrared*) entregan excelente contraste térmico pero carecen de textura, contexto espacial de alta resolución y capacidad para leer señales o reconocer rostros.
- Las cámaras digitales de baja luminosidad (NIR/Vis) fallan en oscuridad total ($< 0.0001\text{ lux}$) a menos que activen iluminadores IR que delatan la posición del emisor.

Existe la necesidad urgente de una unidad de visión nocturna integrada y portátil que combine el espectro visible de ultra-baja luz, el infrarrojo cercano (NIR) y la radiación térmica (LWIR) en un único canal de visualización fusionado mediante inteligencia artificial embebida y procesamiento computacional de imágenes.

### 1.2 Problema
El desafío técnico principal radica en balancear de forma óptima tres parámetros interdependientes y contrapuestos:

$$\text{Sensibilidad Óptica} \longleftrightarrow \text{Velocidad de Respuesta / Latencia} \longleftrightarrow \text{Resolución Espacial}$$

1. **Sensibilidad vs. Velocidad:** Acumular fotones mediante mayor tiempo de integración aumenta la sensibilidad en baja luz, pero introduce *motion blur* (borrosidad por movimiento) y reduce los cuadros por segundo ($fps$), inaceptable para visión táctica o en movimiento.
2. **Filtrado de Ruido sin Pérdida de Detalle:** En iluminación estelar ($< 0.001\text{ lux}$), el ruido térmico y de lectura (*Read Noise / Shot Noise*) domina el sensor CMOS. El filtrado extremo destruye bordes y texturas críticas.
3. **Gestión Discreta del Iluminador Activo:** El uso continuo de emisores infrarrojos consumen energía crítica de batería y delatan la presencia del operador ante otros sensores de visión nocturna. El sistema debe calcular en tiempo real el umbral exacto para emitir **micro-pulsos de luz IR sincronizados** únicamente cuando la reconstrucción pasiva por software no alcance la relación señal-ruido ($SNR$) requerida.

### 1.3 Propiedad Intelectual (PI) y Estado del Arte
- **Estado del Arte:** Las soluciones comerciales avanzadas (ej. AN/PSQ-20 ENVG, Safran JIM Compact, Pulsar Trionyx) emplean fusión óptica/digital estática o tubos intensificadores híbridos de costo sumamente elevado ($> USD\ 8,000 - 18,000$).
- **Estrategia de PI para el Proyecto:** Se desarrollará un algoritmo propietario de **Fusión Multiespectral Adaptativa Guiada por SNR y Salient Edge Detection**, sumado a una arquitectura de **Control Closed-Loop del Emisor VCSEL Infrarrojo** impulsado por métricas de entropía de la imagen en el ISP. La propiedad intelectual se centrará en el software de fusión en tiempo real sobre procesadores heterogéneos (SoC + NPU).

### 1.4 Oportunidad de Negocio
El mercado de visión nocturna digital y termografía integrada muestra una tasa de crecimiento anual compuesta (CAGR) del 8.5%. Un dispositivo portátil compacto de fusión multiespectral digital con un costo de BOM $< USD\ 1,000$ y un precio de lista objetivo $< USD\ 2,500$ puede penetrar eficientemente en mercados de seguridad privada, grupos de rescate, vigilancia ambiental, navegación marítima recreativa/comercial y fuerzas de orden público.

---

## 2. Casos de Uso del Sistema

| Caso | Descripción | Requerimientos Derivados |
| :--- | :--- | :--- |
| **1** | **Búsqueda y Rescate en Ambientes Forestales:** Localización de personas extraviadas en bosques con luz estelar ($< 0.001\text{ lux}$). | Fusión LWIR (firma térmica humana) + NIR (textura de vegetación), latencia total $< 20\text{ ms}$, pantalla OLED de alta densidad. |
| **2** | **Navegación Nocturna Táctica a Pie:** Desplazamiento del operador en terreno irregular sin emitir firmas de luz visibles o IR continuas. | Reconstrucción pasiva por *Computational Low-Light Imaging*, desactivación automática del emisor IR adaptativo. |
| **3** | **Detección de Polizones u Objetos Ocultos a través de Humo/Niebla:** Inspección perimetral en entornos industriales u oceánicos. | Banda térmica LWIR penetrante ($8 - 14\ \mu\text{m}$), realce de bordes mediante algoritmo Laplaciano espacial. |
| **4** | **Reconocimiento Facial en Muy Baja Luz:** Identificación de sujetos a $15\text{ m}$ en condiciones urbanas nocturnas. | Sensor CMOS Ultra-Low Light $1080\text{p}/4\text{K}$, algoritmo de super-resolución por software y de-noising temporal. |
| **5** | **Vigilancia Discreta de Larga Duración:** Monitoreo estático en puntos de control alimentado por batería. | Algoritmos de bajo consumo en NPU, modo *sleep* inteligente, iluminación IR por micro-pulsos mínimos. |
| **6** | **Operación en Interiores con Transición Súbita de Luz:** El operador entra de una habitación oscura a una iluminada (*Flashbang* o encendido de luces). | Protección contra saturación por hardware en el ISP, tiempo de recuperación AGC $< 5\text{ ms}$. |
| **7** | **Lectura de Señalización o Documentos:** Inspección de mapas o señalética bajo la penumbra. | Enfoque macro en canal visible, ajuste dinámico del contraste del visor OLED sin cegar la adaptación ocular del usuario. |
| **8** | **Patrullaje Marítimo Nocturno:** Detección de embarcaciones sin luces de navegación y obstáculos flotantes. | Alto rango dinámico (HDR), filtrado de destellos por reflejo de agua, estanqueidad del chasis (IP67). |
| **9** | **Grabación y Telemetría de Misiones:** Transmisión de video fusionado en tiempo real hacia un centro de mando movil. | Codificación H.265 por hardware, transmisión M2MC vía Wi-Fi 6 / Ethernet / USB-C streaming. |
| **10**| **Seguimiento de Rastros Térmicos Recientes:** Detección de huellas de calor en motores de vehículos recién apagados o asientos. | Sensibilidad térmica NETD $< 35\text{ mK}$, paletas de color térmico falsas asignables (White-Hot, Black-Hot, Fusion-Color). |

---

## 3. Requerimientos del Producto

| ID | Descripción del Requerimiento | Tipo | Prioridad (MoSCoW) |
| :--- | :--- | :--- | :--- |
| **F-01** | El sistema debe capturar simultáneamente video en el espectro visible/NIR ($400 - 1000\text{ nm}$) y térmico LWIR ($8 - 14\ \mu\text{m}$). | Funcional | **Must** |
| **F-02** | El sistema debe realizar la fusión digital en tiempo real de las imágenes visible/NIR y térmica en un único flujo de video. | Funcional | **Must** |
| **F-03** | El sistema debe incluir un algoritmo de *Computational Low-Light Imaging* (fusión espacial-temporal y realce de contraste) activo en baja luz. | Funcional | **Must** |
| **F-04** | El iluminador IR activo debe operar de forma adaptativa, activándose automáticamente en micro-pulsos solo si el $SNR < 10\text{ dB}$. | Funcional | **Must** |
| **F-05** | El dispositivo debe mostrar la imagen en un micro-display OLED interno con ajuste de brillo y retícula HUD configurable. | Funcional | **Must** |
| **F-06** | El sistema debe permitir la selección manual de modos de visualización: Visible Puro, Térmico Puro, Fusión de Bordes y Fusión Completa. | Funcional | **Must** |
| **F-07** | El dispositivo debe transmitir streaming de video fusionado codificado vía Wi-Fi o USB-C a una estación externa. | Funcional | **Should** |
| **F-08** | El sistema debe incorporar un sensor de inercia (IMU 6-Ejes) para estabilización electrónica de imagen (EIS). | Funcional | **Should** |
| **F-09** | La unidad debe permitir la grabación interna de fotos y video en tarjeta MicroSD/eMMC. | Funcional | **Should** |
| **F-10** | El sistema debe disponer de un modo "Invisibilidad Táctica" que deshabilite todas las emisiones LED/IR y baje el brillo OLED al mínimo. | Funcional | **Could** |
| **NF-01**| La latencia total del pipeline de video (captura $\rightarrow$ procesado $\rightarrow$ display) debe ser $\le 25\text{ ms}$. | No Funcional | **Must** |
| **NF-02**| La tasa de refresco del video fusionado en pantalla debe ser de al menos $60\text{ fps}$ en modo Visible/NIR y $\ge 30\text{ fps}$ en modo Térmico. | No Funcional | **Must** |
| **NF-03**| La sensibilidad del sensor visible/NIR debe ser de al menos $0.0001\text{ lux}$ a $F/1.0$. | No Funcional | **Must** |
| **NF-04**| La sensibilidad térmica diferencial (NETD) del sensor LWIR debe ser $< 35\text{ mK}$ a $25^\circ\text{C}$. | No Funcional | **Must** |
| **NF-05**| La autonomía operativa continua del dispositivo debe ser $\ge 4\text{ horas}$ con una sola carga de batería Li-ion. | No Funcional | **Must** |
| **NF-06**| El costo objetivo de materiales (BOM) del prototipo no debe superar los $USD\ 1,000$. | No Funcional | **Must** |
| **NF-07**| La envolvente física debe cumplir con grado de protección estanco IP67 y resistencia a caídas de $1.5\text{ m}$. | No Funcional | **Should** |
| **NF-08**| El peso total del dispositivo portátil (incluyendo baterías y óptica) no debe superar los $650\text{ g}$. | No Funcional | **Should** |
| **NF-09**| El iluminador IR VCSEL debe operar en la longitud de onda de $940\text{ nm}$ (completamente invisible al ojo humano encandilado). | No Funcional | **Should** |

---

## 4. Requerimientos del Sistema (SRD)

| ID | Parámetro / Descripción | Especificación / Valor Target | Método de Verificación |
| :--- | :--- | :--- | :--- |
| **SR-01**| Alimentación Principal | Batería Li-ion 2S ($7.4\text{ V}$ nominal, $3300\text{ mAh}$) o USB-C PD ($9\text{V}/12\text{V}$) | Ensayo de descarga en banco de pruebas con carga constante. |
| **SR-02**| Sub-rieles de Potencia | $+5.0\text{V}$, $+3.3\text{V}$, $+1.8\text{V}$, $+1.2\text{V}$, $+0.8\text{V}$ (Core NPU) | Medición de rizados con osciloscopio en PCB ($< 20\text{ mV}_{pp}$). |
| **SR-03**| Sensor Visible / Ultra-Low Light | Sony STARVIS 2 CMOS $1/1.8"$, resolución $1920\times1080$, tamaño píxel $\ge 2.9\ \mu\text{m}$ | Verificación con hoja de datos e inspección de SNR en cámara de prueba. |
| **SR-04**| Sensor Térmico LWIR | Microbolómetro VOx no refrigerado, $640\times512$ o $384\times288$ píxeles, pitch $12\ \mu\text{m}$, $8-14\ \mu\text{m}$ | Verificación de salida digital DVP/SPI/USB y cálculo NETD. |
| **SR-05**| Micro-Display Ocular | Micro-OLED $0.39"$, resolución $1920\times1080$ Full HD, brillo max $1500\text{ cd/m}^2$, contraste $100000:1$ | Medición de luminancia y verificación de interfaz MIPI-DSI. |
| **SR-06**| Procesador Principal (SoC) | SoC Arm octa-core con NPU dedicada ($\ge 6\text{ TOPS}$, ej. Rockchip RK3588) | Prueba de stress sintético y tasa de frames por segundo. |
| **SR-07**| Memoria de Sistema | $4\text{ GB}$ LPDDR4x + $32\text{ GB}$ eMMC 5.1 Flash | Verificación de ancho de banda en lectura/escritura. |
| **SR-08**| Iluminador Activo Adaptativo | Diodo VCSEL $940\text{ nm}$ de $1\text{ W}$ pulsado con lente colimadora ajustable | Medición de potencia óptica en radiómetro IR y frecuencia PWM. |
| **SR-09**| Algoritmo de Fusión | Fusión por Pirámide Laplaciana / Latent Low-Light Deep Fusion en NPU | Evaluación de calidad estructural SSIM y tiempo de ejecución ($< 10\text{ ms}$). |
| **SR-10**| Interfaz M2MC Inalámbrica | Módulo Wi-Fi 6 ($802.11\text{ax}$) + Bluetooth 5.2 | Prueba de throughput de video H.265 UDP streaming ($> 15\text{ Mbps}$). |
| **SR-11**| Grado de Protección Térmico/Mecánico | Chasis de magnesio/aluminio con o-rings de silicona, $-20^\circ\text{C}$ a $+55^\circ\text{C}$ | Cámara ambiental y prueba de inmersión en agua ($1\text{ m}$ por $30\text{ min}$). |
| **SR-12**| Mecanismo de Control del Iluminador | Control closed-loop en MCU auxiliar basado en histograma de luz ambiental | Inyección de escena a $0.00001\text{ lux}$ y verificación de disparo IR mínimo. |

---

## 5. Arquitectura Técnica y Pilares del Diseño Electrónico

El diseño del dispositivo integra los ocho pilares fundamentales del diseño electrónico:

```
                  =====================================================================
                  |                     SISTEMA EXTERNO (HOST / MANDO)                 |
                  |   - Estación de Monitoreo Remoto / Casco Smart                    |
                  |   - Visualización Streaming H.265 / Telemetría GPS y Batería       |
                  =====================================================================
                                                   ^
                                                   | (Wi-Fi 6 / USB-C UVC Streaming)
                                                   v
====================================================================================================
| SENSOR DE VISIÓN NOCTURNA INTELIGENTE MULTIESPECTRAL (NV-SMART-01)                               |
|                                                                                                  |
|  +-----------------------+     +-------------------------------+     +------------------------+  |
|  | FUENTES DE POTENCIA   |     | ESTRUCTURA COMPUTACIONAL      |     | M2MC & HMI             |  |
|  | - Bat. Li-ion 2S 7.4V   |---->| - SoC RK3588 (8-Core + NPU)   |<--->| - Wi-Fi 6 / BT 5.2     |  |
|  | - PMIC / Reg. Bucks   |     | - MCU Cortex-M4 (Control/Power)|    | - Botones Tácticos     |  |
|  | - Carga USB-C PD 18W  |     | - Denoising & Fusión AI       |     | - Encoder de Brillo    |  |
|  +-----------------------+     +-------------------------------+     +------------------------+  |
|                                     |                     |                                      |
|                                     v (MIPI-CSI Dual)     v (MIPI-DSI)                           |
|                        +----------------------+ +-------------------------+                      |
|                        | ENTRADAS             | | SALIDAS                 |                      |
|                        | - Cam. Visible/NIR   | | - Micro-OLED 1080p     |                      |
|                        |   Ultra-Low Light    | |   (Visor Ocular)        |                      |
|                        | - Sensor LWIR Térmico| | - Driver VCSEL IR 940nm |                      |
|                        | - IMU 6-Ejes & Lux   | |   (Pulsado Adaptativo)  |                      |
|                        +----------------------+ +-------------------------+                      |
|                                   ^                          |                                   |
|                                   |                          v                                   |
|                         [ Radiación Térmica / NIR ]   [ Micro-pulsos IR 940nm ]                  |
|                                   \                          /                                   |
|                                    +------------------------+                                    |
|                                    | OPTOMECÁNICA & ENCLOSURE|                                   |
|                                    +------------------------+                                    |
====================================================================================================
```

### 5.1 Desglose de Dominios

1. **Fuentes de Alimentación:**
   - Pack de baterías de litio-ión $2S1P$ ($7.4\text{ V}$ nominal, $3300\text{ mAh}$).
   - PMIC (Power Management IC) multinivel que genera sub-rieles para Core NPU ($0.8\text{V}$ a $10\text{A}$ pico), memorias LPDDR4 ($1.1\text{V}$), sensor térmico ($3.3\text{V}$ ultrade bajo ruido), CMOS visible ($2.8\text{V}/1.2\text{V}$) y visor OLED ($5\text{V}/-5\text{V}$).
   - Circuito de carga rápida inteligente USB-C Power Delivery (PD 2.0/3.0) de $18\text{ W}$.

2. **Entradas (Acondicionamiento y Sensores):**
   - **Canal Visible/NIR:** Sensor CMOS Sony STARVIS 2 $1/1.8"$ acoplado a un lente de gran apertura $F/1.0$ corregido para el infrarrojo. Salida MIPI-CSI2 de 4 líneas.
   - **Canal Térmico LWIR:** Módulo microbolómetro VOx $640\times512$ ($12\ \mu\text{m}$ pitch) con lente de germanio $F/1.0$. Interfaz de video digital DVP/MIPI-CSI2.
   - **Sensórica Auxiliar:** IMU de 6 ejes (InvenSense) para estabilización electrónica y sensor de luminancia ambiental de amplio rango dinámico ($0.00001\text{ lux}$ a $10000\text{ lux}$).

3. **Salidas (Actuación y Potencia):**
   - **Visualización Ocular:** Micro-Display AMOLED/OLED de $0.39"$ Full HD ($1920\times1080$) accionado por MIPI-DSI con óptica de ocular ajustable (dioptrías $+2$ a $-5$).
   - **Iluminación Infrarroja Activa:** Módulo láser VCSEL de $940\text{ nm}$ ($1\text{ W}$) controlado por un driver de potencia MOSFET de conmutación ultrarrápida ($< 10\text{ ns}$) gestionado por PWM desde la MCU.

4. **Estructura Computacional:**
   - **Procesador Primario:** SoC Rockchip RK3588 (4 cores Cortex-A76 @ $2.4\text{ GHz}$ + 4 cores Cortex-A55 @ $1.8\text{ GHz}$) con GPU Mali-G610 y NPU de triple núcleo con potencia combinada de $6\text{ TOPS}$.
   - **Coprocesador de Ultra-Bajo Consumo:** Microcontrolador ARM Cortex-M4 (STM32G4) dedicado al monitoreo de batería, lectura de botones, control en bucle cerrado del iluminador IR y gestión del estado de energía del SoC.

5. **HMI / MMI (Interfaz Hombre-Máquina):**
   - Botonera táctil de 4 posiciones sellada con goma de silicona para uso con guantes (Power, Mode, Brightness+, Brightness-).
   - Encoder rotativo magnético para zoom digital ($1\times, 2\times, 4\times, 8\times$).
   - HUD visual interactivo sobrepuesto en la pantalla: brújula digital, indicador de nivel de batería, modo de visión actual y barra de emisión IR activa.

6. **M2MC (Comunicación Máquina a Máquina):**
   - Transceptor Wi-Fi 6 / Bluetooth 5.2 dual-band para transmisión de video en tiempo real hacia visores montados en casco, smartphones o consolas tácticas.
   - Puerto físico USB-C 3.1 Gen 1 con soporte DisplayPort Alternate Mode y protocolo UVC (USB Video Class) para conexión directa plug-and-play a PC o tablets.

7. **Ingeniería de Software / Información:**
   - **Sistema Operativo:** Linux Embebido optimizado (Buildroot/Yocto) con tiempo de arranque ultra-rápido ($< 3.5\text{ s}$).
   - **Pipeline de Proceso de Imagen (ISP & AI):**
     1. *Spatial-Temporal Denoising:* Algoritmo de filtrado bilineal compensado por movimiento sobre el flujo CMOS NIR.
     2. *Retinex Enhancement:* Realce de contraste dinámico adaptativo para zonas obscuras.
     3. *Deep Multispectral Fusion:* Red neuronal liviana (UNet reducida) que extrae los bordes térmicos del canal LWIR y los fusiona con las texturas del canal visible/NIR a $60\text{ fps}$.
     4. *Adaptive VCSEL Control Loop:* Histeresis digital que evalúa el $SNR$ global de la escena. Si $SNR < 12\text{ dB}$, dispara ráfagas de luz IR en el tiempo de exposición del sensor CMOS.

8. **Diseño Mecánico e Industrial:**
   - Envolvente de aleación de aluminio y magnesio maquinada en CNC con recubrimiento anódico duro de grado militar.
   - Sellado ambiental hermético mediante juntas de nitrilo (O-rings) e inyección de nitrógeno seco interno para evitar empañamiento de lentes a bajas temperaturas.
   - Acople mecánico estándar de liberación rápida para monturas de casco (Wilcox interface) y riel Picatinny.

---

## 6. Idea de Diseño, Trade-offs y Análisis Tecnológico

### 6.1 Matriz Comparativa Tecnológica de Visión Nocturna

| Criterio | Opción A: Tubo Intensificador Analógico (Gen 3) | Opción B: Cámara Térmica Pura (LWIR) | Opción C: Cámara Digital CMOS Low-Light | Opción Propuesta: NV-SMART-01 (Fusión Multi-Espectral + AI) |
| :--- | :--- | :--- | :--- | :--- |
| **Costo Prototipo** | High ($> \$3,500$) | Medium ($~ \$600$) | Low ($~ \$200$) | **Balanced ($~ \$950$)** |
| **Rango Dinámico y Resistencia a Deslumbramiento** | Muy Pobre (Cegado irreversible) | Excelente (Inmune a luz visible) | Bueno (WDR digital) | **Excelente (Protección por software + LWIR)** |
| **Reconocimiento de Texturas y Rostros** | Bueno en luz estelar | Nulo (Solo siluetas de calor) | Bueno hasta $0.001\text{ lux}$ | **Excelente en todo rango ($0\text{ lux}$ a día)** |
| **Capacidad de Fusión / HUD / Grabación** | Imposible (Requiere optics overlay) | Solo térmica | Digital | **Nativa digital completa** |
| **Dependencia de Iluminación Activa** | No requiere en luz estelar | Nunca requiere | Alta en $0\text{ lux}$ | **Mínima (Optimizada por micro-pulsos AI)** |

### 6.2 Estrategia de Balance Tripartito: Sensibilidad vs. Velocidad vs. Resolución

Para superar las restricciones físicas del ruido fotosensible sin penalizar la velocidad de reacción del operador, se aplica la siguiente estrategia en el pipeline de hardware:

```
                  +-------------------------------------------------------+
                  |               SCENE ILLUMINANCE (LUX)                 |
                  +-------------------------------------------------------+
                                              |
                +-----------------------------+-----------------------------+
                |                                                           |
       [ High / Medium Lux ]                                       [ Ultra-Low Lux ]
    (> 0.001 lux - Moonlight)                                  (< 0.0001 lux - Starlight/Cloudy)
                |                                                           |
   - Visible/NIR @ 60 fps Full HD                             - Activar Temporal Accumulation (Max 2 frames)
   - Temporal Denoising desactivado                           - Activar Deep Denoising NPU (6 TOPS)
   - VCSEL IR OFF                                             - Evaluar SNR de la imagen
                                                                            |
                                                            +---------------+---------------+
                                                            |                               |
                                                      [ SNR >= 12 dB ]              [ SNR < 12 dB ]
                                                            |                               |
                                                    - Mantener VCSEL OFF          - Disparar Micro-pulso
                                                    - Fusión LWIR Activa            VCSEL IR 940nm (Duty Cycle < 5%)
```

### 6.3 Optimización del Iluminador Infrarrojo Activo (LPI - Low Probability of Intercept)
A diferencia de los iluminadores convencionales de emisión continua que descargan la batería y funcionan como "faros" para enemigos o competidores con visión nocturna, el módulo **NV-SMART-01** implementa:
1. **Sincronización por Estroboscopía Vertical:** El VCSEL emite un pulso de luz solo durante el periodo de apertura del obturador electrónico (*Rolling/Global Shutter*) del sensor CMOS, reduciendo el consumo energético en un $80\%$.
2. **Control de Modulación de Potencia Adaptativa:** Ajuste dinámico de la corriente del diodo mediante PWM de $10\text{ kHz}$ controlado por el software en función de la distancia al objetivo estimada por contraste térmico.

---

## 7. Esquema NABC (Need, Approach, Benefits, Competition)

### **N - Need (Necesidad)**
Los equipos tácticos, personal de rescate y seguridad requieren un dispositivo de visión nocturna que permita ver en oscuridad absoluta, humo o niebla, con alta resolución, identificando tanto formas térmicas como detalles faciales y del entorno, sin depender de iluminadores IR delatadores y mantenido en un presupuesto accesible ($< USD\ 2,500$).

### **A - Approach (Enfoque / Solución)**
Desarrollar una unidad de visión nocturna digital portatil tri-banda que integra:
- Sensor CMOS Visible/NIR de alta sensibilidad ($0.0001\text{ lux}$) + Microbolómetro Térmico LWIR $640\times512$.
- Procesador de Inteligencia Artificial heterogéneo (SoC $6\text{ TOPS}$) que ejecuta *Computational Low-Light Imaging* y fusión multiespectral a $60\text{ fps}$.
- Un iluminador adaptativo VCSEL $940\text{ nm}$ con modulación micro-pulsada activada solo bajo demanda estricta de $SNR$.
- Micro-display OLED 1080p interno con baja latencia total ($< 25\text{ ms}$).

### **B - Benefits (Beneficios)**
- **Conciencia Situacional Superior:** Fusiona el contraste térmico (detección inmediata de objetivos vivos/calientes) con el detalle visual (reconocimiento de rostros, armas, obstáculos y letreros).
- **Invisibilidad y Eficiencia:** La emisión IR activa se reduce en más del $90\%$ respecto a sistemas tradicionales, extendiendo la vida útil de la batería a $> 4\text{ horas}$.
- **Costo Accesible:** Arquitectura basada en componentes semiconductores digitales masivos con un costo de lista 60% menor que las soluciones militares equivalentes.

### **C - Competition (Competencia y Diferenciadores)**

| Solución / Competidor | Ventajas Competidor | Desventajas Competidor | Diferenciador de Nuestra Solución |
| :--- | :--- | :--- | :--- |
| **AN/PSQ-20 ENVG (Military Grade)** | Ultra maduro, alta confiabilidad. | Costo astronómico ($> \$15,000$), peso elevado ($> 900\text{ g}$), exportación restringida (ITAR). | **Costo 85% inferior**, software abierto actualizable por IA, streaming Wi-Fi. |
| **Pulsar Trionyx TNA3 / Accolade** | Buena construcción industrial, disponibilidad comercial. | Latencia perceptible ($> 45\text{ ms}$), sensor visible limitado en baja luz pura, fusión ruidosa. | **Procesamiento AI a 60 fps**, latencia de $20\text{ ms}$, iluminador adaptativo pulsado inteligente. |
| **Sistemas Digitales Low-Light (SIONYX Aurora)** | Muy económico ($USD\ 600 - 1,000$), compacto. | Sin canal térmico LWIR (ceguera en humo/oscuridad total), dependiente de iluminador externo. | **Fusión térmica nativa LWIR**, rendimiento garantizado en $0.00001\text{ lux}$. |

---

## 8. Comentarios y Conclusiones

El documento de arquitectura para el sistema **NV-SMART-01** define un concepto innovador en la digitalización de la visión nocturna. Al combinar sensórica multi-espectral pasiva con procesamiento mediante redes neuronales embebidas y emisión infrarroja adaptativa de baja probabilidad de intercepción, se resuelven los dilemas tradicionales entre sensibilidad, latencia y resolución.

**Riesgos Técnicos e Identificados:**
1. **Disipación Térmica en Espacios Estancos:** El procesador NPU procesando algoritmos de fusión a $60\text{ fps}$ genera disipación de calor ($~ 4-6\text{ W}$) dentro de una carcasa sellada IP67. Se requiere un chasis de aluminio con disipación interna conectada por *thermal pads* de alta conductividad.
2. **Alineación Biaxial Óptica (Paralaje):** La distancia física entre el eje del lente térmico y el lente visible provoca desalineación en la fusión a corta distancia ($< 2\text{ m}$). Se implementará una corrección de paralaje por software mediante *homography warping* en la NPU.

**Próximos Pasos:**
- Validar la latencia de captura y fusión en la tarjeta de desarrollo del SoC RK3588.
- Calibrar la matriz de respuesta del sensor CMOS y la frecuencia del iluminador VCSEL pulsado en laboratorio de baja luz.

---

## 9. Control de Revisión y Aprobación

| Revisión | Responsable | Fecha | Estado / Observaciones |
| :--- | :--- | :--- | :--- |
| **0.1** | Equipo de Ingeniería Electrónica y Fotónica | 15 de Septiembre, 2026 | Borrador inicial de requerimientos y arquitectura multiespectral. |
| **0.2** | Líder de Desarrollo de Software Embebido | - | Pendiente de validación de pipeline AI en NPU. |
| **1.0** | Director de I+D | - | Aprobado para diseño de PCB y prototipado físico. |
