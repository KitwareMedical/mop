# Multiscale Optimal Transport

> [!WARNING]
> This repo will remain archived unless development resumes.

An R package implementation of the fast multiscale approach to optimal transport described in:  
>Multiscale Strategies for Computing Optimal Transport  
>Samuel Gerber, Mauro Maggioni  
>Journal of Machine Learning Research; 18(72):1−32, 2017.  
>http://jmlr.org/papers/v18/16-108.html  

![scale-4.png](https://bitbucket.org/repo/XyGX46/images/333242785-scale-4.png)
![scale-6.png](https://bitbucket.org/repo/XyGX46/images/661701334-scale-6.png)
![scale-11.png](https://bitbucket.org/repo/XyGX46/images/104944428-scale-11.png)

The code is designed for fast multiscale optimal transport but also permits single scale optimal transport computations and more:

* Optimal transport solved with Lemon, CPLEX, GPLK or MOSEK
* Fast approximate transport using a mutliscale strategy

## Install

```R
library(devtools)
if(!require("gmra")){
  devtools::install_github("samuelgerber/gmra")
}
Sys.setenv("R_REMOTES_NO_ERRORS_FROM_WARNINGS" = "true")
devtools::install_github("samuelgerber/mop")
```

## Example
```R
     #create example data  
     phi <- runif(1000)*2*pi
     X1<- cbind(cos(phi), sin(phi)) * (1+rnorm(length(phi)) * 0.1)
     X1[,1]=X1[,1]*0.5;
     X1[,2]=X1[,2]*2;
     
     phi <- runif(1653)*2*pi
     X2<- cbind(cos(phi), sin(phi)) * (1+rnorm(length(phi)) * 0.1)
     X2[,1]=X2[,1]*3;
     X2[,2]=X2[,2]*0.5;
     
     #create multiscale decompsotions
     library(gmra)
     gmra1 = gmra.create.ipca(X=X1, eps=0, d=2, maxKids=2)
     gmra2 = gmra.create.ipca(X=X2, eps=0, d=2, maxKids=2)
     
     #setup and solve multiscale lp
     library(mop)
     trp.lp <- multiscale.transport.create.lp(oType=26, transport.type=5, massCost=0.1)
     icprop <- multiscale.transport.create.iterated.capacity.propagation.strategy(1, 0)
     multiscale.transport.set.propagation.strategy.1(trp.lp, icprop);
     
     time1 <- system.time( 
         trp1 <- multiscale.transport.solve(trp.lp, gmra1, gmra2, p = 2, nType=0, dType=1, scaleMass=FALSE) )
     
     multiscale.transport.plot.map(trp1, 100, mapAlpha=0.1)
     
     #add nieghborhood expansion
     multiscale.transport.add.expand.neighborhood.strategy(trp.lp, 1 ) 
     time2 <- system.time( 
         trp2 <- multiscale.transport.solve(trp.lp, gmra1, gmra2, p = 2, nType=0, dType=1) )
     
     #solve optimal
     time3 <- system.time( 
         trp3 <- multiscale.transport.solve(trp.lp, gmra1, gmra2, p = 2, nType=0, dType=1, scale1=0, scale2=0, scaleMass=FALSE) )
     
     multiscale.transport.plot.map(trp3, 100, mapAlpha=0.1)
     
     trp1$cost
     trp2$cost
     trp3$cost
     time1
     time2
     time3
```
