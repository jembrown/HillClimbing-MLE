# Maximum Likelihood Inference of the Binomial p

We are interested in learning about the probability of success from a set of binomial trials.

First, we need to define how big our sample size will be.

`sampleSize <- 10`

Next, we need to simulate a set of outcomes for these trials.

In RevBayes, functions that draw values from distributions all begin with __r__. In our case, we want to draw values from a binomial distribution, so we use `rbinom()`.

```
data <- rbinom(1,0.6,10)		    # Draw our sample from the population
data                                # Print our sample to the screen for inspection
```

Now that we have some data, we need to be able to calculate the likelihood, P(_D_|_H_). In this case, our hypothesis (_H_) is a binomial distribution with a given value of p. _D_ represents the data, the values sampled from our "true" binomial distribution.

```
logLike <- dbinom(data[1],p,sampleSize,log=TRUE)
```

We also need to define a starting place for our exploration of parameter space. In this case, we'll draw a random value for p.

```
p <- rUniform(1,0,1)  # Draw 1 value from a Uniform(0,1) distribution
```

Let's print this value, to see where we'll be starting.

```
p[1]  # Even though p has just one value, it's stored in a vector so we need to extract it
```

Now let's calculate the likelihood for the starting value of p.

```
like <- calcLike(data,p[1],1)
like
```

We can explore parameter space using a hill-climbing algorithm to find the maximum likelihood estimate (MLE) of p.

```
# A hill-climbing algorithm to find the maximum likelihood estimate of p

function findML(Real[] dat, Real p, Real stepSize){
    like = dbinom(dat[1],p,sampleSize)
    while (dbinom(dat[1],p+stepSize,sampleSize) > like){
        p = p + stepSize
        like = calcLike(dat[1],p,1)
    }
    while (dbinom(dat[1],p-stepSize,sampleSize) > like){
        p = p - stepSize
        like = calcLike(dat[1],p,1)
    }
    if(stepSize > 0.0001){
        p = findML(dat,p,stepSize/2.0)
    }
    return p
}
```

Now that we've defined our hill-climbing algorithm, let's estimate p.

`findML(data,p[1],1)`

How close is this to our true p?
