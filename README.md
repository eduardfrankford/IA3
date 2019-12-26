# IA3

Disponemos de un robot con un brazo con 3 grados de libertad (es decir, 3 sectores) y un cabezal que se mueve en 2D tal y como muestra la figura.
![alt text](http://www.cs.us.es/~fsancho/images/2019-12/robot.jpeg)

Ajustando los ángulos θ1, θ2 y θ3 (por ejemplo, por medio de servo-motores) el robot puede colocar su cabezalen diversos puntos del plano. 
Entrena una red neuronal que sea capaz de aprender la cinemática inversa del robot, es decir, dadas las coordenadas del cabezal, (x,y), como entrada, la red debe devolver los correspondientes ángulos (θ1,θ2,θ3) que le permitan alcanzar ese punto.
A partir de esta red construye un procedimiento para que el robot pueda desplazar su cabeza de forma eficiente entre dos puntos cualesquiera del plano.

Si hubiera zonas del plano por las que el robot no puede cruzar (obstáculos verticales que le impiden que ninguno de los sectores del brazo pase por ellos), da un procedimiento para que el robot sea capaz de hacer la misma tarea moviendo su cabeza entre zonas permitidas

![alt text](http://www.cs.us.es/~fsancho/images/2019-12/robot2.gif)
