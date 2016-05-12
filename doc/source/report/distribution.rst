.. _distribution:

Power Laws & Universality
=========================

Statement of Problem:
---------------------


To take a closer look at the tail property of locomotion, we analyze the
moving distance distribution for home based mice and non home based
Mice. Fitting those distribution using some prior knowledge and obtain 
estimator for each mouse. With expectation, it is possible to cluster 
mice by the their unique distribution estimator, and further figure 
figure out with strain they belong to.


Statement of Statistical Problem:
---------------------------------

The major statistical question is how to choose fitted distribution
family. Based on conventions and data we have, we propose two
distributions: law decay distribution and gamma distribution: - In
statistics, power law, also known as a scaling law, is a functional
relationship between two quantities, where a relative change in one
quantity results in a proportional relative change in the other
quantity, independent of the initial size of those quantities: one
quantity varies as a power of another. $F(x)=kx^{-a}$. This power
law decay only works for monotone decreasing distribution. - If the
distribution is not monotone decreasing, power function may not be
realistic. In this case, the distribution is left skewed with one peak
(see exploratory analysis). Thus we can make more general assumption,
gamma distribution.

Exploratory Analysis
--------------------

-  The difference between “home base” and “non home base”: "home base",
   which means a favored location at which long periods of inactivity
   (ISs) occur, is a post defined characteristic of the mouse.
-  The definition of inter-event distance: literally, inter-event
   distance is the distance within one single event. Investigating the
   data of the distance between each two consecutive points recorded by
   the detector of one mouse day, we found the shape of the histogram is
   similar to the plot on the slide, except the value of frequency is
   bigger. This inconsistency gives us the motivation to calculate the
   inter event distance instead of the distance between each two points.
   For this purpose, we need a vector of the index of events for each
   mouse day. In particular, the mapping vector is connected by time
   since xy coordinates are recorded according to time and the event is
   defined based on time.

.. figure:: figure/project6.png
   :alt: alt tag

   Distribution

- Preferred choice of distribution: the power law is a
  monotone decreasing, however our plot indicates a peak, in which gamma
  distribution may fit better.


Data Requirements Description
-----------------------------

-  Label of “home base” or “non home base”: generated in the process of
   data pre-processing by the definition
-  Event index corresponding to the time: a vector mapped the time
   indicating the events.
-  Inter event distance: calculated by the square root of the sum of the
   difference x,y coordinates

Methodology/ Approach Description
---------------------------------

Given the estimated parameter for each distribution, we can learn more
about its distribution and the information lies mainly in the decay rate
of the tail.

Here are our algorithms:

- Draw the histogram for our data.  Observe the distribution and intuitively
  figure out whether our distribution assumption makes sense.
- Estimate parameters based on MoM or MLE.
- Add the density function to our histogram, see the fitness of
  our distribution.
- Conduct statistical test to quantitatively analysis the fitness (Pearson chi
  square test). For testing the hypothetical distributions of a given array,
  there are several existing commonly used methods. However, each approach has
  their pros and cons. Following is a short overview of these testing framework.
  We recommend that all the methods are to be tried to get a comprehensive
  understanding of the inter-event step distributions.
- Pearson Chi-square test
- Fisher’s exact test
- KS test

Testing Framework Outline
-------------------------

The potential functions are recommended to implement:

-  Retrieve data function (*retrieve\_data*): Given the number of mouse
   and the date, create a data frame containing follow variables. 1)
   position: x,y coordinates 2) time: detecting time stamp for each pair
   of coordinates, time interval label for events, time interval label
   for active state and inactive state.

-  Retrieve event function (*retrieve\_event*): Given an event label
   (e.g. Food), subset respective part of data from the data frame we
   got in *retrieve\_data*

-  Compute the distance (*compute\_distance*): Given event label,
   compute the distance between each time stamp. As we already know the
   x, y coordinates from the dataframe in *retrieve\_event*, the
   simplest way to implement this function is that:

   .. math:: distance = ((x_t2 - x_t1)^2+(y_t2 - y_t1)^2)^(1/2)

-  Draw histogram (*draw\_histogram*): Given a sub-array, using the plt
   built-in histogram function to draw the plot. Test distribution
   (*fit\_distr*): Given the testing methods (e.g. “ks”), implement the
   corresponding fitting methods. The potential output could be p-value
   of the hypothesis test.

Based on the potential functions to be implemented, the following is the
guide of testing:

-  *test\_retrieve\_data*: attain a small subset of data from x,y
   coordinate and t, and feed in the function. Compare the results with
   the counted number.

-  *test\_retrieve\_event*: Use the small data frame we get in
   test\_retrieve\_data, given different events/state. Compare the
   results with our counted number.

-  *test\_compute\_distance*: Given x = 3, y =4, the output should be 5.

-  *test\_fit\_distr*: randomly draw samples from widely used
   distributions (e.g. uniform). Test it with right(e.g. uniform) and
   wrong(e.g. gamma) distributions. Compare the p-values with given
   threshold (e.g. alpha = 95%)

Result
--------------------

We fit the power law and exponential distribution for each mouse day. For each, we got an estimator of alpha for power law and an
estimator of lambda for exponential. We store our result in a dataframe called estimation which has five columns: strain, mouse, day
alpha and lambda. Draw histogram of the estimator where red, blue and green stands for different strains.

-  The histogram of estimators from powerlaw:

.. plot:: report/plots/plot_powerlaw.py

   Histogram of the parameters of powerlaw.

-  The histogram of estimators from exponential:

.. plot:: report/plots/plot_exponential.py

   Histogram of the parameters of exponential.

We want to check the fitted curve with the original histogram of distance so we write of function to draw the power law and exponential
curve with corresponding estimator with the original histogram of distance with the input of strain, mouse and day. Here is an example of
strain 0, mouse 2, day 5. From the plot we can see the fitting is pretty well.

-  The histogram of data and fitted curve for strain 0, mouse 2, day 5:

.. plot:: report/plots/plot_fitted.py

   Histogram and fitted curve for strain 0, mouse 2, day 5.

After visualize the fitting, we want to evaluate our fitting in statistical ways. There are several tests to quantify the performance and
we adopt the KS test to evaluate the goodness of fit and GLRT test to compare fitness.

-  Fitting power law distribution and gamma distribution for strain 0, mouse 0,
   and day 0; fitting by Maximum Likelihood, and by minimizing Kolmogorov
   CDF distances:

.. plot:: report/plots/hist_kolmogorov.py

   Histogram of distances travelled in 20ms by strain 0, mouse 0, day 0.
   
- Comparsion Between truncated Exponential and Powerlaw (Pareto) distribution.

One major question we want to answer: which distribution fits better, 
truncated exponential or truncated power law, aka pareto, distribution. 
To measure the distribution of the speed, the major difference is the 
tail distribution. You can also see it from the fitted plot. Both exponential 
distribution and pareto distribution fits quite well and they are actually 
very similar with each other, and the difference is barely noticeable.
Therefore, it is hard to simply tell which distribution fits better. However, 
although the distribution is quite similar at the beginning, it diverse in 
the tail distribution. For exponential distribution, the tail decays with 
the rate e^{-x}, which is much faster than that of pareto distribution 
x^{-a}. Therefore, the goodness of fit is mainly determined by the tail 
distribution. We tried Kolmogorov test to determine whether our sample 
fits the theoretical distribution, but it does not compare two distributions.

To make comparison between two distributions, we used Generalized 
Likelihood Ratio Test to do hypothesis testing. As we cannot actually
treat different distribution equally, with that being said, to do hypothesis
testing, we must have null hypothesis and alternative hypothesis, where
we tend to protect it and only reject when the we have strong evidence
against it. Thus, we will conduct two hypothesis testings, with null being
either exponential or power law distribution. We will expect there to be 
three possible outcomes.

   - Exponential null rejected but power law null not rejected. In this case, we conclude power law is better than exponential.
   - Power law null rejected but exponential null not rejected. In this case, we conclude exponential is better than power law.
   - Both two tests not rejected. In this case, we conclude both two fits similarly and there is no one significantly better than another.
Although theoretically we should consider the case when both two tests 
are rejected, it is highly unlikely this thing happens. Because rejecting 
both two means we have enough evidence to say exponential is better 
and power law is also better, while not rejecting two might happen, as 
we tends to protect the null and if they react similarly, we don’t have 
enough evidence to reject any of them.

Here is the algorithm to conduct the test. The GLRT test statistics is the 
ratio of likelihood, with numerator being likelihood under null set while 
that under alternative in numerator. It is intuitively right that we shall reject
the null if our test statistics is too small. To make the significance level
being 0.05, it is essential to find the critical value. However, it is hard for
us to derive the distribution of test statistics and thus we use simulation
to estimate it. Thus, we draw random number from null distribution and 
then calculate the test statistics. Also, p-value is a better statistics and it 
will not only tell us whether we should reject the null, but also tell us what 
is the confidence that we reject the null.

Reference reading:
------------------

-  https://en.wikipedia.org/wiki/Power\_law

