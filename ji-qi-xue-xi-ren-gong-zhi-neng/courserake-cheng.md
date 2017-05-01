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



