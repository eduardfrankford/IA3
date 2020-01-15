# IA3

Disponemos de un robot con un brazo con 3 grados de libertad (es decir, 3 sectores) y un cabezal que se mueve en 2D tal y como muestra la figura.
![alt text](http://www.cs.us.es/~fsancho/images/2019-12/robot.jpeg)

Ajustando los ángulos θ1, θ2 y θ3 (por ejemplo, por medio de servo-motores) el robot puede colocar su cabezalen diversos puntos del plano. 
Entrena una red neuronal que sea capaz de aprender la cinemática inversa del robot, es decir, dadas las coordenadas del cabezal, (x,y), como entrada, la red debe devolver los correspondientes ángulos (θ1,θ2,θ3) que le permitan alcanzar ese punto.
A partir de esta red construye un procedimiento para que el robot pueda desplazar su cabeza de forma eficiente entre dos puntos cualesquiera del plano.

Si hubiera zonas del plano por las que el robot no puede cruzar (obstáculos verticales que le impiden que ninguno de los sectores del brazo pase por ellos), da un procedimiento para que el robot sea capaz de hacer la misma tarea moviendo su cabeza entre zonas permitidas

![alt text](http://www.cs.us.es/~fsancho/images/2019-12/robot2.gif)











# Brazo robótico manejado por ANN

<p style="text-align:justify">Nuestro problema se basa en brazo robótico con 3 articulaciones (3 sectores) y un cabezal que se mueve por todo el plano ajustando los ángulos θ1, θ2 y θ3.</p>
![alt text](http://www.cs.us.es/~fsancho/images/2019-12/robot.jpeg)

<p style="text-align:justify">Nuestra tarea asignada es la de entrenar una red neuronal que, con unas coordenadas como datos de entrada, nos devuelva los ángulos θ1,θ2,θ3 que debemos aplicar para alcanzarlo.</p>

<p style="text-align:justify">A partir de esta red debemos crear un procedimiento para que el robot pueda desplazar su cabeza entre dos puntos cualesquiera del plano. Además, el brazo debe ser capaz de realizar este movimiento teniendo en cuenta la existencia de unos obstáculos repartidos por el plano.</p>

![alt text](http://www.cs.us.es/~fsancho/images/2019-12/robot2.gif)

[TOC]

## Creación de la ANN

<p style="text-align:justify">Para la creación de las muestras para entrenar y testear la red neuronal, hemos creado una función llamada *generate-data* que nos da tres ángulos aleatorios (que serán la salida). Con estos ángulos variaremos la orientación del agente que irá dibujando el brazo, que, al terminar, guardará esa coordenada como la entrada. Iremos guardando las entradas y salidas en una lista (que luego dividiremos en una lista de entrenamiento y otra de testeo) para usarla en la ANN.</p>

<p style="text-align:justify">Al ser las salidas ángulos, obtenemos un problema para el entrenamiento: al ser una medida cíclica (al llegar 360º, el máximo, vuelve a 0º, el mínimo), dos resultados que sean muy parecidos, por ejemplo 355º y 5º, para la ANN son muy lejanas, lo que provoca que el entrenamiento sea erróneo. Por ello, hemos probado con una versión del problema en la que los ángulos están limitados dentro del intervalo 0º-90º, para evitar el problema de la variable cíclica. A diferencia de la versión completa, en esta obtenemos un error menor (en torno a 10 veces mejor), que aunque sigue existiendo, son resultados al menos cercanos a la solución deseada.</p>

## Evitar obstáculos

<p style="text-align:justify">Con la red neuronal ya creada, hemos modificado el problema añadiendo obstáculos, por los que el brazo no podrá pasar. El funcionamiento este caso es parecido. Una diferencia fundamental es a la hora de crear los brazos robóticos: ahora, al ir generándose, si se encuentra con un obstáculo dejaremos de crear el brazo.</p>

<p style="text-align:justify">Además del método original, hemos creado otro basado en A*. Este modifica la creación de los ejemplos para que sean creados mediante A *,  obteniendo así una creación de ejemplos más compleja, pero a su vez obtenemos ejemplos más efectivos para entrenar la ANN.</p>

