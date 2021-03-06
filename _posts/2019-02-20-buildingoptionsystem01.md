---
title: "Building Option Pricing System in C++ #01"
date: 2019-02-20
tags: [programming, quantitative finance, c++]
excerpt: "The post covers the basic option pricing theory and a prototype of a random number generator class."
mathjax: "true"
---
## Introduction

In this series of blog entries I will describe my progress towards building a versatile yet compact option pricing system in C++. I will follow closely a book by Mark Joshi [C++ Design Patterns and Derivatives Pricing](https://www.amazon.com/Patterns-Derivatives-Pricing-Mathematics-Finance/dp/0521721628). I will use this book as a guideline but at the same time I will try to extend and polish things that were treated vaguely in the book. Thus, I will include numerous other sources and references. The idea of the project is to start on very basics and build on them to develop more sophisticated tools. I will try to focus on rather proven models with emphasis on their implementation as an integral part of bigger system.
{: .text-justify}

## Arithmetic and Geometric Brownian Motion

I will start by covering the very basics of modelling the stock price evolution. The evolution usually involves a drift component that represents steady growth over time with some diffusion component that represents the random deviation from the drift. If the financial underlying could be modelled without a stochastic component then there would not be a reason for quantitative finance to exist in and even if so then not to its current extent. One of the simplest random processes that satisfies the mentioned conditions is the Arithmetic Brownian Motion. If we denote our random process by $$S$$ then if this process follows Arithmetic Brownian Motion it would be specified by the following SDE: 
{: .text-justify}

$$dS(t) = \mu dt + \sigma dW(t)$$

where both $$\mu$$ and $$\sigma$$ are constant and greater than zero. The solution to the SDE is given by 
{: .text-justify}

$$S(T) =  S(0) + \mu T + \sigma W(T)$$

Thus, $$S(T)$$ is equal to the deterministic term $$S(0) + \mu T$$ and the stochastic term, that is normally distributed random variable $$\sigma W(T)$$. The main drawbacks of Arithmetic Brownian Motion in modelling the stock prices is that first of all it describes the absolute change in value, while investors are rather interested in rate of return. Second of all since $$W(T)$$ is a normal variable therefore, the whole equation can assume negative values, which is impossible in the financial world. This process would be good to describe a variable that grows at constant rate but with the uncertainty of growth increasing with time.
{: .text-justify}

The candidate for an optimal process to describe the behavior of the stock price would have a fix for the two drawbacks mentioned above. Therefore, the next on the list is the Geometric Brownian Motion (GBM). It is a model for the relative change in a random process. 
{: .text-justify}

$$dS(t) = \mu S(t)dt + \sigma S(t)dW(t)$$

Both the drift coefficient $$\mu S(t)dt$$ and the diffusion coefficient $$\sigma S(t)dW(t)$$ are proportional to the latest known value $$S(t)$$. Thus, both of those elements are continuously changing. The greater the last known value of $$S(t)$$ the greater the drift coefficient, in turn the diffusion coefficient would be greater because the random term is generated by the Brownian motion with the greater variance.
{: .text-justify}

The solution to the GBM, denoted further by $$S$$, is found by following the intuition that if GBM would be deterministic $$\frac{dS(t)}{S(t)}$$ would be the derivative of $$ln[S(t)]$$. Thus using the Ito's Lemma for the natural logarithm of $$S$$ gives
{: .text-justify}

$$d ln[S] = (\mu - \frac{1}{2}\sigma)dt + \sigma dW$$

and then by integrating and taking exponents we get the final expression for $$S(T)$$:
{: .text-justify}

$$S(T) = S(0)e^{(\mu - \frac{1}{2} \sigma ^2)T + \sigma W(T)}$$

Geometric Brownian Motion has a cure for the drawbacks of the Arithmetic Brownian Motion applied to the financial modelling problems. Solution to GBM being an exponential cannot become negative. And the GBM variance depends linearly on the level of the variable.
{: .text-justify}

## Monte Carlo Pricer Implementation 

I will start building the code base by implementing simple pricer of European option using Monte Carlo method as well as pricer using the Black-Scholes closed form solution. Due to its simplicity as well as possibility to compare the results with the Black-Scholes closed form solution it will make great test case for the start. The European option price of the call option paying not dividends in the Black-Scholes pricing theory is defined as the
{: .text-justify}

$$e^{-rT} \mathbf{E} [f(S_T)]$$

where $$r$$ is the continuously compounding rate of growth of the riskless bond, $$S_T$$ is the price of the underlying at the expiry time $$T$$ and $$f$$ the payoff function. Using the assumption that the price of the underlying follows GBM and noticing that since $$W(t)$$ is a Brownian motion, then $$W(T)$$ is distributed as a Gaussian variable with mean zero and variance $$T$$ the price of the vanilla European option is given as
{: .text-justify}

$$e^{-rT} \mathbf{E} [f(S(0)e^{(\mu - \frac{1}{2} \sigma ^2)T + \sigma \sqrt{T} N(0,1)})]$$

The method for approximating the expected value of some function that involves GBM is called the Monte Carlo method. By using the law of large numbers that tell us that given the sequence of identically distributed independent random variables $$Y_i$$, then with probability one the sequence $$\frac{1}{N} \sum_{i=1}^{N} Y_i$$ converges to $$\mathbf{E} [Y_i]$$. So to get the expected value a random variable $$x$$ form the $$N(0, 1)$$ distribution is to be drawn and then the value of the is function computed. After many repeats, the average of outputs is taken to get the estimate of the expected value.
{: .text-justify}

Most of the functions required to calculate the expectation using the Monte Carlo method are available out of the box in C++. Before C++11 there was no ready made pseudo-random number generator. Therefore, in the older versions of C++ the pseudorandom number generator was to be implemented by the developer. Since C++11 the language is equipped with pseudorandom number generator provided by package *random*. By importing the random module, one can choose from wide range of functions used in generating the pseudorandom numbers.
{: .text-justify}

The implementation will start with the declaration of the header file. In the Monte Carlo pricer we would need only one function, namely the pricer itself. The pricing function will take as an input the mentioned earlier parameters.
{: .text-justify}

```cpp 
// EuropeanMC.hpp
#ifndef EUROPEANMC_H
#define EUROPEANMC_H

enum OptionType {call, put};

double EuropeanMC(double expiry,
                  double strike,
                  double spot,
                  double vol,
                  double rf_rate,
                  OptionType type,
                  int number_of_paths);

#endif
```

The implementation of the pricer function divided into two parts. First we prepare all variables that are not calculated directly in Monte Carlo loop in order to speed-up the calculation. Then the actual simulation takes place and after that the mean of all simulated prices is returned.  
{: .text-justify}

```cpp
// EuropeanMC.cpp
#include "EuropeanMC.hpp"
#include <random>
#include <cmath>

using namespace std;

double EuropeanMC(double expiry,
                  double strike,
                  double spot,
                  double vol,
                  double rf_rate,
                  OptionType type,
                  int number_of_paths)
{
    double time_variance = vol * vol * expiry;
    double root_time_variance = sqrt(time_variance);
    double ito_correction = -0.5 * time_variance;

    double moved_spot = spot * exp(rf_rate * expiry + ito_correction);
    double temp_spot;
    double rolling_sum = 0;

    default_random_engine generator;
    normal_distribution<double> distribution(0.0, 1.0);

    double payoff = 0;
    for(int i = 0; i < number_of_paths; i++)
    {
        double random_number = distribution(generator);
        temp_spot = moved_spot * exp(root_time_variance * random_number); 
        
        if(type == call)
        {
            payoff = temp_spot - strike;
        }
        else 
        {
            payoff = strike - temp_spot;
        }
        
        if (payoff < 0)
        {
            payoff = 0;
        }
        rolling_sum += payoff;
    }

    double mean_result = rolling_sum / number_of_paths;
    mean_result *= exp(-rf_rate * expiry);

    return mean_result;
}
```

## Black-Scholes Closed Form Solution Formula Implementation

The next step would be to implement the Black-Scholes closed from solution pricing function. It will serve as the benchmark and indicator of implementation correctness in the test cases. It will also allow us to examine the speed of Monte Carlo convergence. The convergence will be achieved when the error between the result of closed form solution and the result of Monte Carlo will be sufficiently small.
{: .text-justify}

The implementation of pricing formula is straightforward with exception of the function calculating cumulative normal distribution that is not available out of the box. The shortcut for implementation of this function is to use the complementary error function that is available in the *std*. Let's define the error function first. Given a series of measurements that follow a normal distribution with mean of 0 and standard deviation of $$\sigma$$, the error function $$\operatorname {erf}(\frac{a}{\sigma \sqrt{2}})$$ returns the probability that the error of a single measurement lies within $$-a$$ and $$+a$$ for some positive $$a$$. The complimentary error function is thus defined as:
{: .text-justify}

$$\operatorname{erfc} (x) =1-\operatorname{erf} (x)={\frac {2}{\sqrt {\pi }}}\int _{x}^{\infty }e^{-t^{2}}\,dt$$

One may notice that the cumulative normal distribution function differ only by scaling and translation from the complimentary error function. Thus by application of the algebraic manipulation we may get normal cumulative distribution function expressed in terms of the complimentary error function. Thus, if we denote the normal CDF as:
{: .text-justify}

$${\displaystyle \Phi (x)={\frac {1}{\sqrt {2\pi }}}\int _{-\infty }^{x}e^{\tfrac {-t^{2}}{2}}\,dt={\frac {1}{2}}\operatorname {erfc} \left(-{\frac {x}{\sqrt {2}}}\right)}$$

Having the normal CDF function define one may proceed to implementation. The header file will have two functions and one enum declared.
{: .text-justify}

```cpp
// BlackScholesCFS.hpp
#ifndef BLACKSCHOLESCFS_H
#define BLACKSCHOLESCFS_H

double normalCDF(double value);

enum OptionType {call, put};

double BlackScholesCFS(double expiry,
                        double strike,
                        double spot,
                        double vol,
                        double rf_rate,
                        OptionType option_type,
                        int number_of_paths);

#endif
```

The functions are defined in the source file. The function for normal CDF is defined according to a previously mentioned formula. A constant *M_SQRT1_2* is used for the value of $$\frac{\sqrt{2}}{2}$$. Then the the second function is the implementation of the Black-Scholes closed form solution formula.  
{: .text-justify}

``` cpp
// BlackScholesCFS.cpp
#include "BlackScholesCFS.hpp"
#include <iostream>
#include <cmath>

using namespace std;

double normalCDF(double value)
{
   return 0.5 * erfc(-value * M_SQRT1_2);
}

double BlackScholesCFS(double expiry,
                        double strike,
                        double spot,
                        double vol,
                        double rf_rate,
                        OptionType option_type,
                        int number_of_paths)
{
    double d_1 = 0;
    double d_2 = 0;


    d_1 = (log(spot/strike) + expiry * (rf_rate + vol * vol / 2)) 
          / (vol * sqrt(expiry));
    d_2 = d_1 - vol * sqrt(expiry);

    double option_price = 0.0;
    if(option_type == call)
    {
        option_price = spot * normalCDF(d_1) - strike 
        * exp(-rf_rate * expiry) * normalCDF(d_2); 
    }
    else if(option_type = put)
    {
        option_price = strike * exp(-rf_rate * expiry) * normalCDF(-d_2)
        - spot * normalCDF(-d_1);
    }
    else
    {
        cout << "Wrong option type. Program terminates.";
        return 0;
    }
    
    return option_price;
}
```

## Conclusions

The next blog entry will cover the comparison and testing of those files, convergence of the Monte Carlo method and first steps towards building the unified, object oriented system for pricing derivatives. 
{: .text-justify}

sources:  
[Black-Scholes Model](https://en.wikipedia.org/wiki/Black%E2%80%93Scholes_model)  
[Brownian Motion Calculus](https://www.amazon.com/Brownian-Motion-Calculus-Ubbo-Wiersema/dp/0470021705)  
[C++ Design Patterns and Derivatives Pricing](https://www.amazon.com/Patterns-Derivatives-Pricing-Mathematics-Finance/dp/0521721628)  
[Error Function](https://en.wikipedia.org/wiki/Error_function#Cumulative_distribution_function)