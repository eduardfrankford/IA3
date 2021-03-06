# Artificial Intelligence: 3º Project

[TOC]



## Problem

<p style="text-align:justify">We are provided with a robotic arm divided in 3 segments of the same length, and a head placed at the end of the last segment, that can be moved in two dimensions all around the coordinate plane. This system is showed in the picture below:

![alt text](http://www.cs.us.es/~fsancho/images/2019-12/robot.jpeg)

<p style="text-align:justify">Adjusting the three angles θ1, θ2 y θ3 (e.g. via servomotors) the robot can change the head's coordinates to any given point in the plane (inside the arm's range)

<p style="text-align:justify">We must train a ANN to be able to learn the arm's cinematic, i.e., given the head's coordinates, as input, the ANN must return the angles (θ1,θ2,θ3) that makes the head reach that point.

<p style="text-align:justify">From the ANN created, we also must create a procedure so that the robot can efficiently move its head between any two points in the plane. If there are areas in the plane where the arm cannot pass through (obstacles), the robot must find a  solution in which the head is moved between valid areas.

![alt text](http://www.cs.us.es/~fsancho/images/2019-12/robot2.gif)

# Documentation

## Library used ANN.nls

<p style="text-align:justify">ANN.nls is a neural network library for netlogo.
In order to use it one only needs to give the ANN:train method the number-of-epochs, Batch, data and a learning rate. If one wants to see the test and training error it is possible to plot it with ANN:external-update. 


## Files

<p style="text-align:justify">As the problem can be seperated into different parts we also wrote different programs for each part but in the end all the knowledge of each part has to be combined to the final full functioning robot.

### Specifications: 
#### No obstacles and only one position
<p style="text-align:justify">Soltuion: noObstaclesNeuralNetwork.nlogo
Speciality: only allowed 0 - 90 movements and ordered theta output

#### No obstacles all positions
<p style="text-align:justify">Solution: noObstaclesAllPositions.nlogo
Speciality: added two input neurons in order to represent current position

#### With obstacles all position
<p style="text-align:justify">Solution: withObstaclesAllPositions.nlogo
Speciality: added two input neurons in order to represent current position, degrees between 0 and 180, sorted output 

#### Rest of the files
<p style="text-align:justify">Have been some changes in representations, which will be explained in the following.

## Important file to grade -> withObstaclesAllPositions.nlogo

### Important functions

#### generate-data-random
<p style="text-align:justify">Is the main function which generates the training data for the neural network. This function might differ from file to file. So i will only explain one.
With obstacles all positions has been implemented to create as trainingset [[xcor ycor xOrigin yOrigin][theta0 theta1 theta2]]

<p style="text-align:justify">There will first be some thetas generated and then a turtle will be ordered to follow the theta heading 1 step at the time until the length of the robot arm has been reached. Like that it is possible to detect a movement over an obstacle which should not be included into the training data.

This process will be repeated a user-specified number of times.

#### visualize-random-samples
<p style="text-align:justify">This function is used to visualize random test samples after the network has been trained.

#### train
Library function called to train neural network

```netlogo
to train
  ANN:train number-of-epochs Batch data-test Learning-rate
end
```

#### setup-obstacles
Function which places 50 random obstacles all over the map

#### setup-random
<p style="text-align:justify">The setup function which calls setup-obstacles, generate-data-random and divides the generated data into test and training data and after that generates the neural network by calling ANN:create which will be given a string as user input.


## Describtion and problems encountered

<p style="text-align:justify">For this third practical exercise the problem was not to use the neural network library described. The problem was to create training data which is useful for the neural network, meaning which has a pattern to be learned.

### Steps taken

First of all we started out generating training data randomly.
```netlogo
    set theta0 random 360
    set theta1 random 360
    set theta2 random 360
```
<p style="text-align:justify">From this three degrees it made it possible to make a turtle follow them and then at the end take the xcor and ycor in order to get the correct input position to the output position.
Meaning that a pair of training data consisted of [[x y] [theta0 theta1 theta2]]

The problem with this solution is:

1. <p style="text-align:justify">360 degrees is the same as 0 degrees
Because of this property the neural network becomes confused when learning. For example if theta0 is 1° or 359° doesn't make a lot of difference regarding the end coordinates, but this confuses the Neural Network as there is a sudden jump in the function which it should learn.

#### Solutions tried:

1. <p style="text-align:justify">Reduce possible degree range from 0 to 180 degrees
Problem: The turtle can only go North, East and Southwards.

2. <p style="text-align:justify">Reduce possible degree range to 0 180 but with an extra output indicating whether the turtle shall move forward or backward

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

<p style="text-align:justify">This already helped but didn't bring the expected results therefore an other option has been tried.

3. Represent a theta instead of degrees as a pair of (sin(theta), cos(theta)

<p style="text-align:justify">This is also a possibility to avoid this negative property of [0,360] representation but still it did not help the neural network learning better.

```netlogo
    set theta0 random 360
    set theta1 random 360
    set theta2 random 360
    let sin0 sin theta0
    let cos0 cos theta0
    let sin1 sin theta1
    let cos1 cos theta1
    let sin2 sin theta2
    let cos2 cos theta2
    set output map [x -> x / normalizationFactor] (list theta0 theta1 theta2)
    let outputNORM map [x -> ( (x + 1) / 2)]((list sin0 cos0 sin1 cos1 sin2 cos2))
```



<p style="text-align:justify">The problem was not only the circular property of the degrees but also that one can mix the three thetas and still reach the same point. 
For example it does not matter if a turtle goes 45°, °100 and °30 or 30°, 45°, 100°. This was a big confusion factor for the neural network.

#### Solutions

1. <p style="text-align:justify">The simplest was to just sort the theats before putting them into the training data. Like that the neuron 1 was responsible for small degrees, neuron 2 for the medium degrees and neuron 3 for the big degrees. The improvment of this simple line of code was very good.

```netlogo
    set output (sort (list theta0 theta1 theta2))
```

![alt text](https://raw.githubusercontent.com/eduardfrankford/IA3/master/EpochError.PNG)
<p style="text-align:justify">But the problem with this solution is when the area to move the robot in has obstacles, we need a different order of thetas generated by the network.
This technique can also be applied for areas with obstacles but one has to be careful because as it might give good solutions most of the time. Some times it is not possible to reach the given point with a sorted theta output. Sometimes it might be necessary to move with the medium degree first then small and then big or in an other order. 

<p style="text-align:justify">We also thought about creating test data by changing the A star algoritm and making it return 3 thetas but this also would have had as a result that it might return strange orders for the neural network.

### Conclusion of problems

<p style="text-align:justify">As you can see it is not easy to create valuable training data for a neural network. The two big problems encountered were: 

1. Circular property degrees
2. Same point with different degree order

Solutions

1. Change representation to 180 degrees or (sin,cos) pair.
2. Sort output in order to let neuron1 be small, neuron2 be medium and neuron3 be large.


### Possible further steps

<p style="text-align:justify">In order to make the robot better it might be useful not to give him three thetas as a output but only one. Like this we could make the neural network generate one theta then make the step, generate one theta make the step and so on. As a result the robot would be able to move much more precisely.
Or maybe stop using neural networks for this problem and go on with self organizing maps, but this is a topic for an other practica.





