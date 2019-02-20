---
title: "Building Option Pricing System in C++ #01"
date: 2019-02-20
tags: [programming, quantitative finance, c++]
excerpt: "The post covers the basic option pricing theory and a prototype of a random number generator class."
mathjax: "true"
---

In this series of blog entries I will describe my progress towards building a versatile yet compact option pricing system in C++. I will follow closely a book by Mark Joshi [C++ Design Patterns and Derivatives Pricing](https://www.amazon.com/Patterns-Derivatives-Pricing-Mathematics-Finance/dp/0521721628). I will use this book as a guideline but at the same time I will try to extend and polish things that were treated vaguely in the book. Thus, I will include numerous other sources and references.  
{: .text-justify}

I will start by covering the very basics of modelling the stock price evolution. The evolution usually involves a drift component that represents steady growth over time with some diffusion component that represents the random deviation from the drift. If the financial underlying could be modelled without a stochastic component then there would not be a reason for quantitative finance to exist in and even if so then not to its current extent. One of the simplest random processes that satisfies the mentioned conditions is the Arithmetic Brownian Motion. If we denote our random process by $$X$$ then if this process follows Arithmetic Brownian Motion it would be specified by the following SDE: 
{: .text-justify}

$$dX(t) = \mu dt + \sigma dB(t)$$

where both $$\mu$$ and $$\sigma$$ are constant and greater than zero. The solution to the SDE is given by 
{: .text-justify}

$$S(T) =  X(0) + \mu T + \sigma B(T)$$

Thus, $$S(T)$$ is equal to the deterministic term $$X(0) + \mu T$$ and the stochastic term, that is normally distributed random variable $$\sigma B(T)$$. The main drawbacks of Arithmetic Brownian Motion in modelling the stock prices is that first of all it describes the absolute change in value, while investors are rather interested in rate of return. Second of all since $$B(T)$$ is a normal variable therefore, the whole equation can assume negative values, which is impossible in the financial world. This process would be good to describe a variable that grows at constant rate but with the uncertainty of growth increasing with time.
{: .text-justify}

The candidate for an optimal process to describe the behaviour of the stock price would have a fix for the two drawback mentioned above.
{: .text-justify}
