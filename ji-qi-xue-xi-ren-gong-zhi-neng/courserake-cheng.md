1. 机器学习定义11
2. ## What is Machine Learning?

   Two definitions of Machine Learning are offered. Arthur Samuel described it as: "the field of study that gives computers the ability to learn without being explicitly programmed." This is an older, informal definition.

   Tom Mitchell provides a more modern definition: "A computer program is said to learn from experience E with respect to some class of tasks T and performance measure P, if its performance at tasks in T, as measured by P, improves with experience E."

   Example: playing checkers.

   E = the experience of playing many games of checkers

   T = the task of playing checkers.

   P = the probability that the program will win the next game.

   In general, any machine learning problem can be assigned to one of two broad classifications:

   Supervised learning and Unsupervised learning.

3. 监督学习

   ## Supervised Learning

   In supervised learning, we are given a data set and already know what our correct output should look like, having the idea that there is a relationship between the input and the output.

   Supervised learning problems are categorized into "regression" and "classification" problems. In a regression problem, we are trying to predict results within a continuous output, meaning that we are trying to map input variables to some continuous function. In a classification problem, we are instead trying to predict results in a discrete output. In other words, we are trying to map input variables into discrete categories.

   **Example 1:**

   Given data about the size of houses on the real estate market, try to predict their price. Price as a function of size is a continuous output, so this is a regression problem.

   We could turn this example into a classification problem by instead making our output about whether the house "sells for more or less than the asking price." Here we are classifying the houses based on price into two discrete categories.

   **Example 2**:

   \(a\) Regression - Given a picture of a person, we have to predict their age on the basis of the given picture

   \(b\) Classification - Given a patient with a tumor, we have to predict whether the tumor is malignant or benign.

4. 非监督式学习

   ## Unsupervised Learning

   Unsupervised learning allows us to approach problems with little or no idea what our results should look like. We can derive structure from data where we don't necessarily know the effect of the variables.

   We can derive this structure by clustering the data based on relationships among the variables in the data.

   With unsupervised learning there is no feedback based on the prediction results.

   **Example:**

   Clustering: Take a collection of 1,000,000 different genes, and find a way to automatically group these genes into groups that are somehow similar or related by different variables, such as lifespan, location, roles, and so on.

   Non-clustering: The "Cocktail Party Algorithm", allows you to find structure in a chaotic environment. \(i.e. identifying individual voices and music from a mesh of sounds at a[cocktail party](https://en.wikipedia.org/wiki/Cocktail_party_effect)\).

5. 模型

   # Model Representation

   To establish notation for future use, we’ll usex\(i\)to denote the “input” variables \(living area in this example\), also called input features, andy\(i\)to denote the “output” or target variable that we are trying to predict \(price\). A pair\(x\(i\),y\(i\)\)is called a training example, and the dataset that we’ll be using to learn—a list of m training examples\(x\(i\),y\(i\)\);i=1,...,m—is called a training set. Note that the superscript “\(i\)” in the notation is simply an index into the training set, and has nothing to do with exponentiation. We will also use X to denote the space of input values, and Y to denote the space of output values. In this example, X = Y = ℝ.

   To describe the supervised learning problem slightly more formally, our goal is, given a training set, to learn a function h : X → Y so that h\(x\) is a “good” predictor for the corresponding value of y. For historical reasons, this function h is called a hypothesis. Seen pictorially, the process is therefore like this:

   ![](https://d3c33hcgiwev3.cloudfront.net/imageAssetProxy.v1/H6qTdZmYEeaagxL7xdFKxA_2f0f671110e8f7446bb2b5b2f75a8874_Screenshot-2016-10-23-20.14.58.png?expiry=1493769600000&hmac=PxXp0ToZaccdxgRVxV8k10wkIglDJqHn8PkBeKOPh_I)

   When the target variable that we’re trying to predict is continuous, such as in our housing example, we call the learning problem a regression problem. When y can take on only a small number of discrete values \(such as if, given the living area, we wanted to predict if a dwelling is a house or an apartment, say\), we call it a classification problem.

6. 代价函数：判断假设函数h的准确性e Learnin

# Cost Function

We can measure the accuracy of our hypothesis function by using a**cost function**. This takes an average difference \(actually a fancier version of an average\) of all the results of the hypothesis with inputs from x's and the actual output y's.

J\(θ0,θ1\)=12m∑i=1m\(y^i−yi\)2=12m∑i=1m\(hθ\(xi\)−yi\)2

To break it apart, it is12x¯wherex¯is the mean of the squares ofhθ\(xi\)−yi, or the difference between the predicted value and the actual value.

This function is otherwise called the "Squared error function", or "Mean squared error". The mean is halved\(12\)as a convenience for the computation of the gradient descent, as the derivative term of the square function will cancel out the12term. The following image summarizes what the cost function does:

![](https://d3c33hcgiwev3.cloudfront.net/imageAssetProxy.v1/R2YF5Lj3EeajLxLfjQiSjg_110c901f58043f995a35b31431935290_Screen-Shot-2016-12-02-at-5.23.31-PM.png?expiry=1493769600000&hmac=aXQtoAFfzCrL_rclT3vEBU-ecQmEHQw9rJnaX7nE-4I)

# Cost Function - Intuition I

If we try to think of it in visual terms, our training data set is scattered on the x-y plane. We are trying to make a straight line \(defined byhθ\(x\)\) which passes through these scattered data points.

Our objective is to get the best possible line. The best possible line will be such so that the average squared vertical distances of the scattered points from the line will be the least. Ideally, the line should pass through all the points of our training data set. In such a case, the value ofJ\(θ0,θ1\)will be 0. The following example shows the ideal situation where we have a cost function of 0.

![](https://d3c33hcgiwev3.cloudfront.net/imageAssetProxy.v1/_B8TJZtREea33w76dwnDIg_3e3d4433e32478f8df446d0b6da26c27_Screenshot-2016-10-26-00.57.56.png?expiry=1493769600000&hmac=2RDhldCA9m6v5cCdOzTV1rH5kCZV5hDYpYywY3PFQ7w)

Whenθ1=1, we get a slope of 1 which goes through every single data point in our model. Conversely, whenθ1=0.5, we see the vertical distance from our fit to the data points increase.

![](https://d3c33hcgiwev3.cloudfront.net/imageAssetProxy.v1/8guexptSEeanbxIMvDC87g_3d86874dfd37b8e3c53c9f6cfa94676c_Screenshot-2016-10-26-01.03.07.png?expiry=1493769600000&hmac=CG1kTiCghrgvFFGLq5npNuCuWraUjKuCVeuKwloaMWo)

This increases our cost function to 0.58. Plotting several other points yields to the following graph:

![](https://d3c33hcgiwev3.cloudfront.net/imageAssetProxy.v1/fph0S5tTEeajtg5TyD0vYA_9b28bdfeb34b2d4914d0b64903735cf1_Screenshot-2016-10-26-01.09.05.png?expiry=1493769600000&hmac=6LIQWf13odqJcskwhR1MHAOGgj_QaZoiZLcfhTEt6o4)

Thus as a goal, we should try to minimize the cost function. In this case,θ1=1is our global minimum.

# Cost Function - Intuition II

A contour plot is a graph that contains many contour lines. A contour line of a two variable function has a constant value at all points of the same line. An example of such a graph is the one to the right below.

![](https://d3c33hcgiwev3.cloudfront.net/imageAssetProxy.v1/N2oKYp2wEeaVChLw2Vaaug_d4d1c5b1c90578b32a6672e3b7e4b3a4_Screenshot-2016-10-29-01.14.37.png?expiry=1493769600000&hmac=NLYicNZJ-oUAMtVoMu-zmWKuWuSSatd6jO7kpt3W5_0)

Taking any color and going along the 'circle', one would expect to get the same value of the cost function. For example, the three green points found on the green line above have the same value forJ\(θ0,θ1\)and as a result, they are found along the same line. The circled x displays the value of the cost function for the graph on the left whenθ0= 800 andθ1= -0.15. Taking another h\(x\) and plotting its contour plot, one gets the following graphs:

![](https://d3c33hcgiwev3.cloudfront.net/imageAssetProxy.v1/26RZhJ34EeaiZBL80Yza_A_0f38a99c8ceb8aa5b90a5f12136fdf43_Screenshot-2016-10-29-01.14.57.png?expiry=1493769600000&hmac=OkMSNl82N3K_-oEpzWDk6dnYvsAMg4FhZ7e_A0vqLNs)

Whenθ0= 360 andθ1= 0, the value ofJ\(θ0,θ1\)in the contour plot gets closer to the center thus reducing the cost function error. Now giving our hypothesis function a slightly positive slope results in a better fit of the data.

![](https://d3c33hcgiwev3.cloudfront.net/imageAssetProxy.v1/hsGgT536Eeai9RKvXdDYag_2a61803b5f4f86d4290b6e878befc44f_Screenshot-2016-10-29-09.59.41.png?expiry=1493769600000&hmac=_ojV8Yi72d4nIhTC6xzgW3fsA9DSR5b4uFqcs5l8vpM)

The graph above minimizes the cost function as much as possible and consequently, the result ofθ1andθ0tend to be around 0.12 and 250 respectively. Plotting those values on our graph to the right seems to put our point in the center of the inner most 'circle'.

# Gradient Descent

So we have our hypothesis function and we have a way of measuring how well it fits into the data. Now we need to estimate the parameters in the hypothesis function. That's where gradient descent comes in.

Imagine that we graph our hypothesis function based on its fieldsθ0andθ1\(actually we are graphing the cost function as a function of the parameter estimates\). We are not graphing x and y itself, but the parameter range of our hypothesis function and the cost resulting from selecting a particular set of parameters.

We putθ0on the x axis andθ1on the y axis, with the cost function on the vertical z axis. The points on our graph will be the result of the cost function using our hypothesis with those specific theta parameters. The graph below depicts such a setup.

![](https://d3c33hcgiwev3.cloudfront.net/imageAssetProxy.v1/bn9SyaDIEeav5QpTGIv-Pg_0d06dca3d225f3de8b5a4a7e92254153_Screenshot-2016-11-01-23.48.26.png?expiry=1493769600000&hmac=I9xfX12FRuC-5K0kstQA4HsD1B0AZPMd3Pl3Tew_dck)

We will know that we have succeeded when our cost function is at the very bottom of the pits in our graph, i.e. when its value is the minimum. The red arrows show the minimum points in the graph.

The way we do this is by taking the derivative \(the tangential line to a function\) of our cost function. The slope of the tangent is the derivative at that point and it will give us a direction to move towards. We make steps down the cost function in the direction with the steepest descent. The size of each step is determined by the parameter α, which is called the learning rate.

For example, the distance between each 'star' in the graph above represents a step determined by our parameter α. A smaller α would result in a smaller step and a larger α results in a larger step. The direction in which the step is taken is determined by the partial derivative ofJ\(θ0,θ1\). Depending on where one starts on the graph, one could end up at different points. The image above shows us two different starting points that end up in two different places.

The gradient descent algorithm is:

repeat until convergence:

θj:=θj−α∂∂θjJ\(θ0,θ1\)

where

j=0,1 represents the feature index number.

At each iteration j, one should simultaneously update the parametersθ1,θ2,...,θn. Updating a specific parameter prior to calculating another one on thej\(th\)iteration would yield to a wrong implementation.

![](https://d3c33hcgiwev3.cloudfront.net/imageAssetProxy.v1/yr-D1aDMEeai9RKvXdDYag_627e5ab52d5ff941c0fcc741c2b162a0_Screenshot-2016-11-02-00.19.56.png?expiry=1493769600000&hmac=BVgsYtUe1od7GxVCXZ97n37_fMbROrlmMB94QA7qBig)

# Gradient Descent Intuition

In this video we explored the scenario where we used one parameterθ1and plotted its cost function to implement a gradient descent. Our formula for a single parameter was :

Repeat until convergence:

| θ1:=θ1−αddθ1J\(θ1\) |
| :--- |


Regardless of the slope's sign forddθ1J\(θ1\),θ1eventually converges to its minimum value. The following graph shows that when the slope is negative, the value ofθ1increases and when it is positive, the value ofθ1decreases.

![](https://d3c33hcgiwev3.cloudfront.net/imageAssetProxy.v1/SMSIxKGUEeav5QpTGIv-Pg_ad3404010579ac16068105cfdc8e950a_Screenshot-2016-11-03-00.05.06.png?expiry=1493769600000&hmac=MuY2HmTzPVjhcnEkleBGt1dd6JTvzoZutFaSYe4oJm0)

On a side note, we should adjust our parameterαto ensure that the gradient descent algorithm converges in a reasonable time. Failure to converge or too much time to obtain the minimum value imply that our step size is wrong.

![](https://d3c33hcgiwev3.cloudfront.net/imageAssetProxy.v1/UJpiD6GWEeai9RKvXdDYag_3c3ad6625a2a4ec8456f421a2f4daf2e_Screenshot-2016-11-03-00.05.27.png?expiry=1493769600000&hmac=J3f4tjWpeU77bD0dSK9swiMxjsLYkGK7sM-4zC2KDe0)

### How does gradient descent converge with a fixed step sizeα?

The intuition behind the convergence is thatddθ1J\(θ1\)approaches 0 as we approach the bottom of our convex function. At the minimum, the derivative will always be 0 and thus we get:

| θ1:=θ1−α∗0 |
| :--- |


![](https://d3c33hcgiwev3.cloudfront.net/imageAssetProxy.v1/RDcJ-KGXEeaVChLw2Vaaug_cb782d34d272321e88f202940c36afe9_Screenshot-2016-11-03-00.06.00.png?expiry=1493769600000&hmac=sKprKxNhggWpUhlEbQLXDq4ryhlJF7J_RCP0QyZ3Y1s)

# Gradient Descent For Linear Regression

**Note:**\[At 6:15 "h\(x\) = -900 - 0.1x" should be "h\(x\) = 900 - 0.1x"\]

When specifically applied to the case of linear regression, a new form of the gradient descent equation can be derived. We can substitute our actual cost function and our actual hypothesis function and modify the equation to :

| repeat until convergence: {θ0:=θ1:=}θ0−α1m∑i=1m\(hθ\(xi\)−yi\)θ1−α1m∑i=1m\(\(hθ\(xi\)−yi\)xi\) |
| :--- |


where m is the size of the training set,θ0a constant that will be changing simultaneously withθ1andxi,yiare values of the given training set \(data\).

Note that we have separated out the two cases forθjinto separate equations forθ0andθ1; and that forθ1we are multiplyingxiat the end due to the derivative. The following is a derivation of∂∂θjJ\(θ\)for a single example :

![](https://d3c33hcgiwev3.cloudfront.net/imageAssetProxy.v1/QFpooaaaEea7TQ6MHcgMPA_cc3c276df7991b1072b2afb142a78da1_Screenshot-2016-11-09-08.30.54.png?expiry=1493769600000&hmac=14zAsaWGndut9IhPInOeKhjF9UsYyj95lQrmOXSUqXo)

The point of all this is that if we start with a guess for our hypothesis and then repeatedly apply these gradient descent equations, our hypothesis will become more and more accurate.

So, this is simply gradient descent on the original cost function J. This method looks at every example in the entire training set on every step, and is called**batch gradient descent**. Note that, while gradient descent can be susceptible to local minima in general, the optimization problem we have posed here for linear regression has only one global, and no other local, optima; thus gradient descent always converges \(assuming the learning rate α is not too large\) to the global minimum. Indeed, J is a convex quadratic function. Here is an example of gradient descent as it is run to minimize a quadratic function.

![](https://d3c33hcgiwev3.cloudfront.net/imageAssetProxy.v1/xAQBlqaaEeawbAp5ByfpEg_24e9420f16fdd758ccb7097788f879e7_Screenshot-2016-11-09-08.36.49.png?expiry=1493769600000&hmac=8ws6CpnRpkopj-kHcIzTfWvJAQyoS4GLMh9vOFe9LPQ)

The ellipses shown above are the contours of a quadratic function. Also shown is the trajectory taken by gradient descent, which was initialized at \(48,30\). The x’s in the figure \(joined by straight lines\) mark the successive values of θ that gradient descent went through as it converged to its minimum.

# Matrices and Vectors

Matrices are 2-dimensional arrays:



| ⎡⎣⎢⎢⎢adgjbehkcfil⎤⎦⎥⎥⎥ |
| :--- |


The above matrix has four rows and three columns, so it is a 4 x 3 matrix.

A vector is a matrix with one column and many rows:

| ⎡⎣⎢⎢⎢wxyz⎤⎦⎥⎥⎥ |
| :--- |


So vectors are a subset of matrices. The above vector is a 4 x 1 matrix.

**Notation and terms**:

* Aij refers to the element in the ith row and jth column of matrix A.
* A vector with 'n' rows is referred to as an 'n'-dimensional vector.
* vi refers to the element in the ith row of the vector.
* In general, all our vectors and matrices will be 1-indexed. Note that for some programming languages, the arrays are 0-indexed.
* Matrices are usually denoted by uppercase names while vectors are lowercase.
* "Scalar" means that an object is a single value, not a vector or matrix.
* ℝrefers to the set of scalar real numbers.
* ℝ𝕟refers to the set of n-dimensional vectors of real numbers.



