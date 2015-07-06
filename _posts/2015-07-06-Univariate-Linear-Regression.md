---
layout: post
title: Univariate Linear Regression - Data Structures and Computation (DRAFT)
---

### Motivation

Suppose you made some observations regarding the area of a house and its
sale price:

    Area = 1 , Price =  3
    Area = 2 , Price =  7
    Area = 3 , Price = 10 
    Area = 4 , Price = 14

Looking at that data, you suspect a some kind of relationship between
area and price. Knowing this relationship is useful because it allows
you to make predictions about the price of a house, given its area.

#### Why Linear Regression?

To predict the value of one variable given the value of another
variable.

### Hypothesis

To predict price given area, requires that we first make a hypothesis
that price has a linear relationship to area. Using x to represent area,
we develop the following hypothesis:

    h(x) = p0 + p1 * x

The hypothesis contains two parameters, p0 and p1. Our goal is to find
the parameter values that best fits the data. To develop our intuition
of "fitting the data", let's randomly assign 1 to both p0 and p2,
resulting in the following hypothesis:

    h(x) = 1 + 1 * x
         = 1 + x

Given those parameter values, the predicted values h(x), vs actual values
y, and the difference between them are as follows:

    x = 1,  h(x) = 2,  y =  3,  diff = -1
    x = 2,  h(x) = 3,  y =  7,  diff = -4
    x = 3,  h(x) = 4,  y = 10,  diff = -6
    x = 4,  h(x) = 5,  y = 14,  diff = -9

With a different pair of parameter values, we have a different set of
predicted values and difference. The parameters that best fit the data
are those that result in the smallest difference.

The cost function, also known as the loss function, captures this notion
of "difference" that we want to minimize.

    Loss Function ~ Sum of Difference
                  ~ Sum of Squared Difference   # square to handle negative values
                  ~ SUM [ Square(Computed - Actual) ]
                  ~ SUM [ Square(h(x[i] - y[i]) ]
                  ~ SUM [ Square(h(x[i] - y[i]) ]
                  = (1/2m) * SUM [ Square(h(x[i] - y[i]) ]  # average over m examples

    Or better written as:

         J(p0,p1) = (1/2m) * SUM [ Square(h(x[i] - y[i]) ]

    Where 
        J    : the loss function
        x[i] : x value of the i-th observation
        y[i] : y value of the i-th observation
        m    : number of training examples

Our goal is to minimize J(p0,p1) with respect to p0 and p1.

To minimize the loss function, we use the gradient descent algorithm as
follows:

    Gradient Descent Algorithm
    --------------------------

    Repeat until convergence {
        p0 = p0 - a * 1/m * SUM {   h(x[i]) - y[i]          }
        p1 = p1 - a * 1/m * SUM { ( h(x[i]) - y[i] ) * x[i] }
    }

    where:
        m : the number of training examples
        a : the learning rate

    Update p0 and p1 simultaneously

### Data structures and computations

This section attempts to break down the linear regression algorithm
above into small bite-sized data structures and computation that can be
easily implemented.

#### Data Structure #1: A single training example

A training example is a PAIR of an input value and its associated output value: 

        [ Xi, Yi ]

    where:
        Xi : the input value for the i-th training example
        Yi : the output value for the i-th training example

#### Data Structure #2: A set of training examples, ( X, Y )

This is typically a PAIR of ARRAYs, i.e. the input ARRAY X and the
output array Y:

    [ X, Y ] = [ [X1, X2, .., Xm]], [Y1, Y2, .., Ym] ]

    where:
        X : the ARRAY of input values
        Y : the ARRAY of output values
        m : the number of training examples

#### Data Structure #3: Hypothesis parameters P

This is a LIST of parameter values:

    P = [p0, p1]

#### Computation #1: initializeParameters

Initialize Parameters is a function that randomly generates an initial
list of parameter values:

    initializeParameters(l) -> P

    Where:
        l : length of parameter LIST
        P : a LIST of parameter values

This computation takes a length value l, and returns a LIST of length l,
containing random numbers.

#### Computation #2: computeHypothesis

computeHypothesis is a function that takes the parameters P, and the
set of training inputs X and computes the predicted values H:

        computeHypothesis( P, X ) -> H

    Where:
        P : a LIST of parameter values
        X : an ARRAY of input values
        H : an ARRAY of predicted values

This computation takes a LIST of scalars P, and an ARRAY of values X and
returns an ARRAY of predicted values H. Below is a worked example:

    Suppose:

        P = [1, 2]
        X = [1, 2, 3, 4]

    And assume the hypothesis:

        h(x[i]) = p0 + p1 * x[i]

    The result of the computation H is an ARRAY of values:

        H = [ p0+p1*x[1], p0+p1*x[2], p0+p1*x[3], p0+p1*x[4] ]
          = [ 1 + 2*1,    1 + 2*2,    1 + 2*3,    1 + 2*3    ]
          = [ 3, 5, 7, 9 ]

#### Computation #3: averagePredictionError

This computes the following portion of the gradient descent algorithm:

        1/m * SUM { h(x[i]) - y[i] }

The computation has the following signature:

        averagePredictionError( H, Y ) -> e

    Where:
        H : an ARRAY of predicted output values
        Y : an ARRAY of actual output values
        e : a SCALAR representing the average prediction error

This computation takes an ARRAY of output values H, and an ARRAY of
actual values Y and returns a SCALAR value e, e.g.:

    Suppose:

        H = [ 3, 5,  7,  9 ]
        Y = [ 3, 7, 10, 14 ]
        m =  4                  # number of training examples

    Compute and return:

        e = SUM (H - Y) / m
          = SUM [0, -2, -3, -5] / 4
          = -10 / 4
          = -2.5

#### Computation #4: averageInputAdjustedPredictionError

    This computes the following portion of the gradient descent
    algorithm:

        1/m * SUM { ( h(x[i]) - y[i] ) * x[i] }

    And the coputation has the following signature:

        averageInputAdjustedPredictionError( X, Y, H ) -> e

    Where:
        H : an ARRAY of predicted output values
        Y : an ARRAY of actual output values
        X : an ARRAY of input values
        e : a SCALAR representing the input adjusted prediction error

    Note that the averagePredictionError is a special case where the
    input values X, always has the value one (1).

    The intuition is that the adjustment is made up of two components:
    
        1. the prediction error :  h(x[i] - y[i]
        2. the input value      :  x[i]

    The adjustment is bigger if either the prediction error or the input
    value are big. Some adjustments may be positive values while others
    may be negative values. The adjustments are summed up and averaged
    over the number of examples. Over time, as the model converges,
    ideally, this sum should be very, very small.

    This computation takes an ARRAY of SCALARs H, and an ARRAY of
    SCALARs Y, an ARRAY of SCALARs X and returns a SCALAR e, e.g.:

                  H = [ 3, 5,  7,  9 ]
                  Y = [ 3, 7, 10, 14 ]
                  X = [ 1, 2,  3,  4 ]
              H - Y = [ 0,-2, -3, -5 ]
        (H - Y) * X = [ 0,-4, -9,-20 ]
                  e = (0 - 4 - 9 - 20) / 4
                    = -33 / 4
                    = -8.25

#### Computation #5: ParameterAdjustment

This computes adjustments to the parameter P, which is the following
portion of the gradient descent algorithm:

        p0 = p0 - a * 1/m * SUM {   h(x[i]) - y[i]          }
        p1 = p1 - a * 1/m * SUM { ( h(x[i]) - y[i] ) * x[i] }

    where:
        a : the SCALAR learning rate
        m : the number of training examples

The computation has the following signature:

        parameterAdjustment( X, Y, H ) -> D

    where:
        X : an ARRAY of input values
        Y : an ARRAY of actual output values
        H : an ARRAY of predicted output values
        D : a LIST of adjustments to the parameter values

The gradient descent algorithm can be rewritten as follows:

        P0 = P0 - a * 1/m * SUM { ( Hi - y[i] ) * 1    }
        P1 = P1 - a * 1/m * SUM { ( Hi - y[i] ) * x[i] }

And in a vector form:

        P = P - a * 1/m * SUM [ ( H - Y ) * X ]
        P = P - D

    where:
        P : a LIST of parameter values
        a : the learning rate
        m : the number of training examples
        H : an ARRAY of predicted output values
        Y : an ARRAY of actual output values
        X : an ARRAY of input VECTORS
        D : a LIST of adjustments to the parameter values

    Note that X is now an ARRAY of VECTORS. Each training input X is
    now a vector [1, Xi]. 
    

If we assume that X is an ARRAY of VECTORS where each vector has 1 as
the first element, then gradient descent algorithm is rewritten as:

        P_new = P_old - D

    Where:
        P_new : the new LIST of parameter values
        P_old : the new LIST of parameter values
        D     : the adjustments

    This computation takes an ARRAY of SCALARs H, and an ARRAY of
    SCALARs Y, an ARRAY of VECTORs X, and returns a a LIST of SCALARS e,
    where the length of the LIST is the same as the length of a single
    VECTOR x[i] e.g.:

                  H = [ 3, 5,  7,  9 ]
                  Y = [ 3, 7, 10, 14 ]
                  X = [ [1,1], 
                        [1,2],  
                        [1,3],
                        [1,4] ]

              H - Y = [ 0,-2, -3, -5 ]
        (H - Y) * X =   0,  *   [1,1]
                       -2,  *   [1,2]
                       -3,  *   [1,3]
                       -5   *   [1,4]
                    = [ 0, 0]
                      [-2,-4]
                      [-3,-9]
                      [-5,-20]
        
                  D = ( [0-2-3-5, 0-4-9-20] ) / 4
                    = ( [-10,-33] ) / 4
                    = [-2.5, -8.25]

        Assume a = 0.1:

            P_new = P_old - D
            P_new = [1, 2] - a * [-2.5, -8.25] 
                  = [1, 2] - 0.1 * [-2.5, -8.25] 
                  = [1, 2] - [-0.25, -0.825] 
                  = [1, 2] + [0.25, 0.825] 
                  = [1.25, 2.825]

#### Computation #6: Converge

    This runs the gradient descent algorithm until some criteria is
    reached. Consider the following:

        Repeat until convergence {
            P_new = P_old - D
        }
    
    Converge when some value is below a THRESHOLD e.g:
    
        1. difference between P_old and P_new is below THRESHOLD
        2. sum square of D is below THRESHOLD
        3. iterate more than 10,000 times
        4. any of the above

#### Implementation

This section should show implement the computations above.

### Credits

The terms and algorithms were from a [Machine Learning course by Andrew
Ng](https://www.coursera.org/learn/machine-learning/home/info).

### Resources

Free sources to learn more about linear algebra and linear regression include: 

1. [Linear Algebra as an Introduction to Abstract
Mathematics](https://www.math.ucdavis.edu/courses/syllabus_detail?cm_id=96)
which includes detailed course notes in
[pdf](www.math.ucdavis.edu/~anne/linear_algebra/mat67_course_notes.pdf).
2. [MIT Linear Algebra Open
Courseware](http://ocw.mit.edu/courses/mathematics/18-06-linear-algebra-spring-2010/index.htm)
3. [Stanford Statistical Learning Online
Course](https://lagunita.stanford.edu/courses/HumanitiesandScience/StatLearning/Winter2015/about) comes with a [book](http://www-bcf.usc.edu/~gareth/ISL/).

### Todo

1. Create implementations of the above algorithms, ideally generic and fast
enough to be useful.
2. Create a post for multivariate linear regression.

