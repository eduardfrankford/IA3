# IA3


##Problem

Disponemos de un robot con un brazo con 3 grados de libertad (es decir, 3 sectores) y un cabezal que se mueve en 2D tal y como muestra la figura.

![alt text](http://www.cs.us.es/~fsancho/images/2019-12/robot.jpeg)

Ajustando los ángulos θ1, θ2 y θ3 (por ejemplo, por medio de servo-motores) el robot puede colocar su cabezalen diversos puntos del plano. 

Entrena una red neuronal que sea capaz de aprender la cinemática inversa del robot, es decir, dadas las coordenadas del cabezal, (x,y), como entrada, la red debe devolver los correspondientes ángulos (θ1,θ2,θ3) que le permitan alcanzar ese punto.

A partir de esta red construye un procedimiento para que el robot pueda desplazar su cabeza de forma eficiente entre dos puntos cualesquiera del plano.

Si hubiera zonas del plano por las que el robot no puede cruzar (obstáculos verticales que le impiden que ninguno de los sectores del brazo pase por ellos), da un procedimiento para que el robot sea capaz de hacer la misma tarea moviendo su cabeza entre zonas permitidas

![alt text](http://www.cs.us.es/~fsancho/images/2019-12/robot2.gif)



# Documentation

## Library used ANN.nls

ANN.nls is a neural network library for netlogo.
In order to use it one only needs to give the ANN:train method the number-of-epochs, Batch, data and a learning rate. If one wants to see the test and training error it is possible to plot it with ANN:external-update. 

## Describtion and problems encountered

For this third practical exercise the problem was not to use the neural network library described above. The problem was to create training data which is useful for the neural network, meaning which has a pattern to be learned.

### Steps taken

First of all we started out generating training data randomly.
```netlogo
    set theta0 random 360
    set theta1 random 360
    set theta2 random 360
```
From this three degrees it made it possible to make a turtle follow them and then at the end take the xcor and ycor in order to get the correct input position to the output position.
Meaning that a pair of training data consisted of [[x y] [theta0 theta1 theta2]]

The problem with this solution is:

1. 360 degrees is the same as 0 degrees
Because of this property the neural network becomes confused when learning. For example if theta0 is 1° or 359° doesn't make a lot of difference regarding the end coordinates, but this confuses the Neural Network as there is a sudden jump in the function which it should learn.

#### Solutions tried:

1. Reduce possible degree range from 0 to 180 degrees
Problem: The turtle can only go North, East and Southwards.

2. Reduce possible degree range to 0 180 but with an extra output indicating whether the turtle shall move forward or backward

```netlogo
    set theta0 (random 180)
    let dir0 one-of list -1 1
    set theta1 (random 180)
    let dir1 one-of list -1 1
    set theta2 (random 180)
    let dir2 one-of list -1 1

;Normalization

      set theta0 theta0 / 180
      set theta1 theta1 / 180
      set theta2 theta2 / 180
      set dir0 (dir0 + 1) / 2
      set dir1 (dir1 + 1) / 2
      set dir2 (dir2 + 1) / 2
      set output (list theta0 dir0 theta1 dir1 theta2 dir2)
```

This already helped but didn't bring the expected results therefore an other option has been tried.

3. Represent a theta instead of degrees as a pair of (sin(theta), cos(theta)

This is also a possibility to avoid this negative property of [0,360] representation but still it did not help the neural network learning better.




The problem was not only the circular property of the degrees but also that one can mix the three thetas and still reach the same point. 
For example it does not matter if a turtle goes 45°, °100 and °30 or 30°, 45°, 100°. This was a big confusion factor for the neural network.

#### Solutions

1. The simplest was to just sort the theats before putting them into the training data. Like that the neuron 1 was responsible for small degrees, neuron 2 for the medium degrees and neuron 3 for the big degrees. The improvment of this simple line of code was very good.

```netlogo
    set output (sort (list theta0 theta1 theta2))
```

But the problem with this solution is when the area to move the robot in has obstacles, we need a different order of thetas generated by the network.
This technique can also be applied for areas with obstacles but one has to be careful because as it might give good solutions most of the time. Some times it is not possible to reach the given point with a sorted theta output. Sometimes it might be necessary to move with the medium degree first then small and then big or in an other order. 

We also thought about creating test data by changing the A star algoritm and making it return 3 thetas but this also would have had as a result that it might return strange orders for the neural network.

### Conclusion of problems

As you can see it is not easy to create valuable training data for a neural network. The two big problems encountered were: 

1. Circular property degrees
2. Same point with different degree order

Solutions

1. Change representation to 180 degrees or (sin,cos) pair.
2. Sort output in order to let neuron1 be small, neuron2 be medium and neuron3 be large.




# Insert Image of epoch error




