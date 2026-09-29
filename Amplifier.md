---

# DR-AMP-01 | Especificación de Sistema y Arquitectura Técnica
## Amplificador de Audio Estéreo Hi-Fi Clase A/AB de Alta Fidelidad con Entrada Cascode BJT, Salida MOSFET y Preamplificador con Ecualizador Activo
**Departamento de Ingeniería Electrónica y Telecomunicaciones**  
*Documento de Diseño y Requerimientos de Producto (Rev. 1.0 - Octubre 2026)*

---

## 1. Definición de la Aplicación

### 1.1 Necesidad
En el mercado de consumo y de audio profesional, existe una marcada polarización entre los amplificadores conmutados económicos (Clase D), que introducen ruido de conmutación de alta frecuencia y distorsión de cruce por modulación EMI/RFI, y los amplificadores Clase A puros para audiófilos, caracterizados por costos prohibitivos ($> \text{USD } 2,500$), dimensiones excesivas y una eficiencia térmica insostenible para espacios domésticos. Existe la necesidad de un sistema de reproducción de audio analógico de alta fidelidad, de topología discreta y desacoplada en DC, optimizado para escucha en salas de estar o estaciones de trabajo multimedia, que priorice la microdinámica tímbrica, una respuesta transitoria ultrarrápida y una distorsión armónica imperceptible a volúmenes de escucha realistas sin requerir una infraestructura de disipación industrial.

### 1.2 Problema
Los amplificadores analógicos comerciales de bajo costo basados en circuitos integrados monolíticos (ej. series TDA o LM) sufren de limitación de *Slew Rate* ($< 10\text{ V/}\mu\text{s}$), ancho de banda estrecho, falta de separación física entre etapas y distorsión de intermodulación transitoria (TIM). Por otro lado, los diseños discretos clásicos Clase AB suelen adolecer de distorsión de cruce por cero (*crossover distortion*), susceptibilidad a embalamiento térmico en transistores bipolares de salida, corrimiento de corriente continua (DC Offset) destructivo para los altavoces y recorte abrasivo (*hard clipping*) ante picos de ecualización.  
El desafío de este proyecto consiste en diseñar, simular e implementar un **sistema de amplificación de audio estéreo Hi-Fi completo (2.0)** que integre un preamplificador activo multilínea con ecualización por giradores, un limitador de pico óptico analógico (Vactrol) para prevención total de saturación, y una etapa de potencia lineal con topología Lin modificada simétrica, asistida por un servo-control DC activo y transistores de potencia MOSFET en configuración de alto sesgo (*High-Bias Class AB* con los primeros $9\text{ W}$ en Clase A pura).

### 1.3 Propiedad Intelectual (PI) y Estado del Arte
- **Patentes y Estado del Arte:** El diseño analógico de audio se sustenta en la arquitectura topológica clásica de tres etapas de Lin (1956) y las optimizaciones de compensación lineal documentadas ampliamente por Douglas Self y Bob Cordell. Existen patentes clave relativas a redes de compensación de polo dominante, transconductancia linealizada por realimentación y servos de DC (ej. patentes de Bryston Ltd., Pass Labs y Nelson Pass relativas a circuitos de bias en topologías cascode y transductores de corriente activa).
- **Estrategia de PI para el Proyecto:** Se implementa una topología de arquitectura abierta en silicio comercial sin reclamos de patente en microestructuras. La ventaja técnica se centra en el ajuste paramétrico de la compensación de dos polos con anulación de cero en el semiplano derecho (RHP Zero Nulling mediante resistor $R_z$), acoplado a un servocontrol de acoplamiento directo y una interfaz de compresión óptica suave sin elementos semiconductores no lineales en el trayecto directo de la señal.

### 1.4 Oportunidad de Negocio / Propósito Académico
El proyecto satisface los requerimientos formativos avanzados de electrónica analógica integrada (transistores MOSFET/BJT, fuentes de corriente activas, pares diferenciales acoplados, cascode simétrico, teoría de control por realimentación negativa y márgenes de estabilidad). A nivel de producto, un amplificador discreto Hi-Fi estéreo con preamplificador ecualizado y limitador analógico integrado por un costo de manufactura de componentes (BOM) menor a $\text{USD } 250$ democratiza el acceso a equipamiento metrológico y de audio para monitoreo de estudio en pequeños sellos discográficos, laboratorios acústicos y entornos audiófilos domésticos.

---

## 2. Casos de Uso del Sistema

| Caso | Descripción | Requerimientos Derivados |
| :--- | :--- | :--- |
| **1** | **Monitoreo Crítico en Estudio de Edición:** Escucha de mezclas y pistas de voces e instrumentos acústicos sin coloración tímbrica. | THD+N $< 0.005\%$, respuesta en frecuencia plana desde $0\text{ Hz}$ hasta $> 50\text{ kHz}$, fase plana en banda audible. |
| **2** | **Reproducción Multimedia Doméstica de Alta Fidelidad:** Conexión directa a fuentes de audio digital (DAC/Smartphone/PC) en volumen medio. | Operación en Clase A pura hasta $9\text{ W}$, cero distorsión de cruce por cero, relación señal-ruido ($\text{SNR}) > 100\text{ dB}$. |
| **3** | **Calibración Acústica de Sala con Ecualización:** Ajuste de curvas tímbricas mediante ecualizador gráfico multilínea para compensar modos de salón. | 5 bandas de ecualización analógica activa independientes ($\pm 12\text{ dB}$ por banda) sin desfases erráticos. |
| **4** | **Protección Activa Contra Saturación por Ecualización Agresiva:** Incremento extremo de sliders de graves sin que el amplificador recorte la señal. | Limitador óptico adaptativo por Vactrol calibrado para fijar la salida máxima del preamplificador a $1.0\text{ V}_{peak}$. |
| **5** | **Conexión de Altavoces de Carga Compleja (Cables Largos):** Operación segura sobre altavoces de estantería de baja impedancia y cables capacitivos. | Red Thiele de aislamiento inductivo ($1\mu\text{H} \parallel 10\Omega$) y Red Zobel integradas para estabilidad incondicional. |
| **6** | **Encendido y Apagado Silencioso y Seguro:** Supresión total del pulso transitorio de encendido (*turn-on thump*) y apagado. | Retardo electromecánico por relé temporizado (3 segundos) mediante circuito de supervisión dedicado $\mu\text{PC1237}$. |
| **7** | **Aislamiento Térmico y Prevención de Embalamiento:** Operación prolongada en verano o recintos cerrados bajo polarización de alto reposo. | Sensor térmico bimetálico a $80^\circ\text{C}$ con desconexión física de carga y acople térmico directo del multiplicador $V_{be}$. |
| **8** | **Laboratorio Universitario / Ensayos de Electrónica Analógica:** Banco de pruebas académico para caracterización de márgenes de fase y ganancia. | Puntos de prueba expuestos para osciloscopio: lazo de realimentación, salida de VAS, nodo del servo y rieles de alimentación. |
| **9** | **Inmunidad ante Falla Catastrófica de Silicio:** Protección del altavoz ante cortocircuito directo entre rieles de alimentación y nodo de salida. | Detector de offset DC integrado con tiempo de respuesta $< 15\text{ ms}$ para corte de relé ante tensiones $> \pm 0.7\text{ VDC}$. |
| **10**| **Rechazo a Ruido e Interferencia RF:** Ambiente doméstico con alta densidad de señales Wi-Fi, conmutación de fuentes SMPS y radiofrecuencia. | Filtro de entrada pasivo pasabajas ($723\text{ kHz}$) y filtros de desacoplo activo RC dedicados en los rieles de la etapa VAS. |

---

## 3. Requerimientos del Producto

| ID | Descripción del Requerimiento | Tipo | Prioridad (MoSCoW) |
| :--- | :--- | :--- | :--- |
| **F-01** | El amplificador debe operar en topología estéreo analógica Clase AB de alta corriente con capacidad de operar en Clase A pura hasta $9\text{ W}$. | Funcional | **Must** |
| **F-02** | La etapa de potencia debe integrar MOSFETs verticales de potencia (IRFP240/IRFP9240) excitados por drivers bipolares rápidos (TTC004B/TTA004B). | Funcional | **Must** |
| **F-03** | El circuito debe incluir un par diferencial de entrada acoplado en un encapsulado monolítico (DMMT5401) con espejo de corriente activo (DMMT5551). | Funcional | **Must** |
| **F-04** | La etapa de ganancia en tensión (VAS) debe implementar un Cascode totalmente simétrico tanto en el lazo activo como en la carga activa. | Funcional | **Must** |
| **F-05** | La respuesta en baja frecuencia debe ser de acoplamiento directo (DC verdadero, $0\text{ Hz}$) asistido por un servocontrol activo mediante amplificador operacional JFET. | Funcional | **Must** |
| **F-06** | El sistema debe integrar una red de compensación Miller modificada con resistencia de cancelación de cero RHP ($C_c = 47\text{ pF}$, $R_z = 330\ \Omega$) y adelanto de fase ($C_{lead} = 1\text{ pF}$). | Funcional | **Must** |
| **F-07** | El sistema debe incluir un preamplificador activo con ecualizador gráfico de 5 bandas basado en celdas de girador activo e inductancia simulada. | Funcional | **Must** |
| **F-08** | El preamplificador debe incorporar un circuito limitador óptico por fotocelda/LED (Vactrol) para fijar el nivel de entrada a la etapa de potencia en $\le 1.0\text{ V}_{peak}$. | Funcional | **Must** |
| **F-09** | La salida debe estar protegida electromecánicamente por relé frente a tensiones de continua ($> \pm 0.7\text{ V}$) y retardo de conexión mediante el CI $\mu\text{PC1237}$. | Funcional | **Must** |
| **F-10** | El sistema debe contar con protección térmica de corte mecánico a $80^\circ\text{C}$ montada en el disipador principal. | Funcional | **Should** |
| **F-11** | La etapa de potencia debe incorporar fusibles de acción rápida en ambos rieles DC y diodos Zener de $15\text{ V}$ para protección de compuerta (*Gate-Source*). | Funcional | **Should** |
| **NF-01**| La potencia nominal de salida continua debe ser $\ge 25\text{ W RMS}$ por canal sobre cargas de $8\ \Omega$ con THD $< 0.01\%$. | No Funcional | **Must** |
| **NF-02**| El ancho de banda en pequeña señal $(-3\text{ dB})$ debe extenderse desde $0\text{ Hz}$ hasta $\ge 100\text{ kHz}$. | No Funcional | **Must** |
| **NF-03**| La velocidad de respuesta (*Slew Rate*) del amplificador debe ser $\ge 35\text{ V/}\mu\text{s}$. | No Funcional | **Must** |
| **NF-04**| El margen de fase en bucle abierto debe ser $\ge 60^\circ$ para garantizar estabilidad incondicional frente a cualquier carga reactiva. | No Funcional | **Must** |
| **NF-05**| La distorsión armónica total más ruido (THD+N) a $1\text{ kHz}$ y $10\text{ W}$ debe ser $\le 0.005\%$. | No Funcional | **Must** |
| **NF-06**| La tensión de desvío de continua (DC Offset) en la salida debe ser $\le \pm 5\text{ mV}$ en régimen estacionario. | No Funcional | **Must** |
| **NF-07**| La impedancia de entrada del sistema (a través del preamplificador) debe ser $\ge 100\text{ k}\Omega$. | No Funcional | **Must** |
| **NF-08**| La disipación térmica en reposo por canal no debe superar los $40\text{ W}$ bajo polarización de alto sesgo ($750\text{ mA}$). | No Funcional | **Should** |
| **NF-09**| El costo objetivo de componentes electrónicos (BOM) para la versión estéreo no debe superar $\text{USD } 220$. | No Funcional | **Should** |

---

## 4. Requerimientos del Sistema (SRD)

| ID | Parámetro / Descripción | Especificación / Valor Target | Método de Verificación |
| :--- | :--- | :--- | :--- |
| **SR-01**| Alimentación Principal de Potencia | $\pm 28\text{ VDC} \pm 5\%$ partida (Rieles $+28\text{V}$, $0\text{V}$, $-28\text{V}$) | Multímetro digital y osciloscopio a plena potencia ($25\text{ W}$). |
| **SR-02**| Sub-rieles Regulados Preamp / Servo | $\pm 15\text{ VDC}$ regulados mediante LM7815 / LM7915 | Rizado $< 2\text{ mV}_{RMS}$ bajo consumo nominal de $60\text{ mA}$. |
| **SR-03**| Potencia Nominal por Canal | $25\text{ W RMS}$ continuo sobre $8\ \Omega$ ($20\text{ Hz} - 20\text{ kHz}$) | Carga fantasma de $8\ \Omega$ no inductiva, analizador de audio. |
| **SR-04**| Ganancia de Voltaje en Lazo Cerrado | $A_{CL} = 16\text{ V/V}$ ($24.08\text{ dB}$) en la etapa de potencia | Inyección de $1.0\text{ V}_{peak}$ senoidal a $1\text{ kHz}$; salida $= 16.0\text{ V}_{peak}$. |
| **SR-05**| Corriente de Polarización en Reposo | $100\text{ mA}$ (nominal de ajuste inicial) / $750\text{ mA}$ (modo audiófilo) | Caída de tensión sobre resistencias de source ($0.33\ \Omega$). |
| **SR-06**| Tasa de Rechazo al Rizado (PSRR) | $> 80\text{ dB}$ en bajas frecuencias ($100/120\text{ Hz}$) | Inyección de rizado artificial en fuentes DC mediante generador. |
| **SR-07**| Filtro de Entrada Anti-RF | Pasa-bajas pasivo $RC$ con $f_c \approx 723\text{ kHz}$ ($1\text{ k}\Omega + 220\text{ pF}$) | Barrido en frecuencia con analizador de redes/espectro. |
| **SR-08**| Compensación de Frecuencia Miller | $C_c = 47\text{ pF}$ (NP0/C0G), $R_z = 330\ \Omega$ en serie | Verificación de polo dominante y margen de fase $> 60^\circ$. |
| **SR-09**| Red de Salida Thiele y Zobel | $L = 1-2\mu\text{H} \parallel 10\ \Omega$ (Thiele) y $10\ \Omega + 100\text{ nF}$ (Zobel) | Prueba de carga con capacitor puro de $2\mu\text{F}$ en paralelo a $8\ \Omega$. |
| **SR-10**| Bandas de Ecualización Gráfica | $60\text{ Hz}$, $250\text{ Hz}$, $1\text{ kHz}$, $4\text{ kHz}$, $16\text{ kHz}$ ($\pm 12\text{ dB}$) | Barrido espectral por software midiendo respuesta en frecuencia. |
| **SR-11**| Umbral de Acción del Limitador Vactrol | Activación óptica a $V_{in} \ge 1.0\text{ V}_{peak}$ | Inyección de pulsos de $2\text{V}$ a $4\text{V}$; verificación de salida $\le 1.05\text{ V}_{peak}$. |
| **SR-12**| Tiempo de Desconexión por DC | $< 20\text{ ms}$ ante inyección de $\pm 1.5\text{ VDC}$ | Inyección súbita de offset DC artificial; monitoreo de relé. |
| **SR-13**| Dimensiones de Gabinete / Chasis | $320 \times 240 \times 90\text{ mm}$ (aluminio anodizado) | Inspección dimensional y ajuste mecánico de disipadores. |
| **SR-14**| Resistencia Térmica Disipador ($R_{\theta SA}$)| $\le 0.7^\circ\text{C/W}$ por canal para disipar $36\text{ W}$ en reposo | Ensayo térmico con termocuplas a temperatura ambiente de $25^\circ\text{C}$. |

---

## 5. Arquitectura Técnica y Pilares del Diseño Electrónico

El amplificador estéreo Hi-Fi se concibe como una integración modular de cinco macrobloques funcionales de acuerdo con los estándares de ingeniería analógica discreta:

```
====================================================================================================
|                                      CADENA DE SEÑAL ANALÓGICA                                   |
|                                                                                                  |
| [ ENTRADA AUDIO ]                                                                                |
|        |                                                                                         |
|        v                                                                                         |
| +----------------------------------------------------------------------------------------------+ |
| | PREAMPLIFICADOR Y PROCESAMIENTO DINÁMICO                                                    | |
| |                                                                                              | |
| |  +------------------+     +-------------------+     +------------------+     +-------------+ | |
| |  | BUFFER ENTRADA   | --> | ECUALIZADOR 5 B.  | --> | LIMITADOR ÓPTICO | --> | VOLUMEN Y   | | |
| |  | High-Z (100k)    |     | Giradores Activos |     | Vactrol (Max 1V) |     | BUFFER OUT  | | |
| |  | NE5532 / OPA2134 |     | NE5532 (x6)       |     | LDR + Detector   |     | NE5532      | | |
| |  +------------------+     +-------------------+     +------------------+     +-------------+ | |
| +----------------------------------------------------------------------------------------------+ |
|                                                                                |                 |
|                                                                                v                 |
| +----------------------------------------------------------------------------------------------+ |
| | ETAPA DE POTENCIA LINEAL (BLOQUE MONOMORFO DISCRETO x2 CANALES L/R)                          | |
| |                                                                                              | |
| |  +-------------------------------------+      +-------------------------------------------+  | |
| |  | ETAPA 1: PAR DIFERENCIAL (LTP)      |      | ETAPA 2: VAS CASCODE SIMÉTRICO            |  | |
| |  | - DMMT5401 (PNP Dual Emparejado)    | ---> | - DMMT5551 (NPN Amplificador)             |  | |
| |  | - Espejo Activo: DMMT5551 (NPN Dual)|      | - Carga Activa Cascode: DMMT5401          |  | |
| |  | - Cola de Corriente: 2.0 mA         |      | - Escudos Cascode: 2N5551 / 2N5401        |  | |
| |  +-------------------------------------+      | - Compensación Miller: 47pF + 330R        |  | |
| |                     ^                         +-------------------------------------------+  | |
| |                     |                                              |                         | |
| |          [ Inyección DC Corrección ]                               v                         | |
| |                     |                         +-------------------------------------------+  | |
| |         +----------------------+              | ETAPA 3: MULTIPLICADOR Vbe Y DRIVERS      |  | |
| |         | DC SERVO ACTIVO      | <---+        | - BD139 (Acoplado térmicamente al MOSFET) |  | |
| |         | - Integrador JFET    |     |        | - Drivers: TTC004B / TTA004B (100 MHz)    |  | |
| |         |   (TL071 / OPA134)   |     |        | - Resistor Clase A: 470 Ohms              |  | |
| |         +----------------------+     |        +-------------------------------------------+  | |
| |                                      |                             |                         | |
| |                                      |                             v                         | |
| |                                      |        +-------------------------------------------+  | |
| |                                      |        | ETAPA 4: SALIDA MOSFET CLASE A/AB         |  | |
| |                                      |        | - IRFP240 (N-Ch) / IRFP9240 (P-Ch)        |  | |
| |                                      |        | - Resistencias de Source: 0.33R / 5W      |  | |
| |                                      |        | - Zeners de Gate 15V + Stoppers 220R      |  | |
| |                                      |        +-------------------------------------------+  | |
| |                                      |                             |                         | |
| |                                      |                             v (Nodo de Salida)        | |
| |                                      +-------- [ Lazo Feedback ] --+                         | |
| |                                                  Rf=3.3k, Ri=220   |                         | |
| |                                                                    v                         | |
| |                                               +-------------------------------------------+  | |
| |                                               | ETAPA 5: FILTROS Y PROTECCIÓN             |  | |
| |                                               | - Red Zobel (10R + 100nF)                 |  | |
| |                                               | - Red Thiele (1uH // 10R)                 |  | |
| |                                               | - Supervisor uPC1237 + Relé Altavoz       |  | |
| |                                               +-------------------------------------------+  | |
| +----------------------------------------------------------------------------------------------+ |
|                                                                                |                 |
|                                                                                v                 |
|                                                                        [ ALTAVOZ 8 OHMS ]        |
====================================================================================================
```

### 5.1 Desglose de Dominios
1. **Fuentes de Alimentación:**
   - Transformador toroidal de $120\text{ VA}$ con primario $115/230\text{ VAC}$ y secundarios duales de $20\text{V}-0-20\text{V AC}$.
   - Puente rectificador monolítico de silicio de $25\text{ A} / 400\text{ V}$ montado al chasis.
   - Filtrado principal con banco capacitivo de $20,000\mu\text{F}$ por riel ($2 \times 10,000\mu\text{F} / 50\text{V}$ bajo ESR en paralelo con cerámicos de $100\text{ nF}$). Generación de $\pm 28\text{ VDC}$ no regulados.
   - Rieles regulados secundarios de bajo ruido $\pm 15\text{ VDC}$ mediante reguladores lineales LM7815 / LM7915 dedicados exclusivamente a la alimentación de los operacionales del preamplificador y DC Servo.
   - Redes de desacoplo $RC$ pasivas locales ($22\ \Omega + 100\mu\text{F} + 100\text{ nF}$) para aislar la etapa de entrada y VAS de las fluctuaciones de corriente de la etapa de potencia.
2. **Entradas (Acondicionamiento y Front-End):**
   - Entrada estéreo balanceada/no balanceada mediante conectores RCA dorados aislados de chasis.
   - Buffer seguidor de tensión con operacional de entrada bipolar/JFET (NE5532/OPA2134) que establece una impedancia de entrada de $100\text{ k}\Omega$.
   - Filtro pasivo pasabajas de entrada en la etapa de potencia ($1\text{ k}\Omega + 220\text{ pF}$) con frecuencia de corte a $723\text{ kHz}$ para supresión de RFI/EMI.
3. **Preamplificador y Ecualización:**
   - Módulo ecualizador activo de 5 bandas basado en giradores de topología estandarizada con amplificadores operacionales duales NE5532.
   - Ajuste manual continuo mediante potenciómetros deslizantes lineales de $50\text{ k}\Omega$.
   - Compresor/Limitador óptico suave basado en célula fotoeléctrica acoplada (Vactrol artesanal o comercial LED-LDR) comandado por un detector de envolvente con comparador de precisión ajustado a $1.0\text{ V}_{peak}$.
   - Control de volumen maestro estéreo con potenciómetro rotativo logarítmico (Audio Taper) de $50\text{ k}\Omega$.
   - Buffer de salida de baja impedancia ($< 50\ \Omega$) capaz de excitar la impedancia de entrada reducida de $3.3\text{ k}\Omega$ de la etapa de potencia.
4. **Etapa de Ganancia y Potencia (Amplificador Core):**
   - Par diferencial de entrada (LTP) basado en par PNP simétrico en encapsulado único **DMMT5401**, polarizado a $2.0\text{ mA}$ mediante fuente de corriente en espejo.
   - Carga activa del LTP mediante par emparejado NPN **DMMT5551** en configuración de espejo de corriente Wilson o básico.
   - Etapa de ganancia en tensión (VAS) cascode simétrica excitada por DMMT5551 acoplada a transistores discretos 2N5551/2N5401 para absorción de tensión y disipación de calor ($182\text{ mW}$). Polarización activa mediante cadenas de diodos 1N4148 desacopladas.
   - Red de compensación Miller formada por $C_c = 47\text{ pF}$, $R_z = 330\ \Omega$ y capacitor de compensación por adelanto $C_{lead} = 1\text{ pF}$ en paralelo con la resistencia de realimentación de $3.3\text{ k}\Omega$.
   - Multiplicador $V_{be}$ basado en transistor de media potencia **BD139** (TO-126) acoplado mecánicamente cara a cara con el encapsulado TO-247 del transistor de potencia MOSFET mediante grasa siliconada de alta conductividad térmica.
   - Etapa de excitación (Drivers) compuesta por el par complementario de alta velocidad **TTC004B / TTA004B** ($160\text{V}$, $1.5\text{A}$, $f_T = 100\text{ MHz}$, $C_{ob} = 10\text{ pF}$) polarizados en Clase A local mediante resistencia de emisor a emisor de $470\ \Omega$.
   - Etapa de salida Push-Pull seguidora de fuente (*Source Follower*) basada en HEXFETs verticales de potencia complementarios **IRFP240** (N-Ch) e **IRFP9240** (P-Ch).
   - Resistencias de degradación y sensado de source de $0.33\ \Omega / 5\text{W}$ no inductivas cerámicas.
   - Diodos Zener de $15\text{ V} / 0.5\text{ W}$ y resistencias de compuerta (*Gate Stoppers*) de $220\ \Omega$ directamente soldadas a los terminales de los MOSFETs.
5. **Servocontrol y Acondicionamiento de DC:**
   - Integrador analógico de muy baja frecuencia basado en amplificador operacional de entrada JFET de precisión (**TL071** o **OPA134**).
   - Constante de tiempo fijada por resistencia de sensado de $1\text{ M}\Omega$ y capacitor de película de polipropileno de $1\mu\text{F}$ ($f_c \approx 0.16\text{ Hz}$).
   - Inyección de corriente de compensación al nodo inversor del par diferencial a través de una resistencia de $100\text{ k}\Omega$, eliminando totalmente los capacitores electrolíticos de desacople de DC en el lazo de realimentación y en la entrada.
6. **Protección Electromecánica y Supervisión:**
   - Circuito integrado dedicado **$\mu\text{PC1237}$** con sensado directo de tensión de salida de potencia.
   - Relé electromecánico de audio de contactos de plata-óxido de estaño ($12\text{V}$ o $24\text{V}$ bobina, $10\text{A}$ contactos) que desacopla físicamente los altavoces ante fallas.
   - Detección de pérdida de alterna (*AC loss*) para desconexión instantánea antes del colapso de los rieles DC.
   - Interruptor térmico bimetálico normalmente cerrado ($80^\circ\text{C}$) en serie con la bobina del relé de protección.
   - Red de estabilización reactiva de salida conformada por célula Zobel ($10\ \Omega / 2\text{W} + 100\text{ nF} / 100\text{V}$) y celda Thiele ($10\ \Omega / 5\text{W} \parallel 1.5\mu\text{H}$).
7. **Diseño Mecánico, Chasis y Térmica:**
   - Gabinete metálico de aluminio con apantallamiento electromagnético integral.
   - Disipadores de calor de aluminio extruido con aletas longitudinales con resistencia térmica $\le 0.7^\circ\text{C/W}$ por canal para disipación pasiva de $36\text{ W}$ en reposo.
   - Topología estricta de conexión de tierras en estrella (*Star Grounding*): separación física absoluta entre Tierra Sucia de Potencia (retorno de altavoz y banco capacitivo), Tierra de Chasis y Tierra Limpia de Señal (preamplificador y par diferencial), unidas en un único punto equipotencial central mediante un resistor de desacoplo de $10\ \Omega$ en paralelo con un capacitor de $100\text{ nF}$ y puente de diodos antiparalelo.

---

## 6. Selección y Análisis Comparativo de Topologías y Componentes

### 6.1 Topología de la Etapa de Salida de Potencia

| Criterio | Opción A: Clase A Pura Push-Pull | Opción B: Clase AB High-Bias (Seleccionada) | Opción C: Clase D Auto-oscilante / PWM |
| :--- | :--- | :--- | :--- |
| **Linealidad / Distorsión** | Máxima. Cero distorsión de conmutación o cruce. | Idéntica a Clase A en los primeros vatios ($< 9\text{ W}$); transición imperceptible. | Elevada distorsión en altas frecuencias por tiempo muerto (*Dead Time*). |
| **Eficiencia Térmica** | Muy baja ($15\% - 25\%$). Disipación masiva permanente. | Moderada ($60\% - 70\%$ a plena carga). Consumo controlado en reposo. | Muy alta ($> 90\%$). Mínima necesidad de disipador. |
| **Complejidad de Circuito** | Baja a nivel de señal, pero fuentes y disipadores gigantescos. | Media-Alta. Requiere multiplicador $V_{be}$, seguimiento térmico y servo. | Muy alta en diseño analógico (modulador PWM, filtrado LC de salida, EMI). |
| **Idoneidad Académica** | Demuestra polarización estática pero carece de desafío dinámico. | **Óptima:** Integra cascode, espejos, servo, protecciones y dinámica lineal. | Se enfoca en teoría de conmutación e inductores más que en linealidad analógica. |

### 6.2 Selección de Dispositivos de Salida de Potencia

| Parámetro | BJT de Potencia (ej. 2SC5200 / 2SA1943) | MOSFET Lateral (ej. Exicon ECW20N20 / 20P20) | MOSFET Vertical HEXFET (ej. IRFP240 / IRFP9240) |
| :--- | :--- | :--- | :--- |
| **Control** | Corriente de base (demanda corriente sustancial al VAS/Driver). | Tensión pura en compuerta (aislada por óxido). | Tensión en compuerta (requiere corriente transitoria de carga de $C_{iss}$). |
| **Estabilidad Térmica** | Coeficiente de temperatura negativo permanente (Riesgo alto de embalamiento). | Coeficiente de temperatura positivo intrínseco (inmune a embalamiento). | Coeficiente negativo a bajas corrientes ($< 3\text{A}$); positivo a corrientes altas. |
| **Transconductancia ($g_m$)** | Muy alta ($> 1\text{ S}$). Control férreo de graves. | Baja ($0.8 - 1.2\text{ S}$). Requiere mayor excursión de tensión en compuerta. | **Alta ($5.0\text{ S}$):** Excelente pegada en graves y muy bajo costo comercial. |
| **Costo y Disponibilidad** | Moderado. Vulnerable a falsificaciones masivas en el mercado. | Muy costosos ($> \text{USD } 15 - 20$ c/u), difíciles de conseguir. | **Económicos y universales:** ($\text{USD } 3 - 5$ c/u), encapsulado TO-247 robusto. |
| **Decisión de Diseño** | Descartado por fragilidad térmica y carga de corriente sobre drivers. | Descartado por costo unitario inviable para el presupuesto objetivo. | **Seleccionado:** IRFP240/9240 asistido por $0.33\Omega$ de source y acople de BD139. |

### 6.3 Comparativa de Transistores de Entrada (Diferencial LTP)

| Criterio | BJT Monolítico Dual (DMMT5401) - *Seleccionado* | JFET Dual Emparejado (LSJ689 / 2SJ74) |
| :--- | :--- | :--- |
| **Transconductancia ($g_m$)** | **$38.4\text{ mA/V}$ a $1\text{ mA}$:** Mantiene la ganancia de lazo abierto y alto *Slew Rate*. | $\approx 2.2\text{ mA/V}$ a $1\text{ mA}$ (17 veces inferior). Reduce drásticamente la ganancia de lazo. |
| **Ancho de Banda de Lazo** | Mantiene el ancho de banda unitario optimizado en $\approx 40-50\text{ MHz}$. | Reduce el producto ganancia-ancho de banda a $< 8\text{ MHz}$, alterando el margen de fase. |
| **Impedancia de Entrada** | Media ($\approx 100\text{ k}\Omega$ intrínseca). Demanda ajuste simétrico de resistencias DC. | Infinita en DC ($> 10^{12}\ \Omega$). Desprecia cualquier error de corriente de polarización. |
| **Distorsión Transitoria (TIM)** | Excelente si el *Slew Rate* se mantiene elevado mediante la corriente de cola ($2\text{mA}$). | Excelente por linealidad cuadrática natural sin necesidad de tanta realimentación. |
| **Veredicto Técnico** | **Seleccionado:** Garantiza la máxima supresión de intermodulación y respuesta escalón sin sobrepaso. | Descartado por penalización severa del producto ganancia-ancho de banda y disponibilidad crítica. |

---

## 7. Esquema NABC (Need, Approach, Benefits, Competition)

### **N - Need (Necesidad)**
Los melómanos, estudiantes de ingeniería de sonido y pequeños estudios de producción requieren un sistema de amplificación y procesamiento tímbrico que ofrezca la fidelidad espectral y la calidez transitoria de los amplificadores analógicos de alta gama, pero con la confiabilidad moderna (protección activa total, inmunidad a DC mediante servo, y limitación de saturación) sin invertir miles de dólares en marcas esotéricas.

### **A - Approach (Enfoque / Solución)**
Desarrollo de un **Sistema Hi-Fi Analógico Integral** que acopla:
1. Preamplificador estéreo con búfer de alta impedancia, ecualizador gráfico por giradores de 5 bandas y limitador suave analógico por célula óptica (Vactrol) calibrado a $1\text{ V}_{peak}$.
2. Etapa de potencia monomorfa duplicada en Clase AB con polarización de reposo configurable hasta Clase A pura en escucha estándar ($9\text{ W}$).
3. Topología circuital rigurosa: Par diferencial con transistores bipolares emparejados en encapsulado único, VAS cascode totalmente simétrico, drivers de $100\text{ MHz}$ para excitación de compuertas capacitivas, transistores MOSFET TO-247 de $100\text{ W}$, servo DC de corrección continua e inductancias Thiele de salida.
4. Módulo de supervisión con microcircuito dedicado $\mu\text{PC1237}$, desconexión por relé y fusibles rápidos.

### **B - Benefits (Beneficios)**
- **Fidelidad Metrológica:** Distorsión armónica ultra baja ($< 0.005\%$), respuesta en frecuencia plana desde corriente continua ($0\text{ Hz}$) hasta más allá del espectro ultrasónico ($100\text{ kHz}$) sin desfases dentro del rango auditivo.
- **Inmunidad a Errores de Operación:** El usuario puede elevar al máximo las bandas de ecualización o la ganancia de la fuente sin que el amplificador sature jamás la etapa de potencia gracias al limitador óptico.
- **Seguridad Absoluta del Altavoz:** Protección activa contra fallas de DC, retardo de conexión para silenciar chasquidos y aislamiento ante cables de alta capacitancia.
- **Arquitectura Pedagógica y Reproducible:** Emplea componentes comerciales de costo accesible y amplia distribución internacional.

### **C - Competition (Competencia y Diferenciadores)**

| Solución / Competidor | Ventajas Competidor | Desventajas Competidor | Diferenciador de Nuestra Solución |
| :--- | :--- | :--- | :--- |
| **Módulos Clase D Comerciales (ej. TPA3116, ICEpower)** | Extremadamente pequeños, alta eficiencia ($> 90\%$), bajo costo. | Ruido residual de conmutación de alta frecuencia, sonido frío, clipping duro e inductores de salida ruidosos. | **Topología lineal 100% discreta**, libre de EMI, respuesta en frecuencia hasta DC y sonido orgánico Clase A. |
| **Amplificadores Integrados Monolíticos (ej. Gainclone LM3886)** | Muy pocos componentes externos, implementación rápida. | Ganancia de lazo fija, velocidad de respuesta mediocre ($10\text{ V/}\mu\text{s}$), nula versatilidad de polarización. | **Mayor velocidad ($42\text{ V/}\mu\text{s}$)**, transistores discretos de potencia sobredimensionados ($200\text{ W}$) y diseño a medida. |
| **Amplificadores Audiófilos Comerciales (ej. Cambridge, Marantz)** | Acabado industrial estético, marca consolidada. | Precios elevados ($> \text{USD } 600 - 1,500$), esquemáticos cerrados, ecualizadores pasivos básicos de 2 bandas. | **Ecualizador gráfico de 5 bandas por giradores**, limitador Vactrol anti-clip y servo DC por una fracción del costo. |

---

## 8. Comentarios, Riesgos y Conclusiones

El presente documento formaliza la arquitectura técnica definitiva del sistema de amplificación **DR-AMP-01**. La transición a una etapa de potencia lineal con transistores bipolares emparejados monolíticos, drivers de conmutación ultra-rápida y salida por MOSFETs verticales complementa un diseño analógico de élite con parámetros de control dinámico sobresalientes.

**Limitaciones y Riesgos Técnicos Identificados:**
1. **Gestión Térmica en Modo Alto Sesgo ($750\text{ mA}$):** La disipación estática de $36\text{ W}$ por canal requiere disipadores térmicos de gran masa y aleteado vertical con libre convección. Para la validación inicial del prototipo se operará a $100\text{ mA}$ ($4.8\text{ W}$ de disipación total en reposo).
2. **Estabilidad de Lazo con el DC Servo:** Si la frecuencia de corte del integrador del servo se sitúa demasiado alta ($> 1\text{ Hz}$), el servo intentará corregir las frecuencias graves de la música, introduciendo distorsión armónica de segundo orden. Se ha fijado estrictamente la constante de tiempo en $0.16\text{ Hz}$ mediante resistencia de $1\text{ M}\Omega$ y capacitor de polipropileno de $1\mu\text{F}$.

---

## 9. Control de Revisión y Aprobación

| Revisión | Responsable | Fecha | Estado / Observaciones |
| :--- | :--- | :--- | :--- |
| **0.1** | Equipo de Diseño Electrónico | 12 de Octubre, 2026 | Definición conceptual inicial de etapas discretas. |
| **0.2** | División de Simulación y Control | 18 de Octubre, 2026 | Compensación Miller RHP optimizada y verificación con modelo Python. |
| **1.0** | Aprobación Final de Arquitectura | 24 de Octubre, 2026 | Integración de Preamp, Limitador Vactrol, Servo DC y Protecciones para manufactura. |

---

## 10. Lista de Materiales (BOM) — Formato CSV

A continuación se detalla la lista completa y desglosada de componentes requeridos para la fabricación de un sistema completo estéreo (2 canales de potencia, preamplificador estéreo con ecualizador de 5 bandas, limitador, protecciones y fuente de alimentación).

```csv
Item,Designator,Description,Package,Quantity,Unit_Cost_USD,Total_Cost_USD,Category
1,Q1_L Q1_R,Matched Dual PNP Small Signal BJT 150V 0.2A (DMMT5401),SOT-26,2,0.45,0.90,Semiconductors
2,Q2_L Q2_R,Matched Dual NPN Small Signal BJT 160V 0.2A (DMMT5551),SOT-26,2,0.45,0.90,Semiconductors
3,Q3_L Q3_R,NPN Small Signal Transistor 160V 0.6A (2N5551),TO-92,2,0.20,0.40,Semiconductors
4,Q4_L Q4_R,PNP Small Signal Transistor 150V 0.6A (2N5401),TO-92,2,0.20,0.40,Semiconductors
5,Q5_L Q5_R,NPN Medium Power BJT Vbe Multiplier 80V 1.5A (BD139),TO-126,2,0.65,1.30,Semiconductors
6,Q6_L Q6_R,NPN High Speed Driver BJT 160V 1.5A 100MHz (TTC004B),TO-126,2,0.85,1.70,Semiconductors
7,Q7_L Q7_R,PNP High Speed Driver BJT 160V 1.5A 100MHz (TTA004B),TO-126,2,0.85,1.70,Semiconductors
8,Q8_L Q8_R,N-Channel Power HEXFET MOSFET 200V 18A 150W (IRFP240),TO-247AC,2,3.80,7.60,Semiconductors
9,Q9_L Q9_R,P-Channel Power HEXFET MOSFET 200V 12A 150W (IRFP9240),TO-247AC,2,4.20,8.40,Semiconductors
10,U1 U2 U3 U4 U5 U6 U7,Low-Noise Dual Audio Operational Amplifier (NE5532P / OPA2134),DIP-8,7,1.20,8.40,Semiconductors
11,U8_L U8_R,Precision JFET-Input Operational Amplifier for DC Servo (TL071CP / OPA134),DIP-8,2,0.90,1.80,Semiconductors
12,U9,Speaker Protection Integrated Circuit (uPC1237 / NTE7132),SIP-8,1,2.50,2.50,Semiconductors
13,U10,Positive Linear Voltage Regulator +15V 1A (LM7815CT),TO-220,1,0.55,0.55,Semiconductors
14,U11,Negative Linear Voltage Regulator -15V 1A (LM7915CT),TO-220,1,0.55,0.55,Semiconductors
15,BR1,Bridge Rectifier Glass Passivated 400V 25A (KBPC2504),Chassis Mount,1,3.50,3.50,Semiconductors
16,D1_L-D4_L D1_R-D4_R,High-Speed Switching Diode 75V 0.15A (1N4148),DO-35,16,0.05,0.80,Semiconductors
17,DZ1_L-DZ2_L DZ1_R-DZ2_R,Zener Diode 15V 0.5W Gate Protection (1N5245B),DO-35,4,0.15,0.60,Semiconductors
18,VACT1,Analog Optical Limiter Cell (Vactrol NSL-32SR3 or 5mm Red LED + GL5528 LDR),Opto Module,2,2.20,4.40,Optoelectronics
19,R_S1_L R_S1_R R_S2_L R_S2_R,Ceramic Wirewound Power Resistor 0.33 Ohm 5W 5%,Radial Cement,4,0.75,3.00,Resistors
20,R_F1_L R_F1_R,Precision Metal Film Resistor 3.3k Ohm 1/4W 1%,Axial,2,0.08,0.16,Resistors
21,R_I1_L R_I1_R,Precision Metal Film Resistor 220 Ohm 1/4W 1%,Axial,2,0.08,0.16,Resistors
22,R_Z1_L R_Z1_R,Precision Metal Film Resistor 330 Ohm 1/4W 1%,Axial,2,0.08,0.16,Resistors
23,R_GATE_L R_GATE_R,Carbon Film Resistor Gate Stopper 220 Ohm 1/4W 5%,Axial,4,0.05,0.20,Resistors
24,R_ZOB_L R_ZOB_R,Power Metal Oxide Resistor Zobel 10 Ohm 2W 5%,Axial,2,0.30,0.60,Resistors
25,R_THI_L R_THI_R,Power Ceramic Resistor Thiele 10 Ohm 5W 5%,Axial,2,0.50,1.00,Resistors
26,R_SERVO_L R_SERVO_R,Metal Film Resistor Servo Input 1M Ohm 1/4W 1%,Axial,2,0.08,0.16,Resistors
27,R_INJ_L R_INJ_R,Metal Film Resistor Servo Inject 100k Ohm 1/4W 1%,Axial,2,0.08,0.16,Resistors
28,R_EQ_GEN,Metal Film Assorted Resistors for 5-Band EQ Gyrator Cells 1/4W 1%,Axial,40,0.05,2.00,Resistors
29,RV_BIAS_L RV_BIAS_R,Cermet Trimpot Multi-turn 10k Ohm (Vbe Multiplier Adjustment),Bourns 3296Y,2,0.85,1.70,Potentiometers
30,RV_EQ1-RV_EQ5_STEREO,Dual Slide Potentiometer Linear 50k Ohm 60mm Travel (EQ Bands),Slide Panel,5,2.10,10.50,Potentiometers
31,RV_VOL,Dual Rotary Potentiometer Logarithmic Audio 50k Ohm (Master Volume),Alps RK27,1,4.50,4.50,Potentiometers
32,C_C1_L C_C1_R,Ceramic Capacitor High-Voltage C0G/NP0 47pF 100V 5%,Radial Disc,2,0.25,0.50,Capacitors
33,C_LEAD_L C_LEAD_R,Ceramic Capacitor C0G/NP0 1pF 100V 5%,Radial Disc,2,0.25,0.50,Capacitors
34,C_HF1_L C_HF1_R,Ceramic Capacitor C0G/NP0 220pF 100V 5%,Radial Disc,2,0.20,0.40,Capacitors
35,C_ZOB_L C_ZOB_R,Metalized Polyester Film Capacitor Zobel 100nF (0.1uF) 100V 5%,Box Radial,2,0.40,0.80,Capacitors
36,C_SERVO_L C_SERVO_R,Polypropylene Film Capacitor Servo Integrator 1uF 63V 5%,Box Radial,2,0.85,1.70,Capacitors
37,C_FILT_BULK,Aluminum Electrolytic Capacitor Low-ESR 10000uF 50V 20%,Snap-In 35mm,4,5.50,22.00,Capacitors
38,C_DEC_RAIL,Aluminum Electrolytic Capacitor 100uF 50V 20%,Radial,4,0.25,1.00,Capacitors
39,C_DEC_CER,Ceramic Multilayer Capacitor X7R 100nF 50V 10%,Radial 0.1",12,0.10,1.20,Capacitors
40,C_EQ_GEN,Metalized Film Capacitors Assorted for 5-Band EQ Gyrators 5%,Box Radial,20,0.35,7.00,Capacitors
41,L_THIELE_L L_THIELE_R,Air Core Hand-Wound Inductor 1.5uH (15 turns 18 AWG over R_THI),Custom Axial,2,0.30,0.60,Magnetics
42,XFMR1,Toroidal Power Transformer 120VA Prim 115/230V Sec Dual 20V-0-20V,Chassis Mount,1,38.00,38.00,Magnetics
43,RLY1,Audio Output Relay Dual Form C (DPDT) Contacts AgSnO 12VDC Coil 10A (Omron G2R-2),PCB Mount,1,3.20,3.20,Electromechanical
44,TSW1,Bimetallic Thermal Switch Normally Closed 80C Auto-Reset (Sensata 17AM),Chassis Mount,1,1.80,1.80,Sensors
45,F1 F2,Glass Fuse Fast-Acting DC Rails 5x20mm 4A 250V,Clip Mount,4,0.30,1.20,Protection
46,F_MAINS,Glass Fuse Slow-Blow AC Mains 5x20mm 1.6A 250V with Fuseholder,Panel Mount,1,1.50,1.50,Protection
47,HS1 HS2,Extruded Aluminum Heatsink 0.7 C/W Anodized Black (Fischer SK 47/100),Chassis Mount,2,14.50,29.00,Mechanical
48,CONN_AC,IEC Power Inlet with Integrated Rocker Switch and Fuse Drawer,Snap-In,1,3.20,3.20,Connectors
49,CONN_RCA,Gold-Plated Dual Female RCA Phono Chassis Jacks Teflon Insulated,Panel Mount,2,1.80,3.60,Connectors
50,CONN_SPK,Heavy-Duty Gold-Plated 5-Way Binding Posts (Pair Red/Black),Panel Mount,2,2.50,5.00,Connectors
51,PCB_SET,Set of Double-Sided FR4 PCBs (Power Amp Core x2, Preamp/EQ x1, PSU/Protection x1),Custom Fab,1,25.00,25.00,PCB
```
