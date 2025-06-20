<b>Proyecto: 	Semáforo Inteligente con Dispositivo para Cruce Peatonal, Detección de Infracción y Semáforo Secundario para Control de Velocidad</b>

<b>Descripción General:</b><br>

El proyecto consiste en el diseño e implementación de un sistema de dos semáforos controlados por Arduino, que regulan el tránsito vehicular y peatonal.<br>
Cómo funciona:<br>
-	Detecta la presencia de vehículos mediante sensor de movimiento (IR).<br>
-	Habilita el paso de peatones mediante un pulsador.<br>
-	Prioriza el cruce peatonal de forma segura y eficiente, minimizando el tiempo de espera.<br>
-	Incluye control por máquina de estados (se refiere a la parte del código donde se usa el switch (estado) para manejar los distintos comportamientos del sistema, según el valor de la variable estado), control por tiempo (se ve representado por el uso de la función millis() para medir cuánto tiempo ha pasado desde un evento, y tomar decisiones en función de eso), y conteo de flancos (detección de pulsaciones).<br>

![Imagen del circuito Semáforo 1](https://github.com/Newi-code/Labo1/tree/imagenes/semaforo_1.jpg?raw=true)

<b>Funcionalidades Detalladas:</b><br>

<b>1. Control de Tráfico Vehicular</b><br>
-	LED verde, amarillo y rojo en cada semáforo de autos. Ciclo configurable en cada semáforo (por ejemplo: verde = 10 seg, amarillo = 3 seg, rojo = 10 seg).<br>
-	Sensor IR que detecta los vehículos que cruzan la bocacalle del semáforo principal (“maestro”) en infracción (es decir mientras el semáforo se encontraba en rojo) y envía una señal de alarma al semáforo secundario, que funciona como “esclavo” del primero y estaría ubicado a 100 metros de este. Al recibir la alarma se pone inmediatamente pone en rojo, obligando al vehículo a frenar.<br>

<b>2. Cruce Peatonal Inteligente</b><br>
-	Botón pulsador que simula el pedido de cruce peatonal.<br>

<b>3. Contador de Flancos</b><br>
-	Detección del flanco de subida del botón de cruce peatonal (pasar de LOW a HIGH).<br>

<b>4. Control por Tiempo</b><br>
-	Control de duración de cada luz del semáforo mediante temporizadores internos del Arduino (basados en millis()).<br>

<b>5. Máquina de Estados</b><br>
Controlar el comportamiento del semáforo principal, tiene los siguientes estados:<br>
-	<b>Estados posibles</b>:<br>
•	Estado 0: Verde - Semáforo en verde para el tráfico vehicular<br>
•	Estado 1: Amarillo a Rojo - Transición de verde a rojo<br>
•	Estado 2: Rojo - Semáforo en rojo para el tráfico vehicular (verde para peatones)<br>
•	Estado 3: Amarillo a Verde - Transición de rojo a verde<br>
-	<b>Transiciones entre Estados</b>:
Las transiciones entre estados se controlan mediante:<br>
•	Temporización: Cada estado tiene un tiempo máximo de duración<br>
•	Eventos externos:<br>
&nbsp;&nbsp;1.	Presión del botón peatonal<br>
&nbsp;&nbsp;2.	Detección de infracción por el sensor<br>
-	<b>Diagrama de Transición (Semáforo 1)</b>:<br>
Verde&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;(10 segundos o botón)&nbsp;→&nbsp;Amarillo a Rojo<br>
Amarillo a Rojo&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;(3 segundos)&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;→&nbsp;Rojo<br>
Rojo&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;(10 segundos)&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;→&nbsp;Amarillo a Verde<br>
Amarillo a Verde&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;(3 segundos)&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;→&nbsp;Verde<br>
-	<b>Implementación en Código</b>:<br>
La máquina de estados se implementa mediante una estructura switch-case en el loop principal:<br>

ESQUEMA

&nbsp;&nbsp;95&nbsp;&nbsp;&nbsp;&nbsp;switch (estado) {<br>
&nbsp;&nbsp;96&nbsp;&nbsp;&nbsp;&nbsp;case 0: // VERDE<br>
&nbsp;&nbsp;97&nbsp;&nbsp;&nbsp;&nbsp;// Lógica del estado verde<br>
&nbsp;&nbsp;98&nbsp;&nbsp;&nbsp;&nbsp;if (botón presionado O tiempo >= 10 seg) {<br>
&nbsp;&nbsp;99&nbsp;&nbsp;&nbsp;&nbsp;cambiar a AMARILLO a ROJO;<br>
100&nbsp;&nbsp;&nbsp;&nbsp;}<br>
101&nbsp;&nbsp;&nbsp;&nbsp;break;<br>
102<br> 
103&nbsp;&nbsp;&nbsp;&nbsp;case 1: // AMARILLO a ROJO<br>
104&nbsp;&nbsp;&nbsp;&nbsp;// Lógica del estado amarillo<br>
105&nbsp;&nbsp;&nbsp;&nbsp;if (tiempo >= 3 seg) {<br>
106&nbsp;&nbsp;&nbsp;&nbsp;cambiar a ROJO;<br>
107&nbsp;&nbsp;&nbsp;&nbsp;	}<br>
108&nbsp;&nbsp;&nbsp;&nbsp;break;<br>
109&nbsp;&nbsp;&nbsp;&nbsp;<br>
110&nbsp;&nbsp;&nbsp;&nbsp;case 2: // ROJO<br>
111&nbsp;&nbsp;&nbsp;&nbsp;// Lógica del estado rojo<br>
112&nbsp;&nbsp;&nbsp;&nbsp;if (sensor detecta infracción) {<br>
113&nbsp;&nbsp;&nbsp;&nbsp;activar alarma;<br>
114&nbsp;&nbsp;&nbsp;&nbsp;}<br>
115&nbsp;&nbsp;&nbsp;&nbsp;if (tiempo >= 10 seg) {<br>
116&nbsp;&nbsp;&nbsp;&nbsp;cambiar a AMARILLO A VERDE;<br>
117&nbsp;&nbsp;&nbsp;&nbsp;}<br>
118&nbsp;&nbsp;&nbsp;&nbsp;break;<br>
119<br>
120&nbsp;&nbsp;&nbsp;&nbsp;case 3: // AMARILLO A VERDE<br>
121&nbsp;&nbsp;&nbsp;&nbsp;// Lógica del estado amarillo<br>
122&nbsp;&nbsp;&nbsp;&nbsp;if (tiempo >= 3 seg) {<br>
123&nbsp;&nbsp;&nbsp;&nbsp;cambiar a VERDE;<br>
124&nbsp;&nbsp;&nbsp;&nbsp;}<br>
125&nbsp;&nbsp;&nbsp;&nbsp;break;<br>
126&nbsp;&nbsp;&nbsp;&nbsp;}<br>

-	<b>Variables de Control</b>:<br>
•	estado: Almacena el estado actual (0-3)<br>
•	tiempoEstado: Marca de tiempo cuando se entró al estado actual<br>
•	botonPresionado: Flag que indica si el botón peatonal fue presionado<br>
•	esperandoCambio: Flag que controla la transición anticipada por botón<br>

-	<b>Comportamiento Especial</b>:<br>
•	Prioridad Peatonal: Cuando se presiona el botón (botonPresionado = true), el semáforo inicia la transición a rojo después del tiempo mínimo en verde.<br>
•	Detección de Infracción: Durante el estado rojo, si el sensor detecta movimiento (estadoSensor == HIGH, infracción), se activa la alarma (ALARMA = HIGH) para notificar al semáforo secundario.<br>
•	Sincronización: El tiempo en cada estado se controla comparando el tiempo actual (millis()) con el tiempo de entrada al estado (tiempoEstado).<br>
Esta máquina de estados proporciona un control robusto y predecible del semáforo, respondiendo tanto a temporizaciones fijas (switch-case) como a eventos externos (botón peatonal, detección de infracciones).<br>

-	<b>Entradas y Salidas</b>:<br>
•	<b>Entradas</b>:<br>
o	Botón peatonal (digital).<br>
o	Sensor IR (digital).<br>
•	<b>Salidas</b>:<br>
o	LEDs de los semáforos vehiculares: verde, amarillo, rojo.<br>
o	LED indicador del Sensor IR: Marca si hay detección de movimiento.<br>


<b>Cumplimiento de Requisitos del Proyecto</b>:
|          Requisito				|			Cumplimiento                                          |
|-----------------------------------|-----------------------------------------------------------------|
|-	Control de Entradas y Salidas:	|Múltiples entradas (botón, sensor), múltiples salidas (LEDs)     |
|-	Contador de Flancos:			|Detección de pulsaciones del botón peatonal                      |
|-	Control Lógico por Tiempo:		|Manejo de semáforo con temporización dinámica                    |
|-	Control por Máquina de Estados:	|Transiciones claras entre estados según eventos y tiempo         |

<b>Semáforo secundario</b>:<br>
•	Se coloca un <b>sensor IR justo después del semáforo principal</b> (unos metros adelante) que detecta si un automóvil <b>cruzó mientras la luz del semáforo principal estaba en rojo</b>.<br>
•	Esto requiere registrar el <b>estado del semáforo</b> en una variable (estadoSemaforo 1) y leer si el sensor detecta paso de vehículo cuando este estado es "ROJO".<br>

<b>Activación del Semáforo Secundario</b>:<br>
•	Si se detecta una infracción, el Arduino activa inmediatamente el <b>semáforo secundario en ROJO</b>, obligando al auto infractor (y a todos) a detenerse más adelante.<br>
