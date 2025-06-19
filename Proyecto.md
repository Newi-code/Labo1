**Proyecto: 	Semáforo Inteligente con Dispositivo para Cruce Peatonal, Detección de Infracción y Semáforo Secundario para Control de Velocidad**

**Descripción General:**
El proyecto consiste en el diseño e implementación de un sistema de dos semáforos controlados por Arduino, que regulan el tránsito vehicular y peatonal.
El sistema:
•	Detecta la presencia de vehículos mediante sensor de movimiento (IR).
•	Habilita el paso de peatones mediante un pulsador.
•	Prioriza el cruce peatonal de forma segura y eficiente, minimizando el tiempo de espera.
•	Incluye control por máquina de estados (se refiere a la parte del código donde se usa el switch (estado) para manejar los distintos comportamientos del sistema, según el valor de la variable estado), control por tiempo (se ve representado por el uso de la función millis() para medir cuánto tiempo ha pasado desde un evento, y tomar decisiones en función de eso), y conteo de flancos (detección de pulsaciones).
•	Muestra al peatón el tiempo restante de cruce en una pantalla LCD ubicada en el semáforo principal.

**Funcionalidades Detalladas:**
1. **Control de Tráfico Vehicular**
•	LED verde, amarillo y rojo en cada semáforo de autos. Ciclo configurable en cada semáforo (por ejemplo: verde = 10 seg, amarillo = 3 seg, rojo = 10 seg).
•	Sensor IR que detecta los vehículos que cruzan la bocacalle del semáforo principal (“maestro”) en infracción (es decir mientras el semáforo se encontraba en rojo) y envía una señal de alarma al semáforo secundario, que funciona como “esclavo” del primero y estaría ubicado a 100 metros de este. Al recibir la alarma se pone inmediatamente pone en rojo, obligando al vehículo a frenar.
2. **Cruce Peatonal Inteligente**
•	Botón pulsador que simula el pedido de cruce peatonal.
3. **Contador de Flancos**
•	Detección del flanco de subida del botón de cruce peatonal (pasar de LOW a HIGH).
4. **Control por Tiempo**
•	Control de duración de cada luz del semáforo mediante temporizadores internos del Arduino (basados en millis()).
5. **Máquina de Estados**
Controlar el comportamiento del semáforo principal, tiene los siguientes estados:
•	<u>Estados posibles</u>:
-	**Estado 0**: Verde - Semáforo en verde para el tráfico vehicular
-	**Estado 1**: Amarillo a Rojo - Transición de verde a rojo
-	**Estado 2**: Rojo - Semáforo en rojo para el tráfico vehicular (verde para peatones)
-	**Estado 3**: Amarillo a Verde - Transición de rojo a verde
•	<u>Transiciones entre Estados</u>:
Las transiciones entre estados se controlan mediante:
-	**Temporización**: Cada estado tiene un tiempo máximo de duración
-	**Eventos externos**:
1.	Presión del botón peatonal
2.	Detección de infracción por el sensor
•	<u>Diagrama de Transición (Semáforo 1)</u>:
Verde 	          (10 segundos o botón)	→	Amarillo a Rojo
Amarillo a Rojo  (3 segundos)	        →	Rojo
Rojo 	          (10 segundos) 	    →	Amarillo a Verde
Amarillo a Verde (3 segundos)	        →	Verde
•	<u>Implementación en Código</u>:
La máquina de estados se implementa mediante una estructura switch-case en el loop principal:
ESQUEMA

95	switch (estado) {
96	case 0: // VERDE
97	// Lógica del estado verde
98	if (botón presionado O tiempo >= 10 seg) {
99	cambiar a AMARILLO a ROJO;
100	}
101	break;
102	 
103	case 1: // AMARILLO a ROJO
104	// Lógica del estado amarillo
105	if (tiempo >= 3 seg) {
106	cambiar a ROJO;
107	}
108	break;
109	 
110	case 2: // ROJO
111	// Lógica del estado rojo
112	if (sensor detecta infracción) {
113	activar alarma;
114	}
115	if (tiempo >= 10 seg) {
116	cambiar a AMARILLO A VERDE;
117	}
118	break;
119	 
120	case 3: // AMARILLO A VERDE
121	// Lógica del estado amarillo
122	if (tiempo >= 3 seg) {
123	cambiar a VERDE;
124	resetear flags;
125	}
126	break;
127	}

•	<u>Variables de Control</u>:
-	**estado**: Almacena el estado actual (0-3)
-	**tiempoEstado**: Marca de tiempo cuando se entró al estado actual
-	**botonPresionado**: Flag que indica si el botón peatonal fue presionado
-	**esperandoCambio**: Flag que controla la transición anticipada por botón
•	<u>Comportamiento Especial</u>:
-	**Prioridad Peatonal**: Cuando se presiona el botón (botonPresionado = true), el semáforo inicia la transición a rojo después del tiempo mínimo en verde.
-	**Detección de Infracción**: Durante el estado rojo, si el sensor detecta movimiento (estadoSensor == HIGH, infracción), se activa la alarma (ALARMA = HIGH) para notificar al semáforo secundario.
-	**Sincronización**: El tiempo en cada estado se controla comparando el tiempo actual (millis()) con el tiempo de entrada al estado (tiempoEstado).
Esta máquina de estados proporciona un control robusto y predecible del semáforo, respondiendo tanto a temporizaciones fijas (switch-case) como a eventos externos (botón peatonal, detección de infracciones).
•	<u>Entradas y Salidas</u>:
-	**Entradas**:
o	**Botón peatonal** (digital).
o	**Sensor IR** (digital).
-	<u>Salidas</u>:
o	**LEDs de los semáforos vehiculares**: verde, amarillo, rojo.
o	**LED indicador del Sensor IR**: Marca si hay detección de movimiento.
o	??? Pantalla LCD: para mostrar tiempo restante para el cruce peatonal.

**Esquema de Diseño**
	Main Arduino	 
		 



**Cumplimiento de Requisitos del Proyecto**:
|          Requisito				|			Cumplimiento                                          |
|-----------------------------------|-----------------------------------------------------------------|
|-	Control de Entradas y Salidas:	|Múltiples entradas (botón, sensor), ??? múltiples salidas (LEDs, ???LCD)|
|-	Contador de Flancos:			|Detección de pulsaciones del botón peatonal                      |
|-	Control Lógico por Tiempo:		|Manejo de semáforo con temporización dinámica                    |
|-	Control por Máquina de Estados:	|Transiciones claras entre estados según eventos y tiempo         |

**Semáforo secundario**:
•	Se coloca un **sensor IR justo después del semáforo principal** (unos metros adelante) que detecta si un automóvil **cruzó mientras la luz del semáforo principal estaba en rojo**.
•	Esto requiere registrar el **estado del semáforo** en una variable (estadoSemaforo 1) y leer si el sensor detecta paso de vehículo cuando este estado es "ROJO".
**Activación del Semáforo Secundario**:
•	Si se detecta una infracción, el Arduino activa inmediatamente el **semáforo secundario en ROJO**, obligando al auto infractor (y a todos) a detenerse más adelante.
