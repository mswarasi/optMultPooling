# OptimalMultiplexGT

This repository contains R programs of the working paper, "Optimizing pooled testing for determining prevalence and/or identifying infected." Our goal is to examine the optimal pooling strategies with multiple (correlated) infections. Particularly, we intend to determine the pool size that (a) maximizes the estimation efficiency (precision), (b) maximizes the testing efficiency, or (c) offers the best cost-effective estimation as a compromise between optimal estimation and optimal testing. Another goal is to develop user-friendly R programs that practitioners can easily use in surveillance applications with pooled testing data.

We consider two types of pooling protocols: master pooled testing (i.e., when only initial pools are tested) and two-stage hierarchical testing (i.e., the Dorfman-type pooling as in Tebbs et al. (2013)). Under both protocols, we use a duplex discriminatory assay that provides diagnostic outcomes for two infections such as chlamydia and gonorrhea from each single test. Our approach involves calculating the maximum likelihood estimate (MLE) of the multinomial disease prevalence parameter p=(p00, p10, p01, p11) based on the methods in Tebbs et al. (2013) and Li et al. (2017).


Files uploaded:

1). multMLE.MPT.txt -- Provides the main funciton **mult.mle.mpt** for maximum likelihood estimation from initial pooled responses (Li et al., 2017)

2). multMLE.DT.txt -- Provides the main funciton **mult.mle.dt** for maximum likelihood estimation from two-stage hierarchical testing data (Tebbs et al., 2013)

3). InitialPooledTesting.txt -- Provides a function to simulate initial pooled responses

4). TwoStageHierPooling.txt -- Provides a function to simulate two-stage hierarchical pooling data

5). covtwodisgen.dll -- Compiled FORTRAN subroutines used in function **mult.mle.dt**

6). gbbstwodisgen.dll -- Compiled FORTRAN subroutines used in function **mult.mle.dt**

7). SimulationExamples.R -- Provides simulation examples

We briefly illustrate how functions **mult.mle.mpt** and **mult.mle.dt** can be used to calculate the maximum likelihood estimate of p=(p00,p10,p01,p11).


References
1. Hungerford, L., Warasi, M., and Lahmers, K. (2021+). Optimizing pooled testing for determining prevalence and/or identifying infected. In preparation.
2. Tebbs, J., McMahan, C., and Bilder, C. (2013). Two-stage hierarchical group testing for multiple infections with application to the Infertility Prevention Project. _Biometrics_, **69**, 1064-1073.
3. Li, Q., Liu, A., and Xiong, W. (2017). D-optimality of group testing for joint estimation of correlated rare diseases with misclassification. _Statistica Sinica_, **27**, 823-838.



# Usage

mult.mle.mpt(multData,psz,Se,Sp,covariance=FALSE)

mult.mle.dt(p0=rep(.25,4),Ytmat,Zmat,G=500,a=100,tol=10^(-3),covariance=FALSE)


# SIMULATION EXAMPLES 
psz <- 5           # Pool size

N <- 1000          # Sample size

Se <- c(.95, .99)  # Sensitivities for diseases 1 & 2   

Sp <- c(.95, .99)  # Specificities for diseases 1 & 2 

p <- c(0.85, 0.07, 0.05, 0.03)  # True parameter


## Estimation based on the work in Li et al. (2017):

set.seed(123)

mpt <- gt.mpt(p=p,N=N,psz=psz,Se=Se,Sp=Sp)

M <- colSums( mpt$Zmul )

res1 <- mult.mle.mpt(multData=M,psz=psz,Se=Se,Sp=Sp,covariance=TRUE)

print( res1 )


### Estimation results:

$param

[1] 0.84932645 0.08155183 0.05521984 0.01390187

$covariance

              [,1]          [,2]          [,3]
              
[1,]  2.175047e-04 -1.462123e-04 -9.388685e-05

[2,] -1.462123e-04  1.532123e-04  3.002627e-05

[3,] -9.388685e-05  3.002627e-05  1.030300e-04

$std.err

[1] 0.014748040 0.012377897 0.010150372 0.007321286


$summary

          Disease.1   Disease.2
          
Estimate 0.09545371 0.069121716

Std.Err  0.01152220 0.008848296



## Estimation based on the work in Tebbs et al. (2013):

set.seed(123)

h2d <- hier.alg.data(p=p,N=N,c.s=c(psz,1),Se=Se,Sp=Sp)

h2d.Yt <- h2d$Ytmat

h2d.Yt[ ,c(1,2)] <- 0

param0 <- rep(.25, 4)  # Initial value of the param

res2 <- mult.mle.dt(p0=param0,Ytmat=h2d.Yt,Zmat=h2d$Zmat,G=3000,a=1000,tol=10^(-3),covariance=TRUE )
                         
print( res2 )


### Estimation results:

$param

[1] 0.8487080 0.0792350 0.0563485 0.0157085

$covariance

               [,1]          [,2]          [,3]
               
[1,]  1.286700e-04 -6.753280e-05 -4.784769e-05

[2,] -6.753280e-05  7.326994e-05 -4.490042e-06

[3,] -4.784769e-05 -4.490042e-06  5.322131e-05

$std.err

[1] 0.011343281 0.008559786 0.007295294 0.003926857

$summary

           Disease.1   Disease.2
           
Estimate 0.094943500 0.072057000

Std.Err  0.009284178 0.008177674

