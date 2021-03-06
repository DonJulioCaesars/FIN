
# Coursera: Financial Engineering I: In-Class Examples in R

Course page: [https://www.coursera.org/learn/financial-engineering-1](https://www.coursera.org/learn/financial-engineering-1)

* [Lesson 3.1: Mechanics of the Futures Margin Account](#3.1)
* [Lesson 3.2: Stock Price Dynamics in the Binomial Model](#3.2)
* [Lesson 3.4: Option pricing in the 1-period binomial model](#3.4)
* [Lesson 4.1: Option pricing in the multi-period binomial model](#4.1)
* [Lesson 4.2: Pricing American Options](#4.2)
* [Lesson 4.4: Pricing a European Put on a Futures Contract](#4.4)
* [Lesson 5.1: Pricing a ZCB ](#5.1)
* [Lesson 5.2: Fixed-Income Derivatives](#5.2)
* [Lesson 5.3: Caplets, Floorlets, Swaps, Swaptions](#5.3)
* [Lesson 5.4: The Forward Equations (Elementary Securities)](#5.4)
* [Lesson 6.1: Black-Derman-Toy (BDT) Model Calibration](#6.1)
* [Lesson 6.2: Pricing using Calibrated BDT Model](#6.2)
* [Lesson 6.3: Pricing Defaultable Bonds, Calibrating Hazard Rates](#6.3)
* [Lesson 6.5: Pricing Credit Default Swaps](#6.5)
* [Lesson 7.1: Mortgage mathematics](#7.1)
* [Lesson 7.2: MBS Pass Thru Mathematics](#7.2)
* [Lesson 7.4: MBS Pass-Thru Sequential CMO with Tranches](#7.4)


```r
    source("fin-eng-1.R")
```

<br /><br />
## <a name="3.1"></a>Lesson 3.1: Mechanics of the Futures Margin Account

### Corn Futures Margin Account


```r
    spot.prices <- c(690.25,
                     693.50,
                     705.00,
                     709.50,
                     719.50,
                     724.25,
                     723.00,
                     732.00,
                     708.00,
                     711.50,
                     725.25,
                     734.50,
                     741.00,
                     741.25,
                     732.75,
                     730.50) / 100

    margin.schedule <- buildMarginAccountSchedule( spot.price.vec=spot.prices,
                                                   num.contracts=5000,
                                                   initial.margin=1688,
                                                   maintenance.margin=1250 )

    data.frame(margin.schedule)
```

```
##    spot.price  profit margin.account margin.call
## 1      6.9025     0.0         1688.0           0
## 2      6.9350   162.5         1850.5           0
## 3      7.0500   575.0         2425.5           0
## 4      7.0950   225.0         2650.5           0
## 5      7.1950   500.0         3150.5           0
## 6      7.2425   237.5         3388.0           0
## 7      7.2300   -62.5         3325.5           0
## 8      7.3200   450.0         3775.5           0
## 9      7.0800 -1200.0         2575.5           0
## 10     7.1150   175.0         2750.5           0
## 11     7.2525   687.5         3438.0           0
## 12     7.3450   462.5         3900.5           0
## 13     7.4100   325.0         4225.5           0
## 14     7.4125    12.5         4238.0           0
## 15     7.3275  -425.0         3813.0           0
## 16     7.3050  -112.5         3700.5           0
```

```r
    sum(margin.schedule$profit)
```

```
## [1] 2012.5
```


### Random Corn Futures Margin Account w/ Margin Call


```r
    # (random) spot prices, quoted in dollars per bushel
    spot.prices <- c(690.25,
                     686.81,
                     686.59,
                     668.51,
                     662.08,
                     674.89,
                     661.97,
                     709.88,
                     719.20,
                     709.36,
                     725.84,
                     743.18,
                     733.77,
                     743.12,
                     776.84,
                     796.17) / 100

    margin.schedule <- buildMarginAccountSchedule( spot.price.vec=spot.prices,
                                                   num.contracts=5000,
                                                   initial.margin=1688,
                                                   maintenance.margin=1250 )

    data.frame(margin.schedule)
```

```
##    spot.price profit margin.account margin.call
## 1      6.9025    0.0         1688.0           0
## 2      6.8681 -172.0         1516.0           0
## 3      6.8659  -11.0         1505.0           0
## 4      6.6851 -904.0         1688.0        1087
## 5      6.6208 -321.5         1366.5           0
## 6      6.7489  640.5         2007.0           0
## 7      6.6197 -646.0         1361.0           0
## 8      7.0988 2395.5         3756.5           0
## 9      7.1920  466.0         4222.5           0
## 10     7.0936 -492.0         3730.5           0
## 11     7.2584  824.0         4554.5           0
## 12     7.4318  867.0         5421.5           0
## 13     7.3377 -470.5         4951.0           0
## 14     7.4312  467.5         5418.5           0
## 15     7.7684 1686.0         7104.5           0
## 16     7.9617  966.5         8071.0           0
```

```r
    sum(margin.schedule$profit)
```

```
## [1] 5296
```



<br /><br />
## <a name="3.2"></a>Lesson 3.2: Stock Price Dynamics in the Binomial Model


```r
    stock.price.lattice <- buildAssetPriceLattice(S0=100,
                                                  n=3,
                                                  u=1.07,
                                                  d=1/1.07) 
    stock.price.lattice
```

```
##      [,1]      [,2]      [,3]      [,4]
## [1,]  100 107.00000 114.49000 122.50430
## [2,]   NA  93.45794 100.00000 107.00000
## [3,]   NA        NA  87.34387  93.45794
## [4,]   NA        NA        NA  81.62979
```

<br /><br />
## <a name="3.4"></a>Lesson 3.4: Option pricing in the 1-period binomial model


```r
    u = 1.07
    d = 1/u
    R = 1.01
    q = (R - d) / (u - d)

    option.price.lattice <- buildOptionPriceLattice(asset.price.lattice=stock.price.lattice,
                                                    n=1,
                                                    K=102,
                                                    R=1.01,
                                                    q=q,
                                                    is.call.option=T,
                                                    is.amer.option=F)
    option.price.lattice 
```

```
##          [,1] [,2]
## [1,] 2.757108    5
## [2,]       NA    0
```


<br /><br />
## <a name="4.1"></a>Lesson 4.1: Option pricing in the multi-period binomial model


```r
    option.price.lattice <- buildOptionPriceLattice(asset.price.lattice=stock.price.lattice,
                                                    n=3,
                                                    K=100,
                                                    R=1.01,
                                                    q=q,
                                                    is.call.option=T,
                                                    is.amer.option=F)
    option.price.lattice 
```

```
##          [,1]     [,2]      [,3]    [,4]
## [1,] 6.574383 10.22933 15.480099 22.5043
## [2,]       NA  2.12846  3.859951  7.0000
## [3,]       NA       NA  0.000000  0.0000
## [4,]       NA       NA        NA  0.0000
```

<br /><br />
## <a name="4.2"></a>Lesson 4.2: Pricing American Options




```r
    option.price.lattice <- buildOptionPriceLattice(asset.price.lattice=stock.price.lattice,
                                                    n=3,
                                                    K=100,
                                                    R=1.01,
                                                    q=q,
                                                    is.call.option=F,
                                                    is.amer.option=T)
    option.price.lattice 
```

```
##          [,1]     [,2]      [,3]      [,4]
## [1,] 3.823931 1.258939  0.000000  0.000000
## [2,]       NA 7.134456  2.869852  0.000000
## [3,]       NA       NA 12.656127  6.542056
## [4,]       NA       NA        NA 18.370212
```

```r
    shouldExerciseOption( asset.price.lattice = stock.price.lattice,
                          option.price.lattice = option.price.lattice,
                          K=100,
                          is.call.option = F )
```

```
##       [,1]  [,2]  [,3]  [,4]
## [1,] FALSE FALSE FALSE FALSE
## [2,]    NA FALSE FALSE FALSE
## [3,]    NA    NA  TRUE  TRUE
## [4,]    NA    NA    NA  TRUE
```


<br /><br />
## <a name="4.4"></a>Lesson 4.4: Pricing a European Put on a Futures Contract

#### Step 1: Calibrate a Binomial Model via Black-Scholes Parameters


```r
    T = 0.50
    sigma = 20/100
    n = 10
    r = 2/100
    c = 1/100

    bin.model <- calibrateBinomialModel(T=T,
                                        n=n,
                                        r=r,
                                        c=c,
                                        sigma=sigma)

    rbind(bin.model)
```

```
##           q         u        d         R        n  r    sigma T   c   
## bin.model 0.4944112 1.045736 0.9562639 1.001001 10 0.02 0.2   0.5 0.01
```

#### Step 2: Build stock price lattice


```r
    stock.price.lattice <- buildAssetPriceLattice(S0=100,
                                                  n=n,
                                                  u=bin.model$u,
                                                  d=bin.model$d) 
    stock.price.lattice
```

```
##       [,1]      [,2]      [,3]      [,4]      [,5]      [,6]      [,7]
##  [1,]  100 104.57364 109.35647 114.35804 119.58837 125.05792 130.77762
##  [2,]   NA  95.62639 100.00000 104.57364 109.35647 114.35804 119.58837
##  [3,]   NA        NA  91.44406  95.62639 100.00000 104.57364 109.35647
##  [4,]   NA        NA        NA  87.44466  91.44406  95.62639 100.00000
##  [5,]   NA        NA        NA        NA  83.62017  87.44466  91.44406
##  [6,]   NA        NA        NA        NA        NA  79.96295  83.62017
##  [7,]   NA        NA        NA        NA        NA        NA  76.46568
##  [8,]   NA        NA        NA        NA        NA        NA        NA
##  [9,]   NA        NA        NA        NA        NA        NA        NA
## [10,]   NA        NA        NA        NA        NA        NA        NA
## [11,]   NA        NA        NA        NA        NA        NA        NA
##            [,8]      [,9]     [,10]     [,11]
##  [1,] 136.75892 143.01379 149.55473 156.39483
##  [2,] 125.05792 130.77762 136.75892 143.01379
##  [3,] 114.35804 119.58837 125.05792 130.77762
##  [4,] 104.57364 109.35647 114.35804 119.58837
##  [5,]  95.62639 100.00000 104.57364 109.35647
##  [6,]  87.44466  91.44406  95.62639 100.00000
##  [7,]  79.96295  83.62017  87.44466  91.44406
##  [8,]  73.12137  76.46568  79.96295  83.62017
##  [9,]        NA  69.92333  73.12137  76.46568
## [10,]        NA        NA  66.86515  69.92333
## [11,]        NA        NA        NA  63.94073
```


#### Step 3: Build Futures price lattice


```r
    futures.price.lattice <- buildFuturesPriceLattice(asset.price.lattice=stock.price.lattice,
                                                      n=n,
                                                      q=bin.model$q)

    futures.price.lattice
```

```
##           [,1]      [,2]      [,3]      [,4]      [,5]      [,6]      [,7]
##  [1,] 100.5013 105.04529 109.79477 114.75900 119.94768 125.37096 131.03944
##  [2,]       NA  96.05768 100.40080 104.94029 109.68503 114.64430 119.82779
##  [3,]       NA        NA  91.81057  95.96167 100.30045 104.83540 109.57540
##  [4,]       NA        NA        NA  87.75125  91.71881  95.86575 100.20020
##  [5,]       NA        NA        NA        NA  83.87141  87.66354  91.62714
##  [6,]       NA        NA        NA        NA        NA  80.16311  83.78758
##  [7,]       NA        NA        NA        NA        NA        NA  76.61877
##  [8,]       NA        NA        NA        NA        NA        NA        NA
##  [9,]       NA        NA        NA        NA        NA        NA        NA
## [10,]       NA        NA        NA        NA        NA        NA        NA
## [11,]       NA        NA        NA        NA        NA        NA        NA
##            [,8]      [,9]     [,10]     [,11]
##  [1,] 136.96422 143.15688 149.62953 156.39483
##  [2,] 125.24565 130.90847 136.82732 143.01379
##  [3,] 114.52971 119.70802 125.12046 130.77762
##  [4,] 104.73062 109.46588 114.41524 119.58837
##  [5,]  95.76994 100.10005 104.62594 109.35647
##  [6,]  87.57592  91.53555  95.67422 100.00000
##  [7,]  80.08298  83.70383  87.48839  91.44406
##  [8,]  73.23113  76.54219  80.00294  83.62017
##  [9,]        NA  69.99329  73.15794  76.46568
## [10,]        NA        NA  66.89859  69.92333
## [11,]        NA        NA        NA  63.94073
```

#### Step 4: Build Put Option price lattice


```r
    option.price.lattice <- buildOptionPriceLattice(asset.price.lattice=futures.price.lattice,
                                                    n=n,
                                                    K=100,
                                                    R=bin.model$R,
                                                    q=bin.model$q,
                                                    is.call.option=F,
                                                    is.amer.option=F)
    option.price.lattice
```

```
##           [,1]     [,2]     [,3]       [,4]       [,5]       [,6]
##  [1,] 5.212449 3.151526 1.612738  0.6284893  0.1420516  0.0000000
##  [2,]       NA 7.238124 4.662531  2.5784187  1.1054165  0.2812439
##  [3,]       NA       NA 9.771099  6.7097950  4.0239583  1.9135557
##  [4,]       NA       NA       NA 12.7840590  9.3495311  6.0956671
##  [5,]       NA       NA       NA         NA 16.1679546 12.5499604
##  [6,]       NA       NA       NA         NA         NA 19.7379567
##  [7,]       NA       NA       NA         NA         NA         NA
##  [8,]       NA       NA       NA         NA         NA         NA
##  [9,]       NA       NA       NA         NA         NA         NA
## [10,]       NA       NA       NA         NA         NA         NA
## [11,]       NA       NA       NA         NA         NA         NA
##             [,7]      [,8]      [,9]     [,10]     [,11]
##  [1,]  0.0000000  0.000000  0.000000  0.000000  0.000000
##  [2,]  0.0000000  0.000000  0.000000  0.000000  0.000000
##  [3,]  0.5568267  0.000000  0.000000  0.000000  0.000000
##  [4,]  3.2440769  1.102445  0.000000  0.000000  0.000000
##  [5,]  8.8962770  5.344781  2.182699  0.000000  0.000000
##  [6,] 16.1477031 12.386861  8.447534  4.321461  0.000000
##  [7,] 23.2878962 19.857355 16.263609 12.499104  8.555936
##  [8,]         NA 26.688679 23.410946 19.977073 16.379831
##  [9,]         NA        NA 29.946761 26.815231 23.534319
## [10,]         NA        NA        NA 33.068321 30.076673
## [11,]         NA        NA        NA        NA 36.059268
```




<br /><br />
## <a name="5.1"></a>Lesson 5.1: Pricing a ZCB 


#### Step 1: Build A Sample Short Rate Lattice


```r
    short.rate.lattice <- buildShortRateLattice(r0=0.06,
                                                n=5,
                                                u=1.25,
                                                d=0.9)

    short.rate.lattice
```

```
##      [,1]  [,2]    [,3]      [,4]      [,5]       [,6]
## [1,] 0.06 0.075 0.09375 0.1171875 0.1464844 0.18310547
## [2,]   NA 0.054 0.06750 0.0843750 0.1054688 0.13183594
## [3,]   NA    NA 0.04860 0.0607500 0.0759375 0.09492188
## [4,]   NA    NA      NA 0.0437400 0.0546750 0.06834375
## [5,]   NA    NA      NA        NA 0.0393660 0.04920750
## [6,]   NA    NA      NA        NA        NA 0.03542940
```

#### Step 2: Price ZCB using short-rate lattice


```r
    zcb.price.lattice <- buildZCBPriceLattice(short.rate.lattice = short.rate.lattice,
                                              face.value = 100,
                                              n=4,
                                              q=0.5)
    zcb.price.lattice
```

```
##          [,1]     [,2]     [,3]     [,4] [,5]
## [1,] 77.21774 79.26800 83.07635 89.51049  100
## [2,]       NA 84.43361 87.34985 92.21902  100
## [3,]       NA       NA 90.63619 94.27292  100
## [4,]       NA       NA       NA 95.80930  100
## [5,]       NA       NA       NA       NA  100
```


<br /><br />
## <a name="5.2"></a>Lesson 5.2: Fixed-Income Derivatives


### Pricing a Euro Call Option on ZCB:


```r
    option.price.lattice <- buildOptionPriceLattice(asset.price.lattice=zcb.price.lattice,
                                                    n=2,
                                                    K=84,
                                                    short.rate.lattice = short.rate.lattice,
                                                    q=0.5,
                                                    is.call.option=T,
                                                    is.amer.option=F)
    option.price.lattice 
```

```
##          [,1]     [,2]     [,3]
## [1,] 2.969474 1.558072 0.000000
## [2,]       NA 4.737214 3.349855
## [3,]       NA       NA 6.636192
```

### Pricing an American Put Option on ZCB:


```r
    option.price.lattice <- buildOptionPriceLattice(asset.price.lattice=zcb.price.lattice,
                                                    n=3,
                                                    K=88,
                                                    short.rate.lattice = short.rate.lattice,
                                                    q=0.5,
                                                    is.call.option=F,
                                                    is.amer.option=T)
    option.price.lattice 
```

```
##          [,1]     [,2]      [,3] [,4]
## [1,] 10.78226 8.731999 4.9236527    0
## [2,]       NA 3.566392 0.6501451    0
## [3,]       NA       NA 0.0000000    0
## [4,]       NA       NA        NA    0
```

```r
    shouldExerciseOption( asset.price.lattice = zcb.price.lattice,
                          option.price.lattice = option.price.lattice,
                          K=88, 
                          is.call.option = F )
```

```
##      [,1] [,2]  [,3]  [,4]
## [1,] TRUE TRUE  TRUE FALSE
## [2,]   NA TRUE  TRUE FALSE
## [3,]   NA   NA FALSE FALSE
## [4,]   NA   NA    NA FALSE
```


### Pricing a Forward on a Coupon-Bearing Bond:



#### Step 1: Price the Coupon-Bearing Bond:


```r
    bond.price.lattice <- buildBondPriceLattice( short.rate.lattice = short.rate.lattice,
                                                 face.value = 100,
                                                 coupon.rate = 0.10,
                                                 n = 6,
                                                 q = 0.5)
    bond.price.lattice
```

```
##          [,1]     [,2]     [,3]     [,4]     [,5]     [,6] [,7]
## [1,] 124.1371 115.8298 108.9798 104.0300 101.6554 102.9757  110
## [2,]       NA 126.1409 118.5542 112.4884 108.4429 107.1872  110
## [3,]       NA       NA 126.2709 119.2747 113.8289 110.4638  110
## [4,]       NA       NA       NA 124.5687 117.9973 112.9631  110
## [5,]       NA       NA       NA       NA 121.1626 114.8410  110
## [6,]       NA       NA       NA       NA       NA 116.2361  110
## [7,]       NA       NA       NA       NA       NA       NA  110
```

```r
    # ex-coupon at t=4:
    ex.coupon.price <- bond.price.lattice[,4+1] - 100 * 0.10
    t(t(ex.coupon.price))
```

```
##           [,1]
## [1,]  91.65536
## [2,]  98.44286
## [3,] 103.82894
## [4,] 107.99732
## [5,] 111.16255
## [6,]        NA
## [7,]        NA
```

#### Step 2: Build Forward Price Lattice:


```r
    forward.price.lattice <- buildBinomialPriceLattice( final.price.vec = ex.coupon.price,
                                                        short.rate.lattice = short.rate.lattice,
                                                        n = 4,
                                                        q = 0.5)
    forward.price.lattice
```

```
##          [,1]     [,2]     [,3]      [,4]      [,5]
## [1,] 79.82696 79.99109 81.52936  85.07893  91.65536
## [2,]       NA 89.24207 90.45149  93.26654  98.44286
## [3,]       NA       NA 97.67079  99.84740 103.82894
## [4,]       NA       NA       NA 104.98777 107.99732
## [5,]       NA       NA       NA        NA 111.16255
```

#### Step 3: Price ZCB with T=4 and face value=1:


```r
    zcb.price.lattice <- buildZCBPriceLattice(short.rate.lattice = short.rate.lattice,
                                              face.value = 1,
                                              n=4,
                                              q=0.5)
    zcb.price.lattice
```

```
##           [,1]      [,2]      [,3]      [,4] [,5]
## [1,] 0.7721774 0.7926800 0.8307635 0.8951049    1
## [2,]        NA 0.8443361 0.8734985 0.9221902    1
## [3,]        NA        NA 0.9063619 0.9427292    1
## [4,]        NA        NA        NA 0.9580930    1
## [5,]        NA        NA        NA        NA    1
```

#### Step 4: Compute fair-value of forward price


```r
    forward.price.lattice[1,1] / zcb.price.lattice[1,1]
```

```
## [1] 103.379
```


### Pricing a Futures on a Coupon-Bearing Bond:



```r
    futures.price.lattice <- buildFuturesPriceLattice( final.price.vec = ex.coupon.price,
                                                       n = 4,
                                                       q = 0.5)
    futures.price.lattice
```

```
##         [,1]     [,2]      [,3]      [,4]      [,5]
## [1,] 103.222 100.8085  98.09251  95.04911  91.65536
## [2,]      NA 105.6355 103.52452 101.13590  98.44286
## [3,]      NA       NA 107.74653 105.91313 103.82894
## [4,]      NA       NA        NA 109.57994 107.99732
## [5,]      NA       NA        NA        NA 111.16255
```



<br /><br />
## <a name="5.3"></a>Lesson 5.3: Caplets, Floorlets, Swaps, Swaptions


### Pricing a Caplet


```r
    K <- 0.02

    # final cash flow is discounted (paid in arrears)
    caplet.final.price <- pmax( (short.rate.lattice[,6] - K) / (1 + short.rate.lattice[,6]), 0 )

    caplet.price.lattice <- buildBinomialPriceLattice(final.price.vec = caplet.final.price,
                                                      short.rate.lattice = short.rate.lattice,
                                                      n = 5,  # technically expires at t=6
                                                      q=0.5)
    caplet.price.lattice 
```

```
##            [,1]       [,2]       [,3]       [,4]       [,5]       [,6]
## [1,] 0.04204522 0.05150267 0.06367244 0.08004766 0.10321618 0.13786215
## [2,]         NA 0.03763321 0.04705830 0.05923580 0.07564031 0.09880932
## [3,]         NA         NA 0.03227250 0.04123367 0.05282733 0.06842669
## [4,]         NA         NA         NA 0.02644821 0.03464991 0.04525112
## [5,]         NA         NA         NA         NA 0.02056019 0.02783768
## [6,]         NA         NA         NA         NA         NA 0.01490145
```

### Pricing an Interest Rate Swap


```r
    swap.price.lattice <- buildInterestRateSwapPriceLattice( short.rate.lattice = short.rate.lattice,
                                                             n = 5,  # technically expires at t=6
                                                             q = 0.5,
                                                             K = 0.05) 
    swap.price.lattice
```

```
##            [,1]       [,2]       [,3]         [,4]        [,5]
## [1,] 0.09900443 0.14025187 0.16860896  0.179274211  0.16475480
## [2,]         NA 0.04963752 0.08293256  0.102057879  0.10143601
## [3,]         NA         NA 0.01370333  0.040003133  0.05115201
## [4,]         NA         NA         NA -0.008464509  0.01221463
## [5,]         NA         NA         NA           NA -0.01736413
## [6,]         NA         NA         NA           NA          NA
##              [,6]
## [1,]  0.112505159
## [2,]  0.072303710
## [3,]  0.041027471
## [4,]  0.017170269
## [5,] -0.000755332
## [6,] -0.014072036
```

### Pricing a Swaption


```r
    swaption.price.lattice <- buildOptionPriceLattice(asset.price.lattice = swap.price.lattice,
                                                      n=3,
                                                      K=0,
                                                      short.rate.lattice = short.rate.lattice,
                                                      q=0.5,
                                                      is.call.option=T,
                                                      is.amer.option=F)
    swaption.price.lattice
```

```
##            [,1]       [,2]       [,3]       [,4]
## [1,] 0.06197181 0.09076654 0.12860896 0.17927421
## [2,]         NA 0.04061369 0.06653912 0.10205788
## [3,]         NA         NA 0.01907454 0.04000313
## [4,]         NA         NA         NA 0.00000000
```


<br /><br />
## <a name="5.4"></a>Lesson 5.4: The Forward Equations (Elementary Securities)


### Building the Elementary Security Price Lattice


```r
    elem.price.lattice <- buildElementarySecurityPriceLattice(short.rate.lattice = short.rate.lattice,
                                                              n = 6,
                                                              q=0.5)
    elem.price.lattice
```

```
##      [,1]      [,2]      [,3]      [,4]      [,5]       [,6]        [,7]
## [1,]    1 0.4716981 0.2193945 0.1002946 0.0448871 0.01957598 0.008273132
## [2,]   NA 0.4716981 0.4431602 0.3078638 0.1868416 0.10408383 0.054253219
## [3,]   NA        NA 0.2237657 0.3142665 0.2900886 0.21931522 0.146131169
## [4,]   NA        NA        NA 0.1066974 0.1992471 0.22926638 0.207450993
## [5,]   NA        NA        NA        NA 0.0511130 0.11904756 0.164032044
## [6,]   NA        NA        NA        NA        NA 0.02458855 0.068605732
## [7,]   NA        NA        NA        NA        NA         NA 0.011873599
```

### Pricing a ZCB using Elementary Prices


```r
    # all ZCB bond prices, w/ expirations t=0 thru t=6
    zcb.prices <- colSums( elem.price.lattice, na.rm=T) * 100
    zcb.prices
```

```
## [1] 100.00000  94.33962  88.63204  82.91223  77.21774  71.58775  66.06199
```

```r
    # spot rates
    (100/zcb.prices)^(1/0:6) - 1
```

```
## [1] 0.00000000 0.06000000 0.06219594 0.06445458 0.06676984 0.06913428
## [7] 0.07153919
```

### Pricing a Forward-Start Swap using Elementary Prices


```r
    swap.coupon.lattice <- buildInterestRateSwapCouponPaymentLattice( short.rate.lattice = short.rate.lattice,
                                                                      n = 2,    # technically expires at t=3
                                                                      forward.start = 1,
                                                                      K = 0.07) 
    swap.coupon.lattice
```

```
##      [,1]   [,2]     [,3]
## [1,]    0  0.005  0.02375
## [2,]   NA -0.016 -0.00250
## [3,]   NA     NA -0.02140
```

```r
    discounted.swap.coupon.lattice <- swap.coupon.lattice / (1 + short.rate.lattice[1:3,1:3])
    discounted.swap.coupon.lattice 
```

```
##      [,1]         [,2]        [,3]
## [1,]    0  0.004651163  0.02171429
## [2,]   NA -0.015180266 -0.00234192
## [3,]   NA           NA -0.02040816
```

```r
    sum( discounted.swap.coupon.lattice * elem.price.lattice[1:3,1:3], na.rm=T ) * 1e6
```

```
## [1] -5807.057
```


<br /><br />
## <a name="6.1"></a>Lesson 6.1: Black-Derman-Toy (BDT) Model Calibration


```r
    q <- 0.5
    n <- 13
    s <- c(7.3, 7.62, 8.1, 8.45, 9.2, 9.64, 10.12, 10.45, 10.75, 11.22, 11.55, 11.92, 12.2, 12.32) / 100
    a <- rep(5.0/100,n+1)       # starting values for a (to be calibrated)
    b <- rep(0.5/100,n+1)       # fixed b for all time periods

    o <- calibrateBDTShortRateLattice(a=a,
                                      b=b,
                                      s=s,
                                      q=q,
                                      n=n,     # num periods in calibrated BDT short-rate lattice
                                      optim.rounds=1)
    o
```

```
## $par
##  [1] 0.07299988 0.07921192 0.09020980 0.09435880 0.12130371 0.11719397
##  [7] 0.12849958 0.12566351 0.12918755 0.15192559 0.14538660 0.15636858
## [13] 0.15151140 0.13449896
## 
## $value
## [1] 7.680024e-08
## 
## $counts
## function gradient 
##      116      116 
## 
## $convergence
## [1] 1
## 
## $message
## [1] "NEW_X"
## 
## $bdt
##             [,1]       [,2]       [,3]       [,4]      [,5]      [,6]
##  [1,] 0.07299988 0.07960897 0.09111642 0.09578485 0.1237542 0.1201608
##  [2,]         NA 0.07921192 0.09066198 0.09530712 0.1231370 0.1195614
##  [3,]         NA         NA 0.09020980 0.09483177 0.1225228 0.1189651
##  [4,]         NA         NA         NA 0.09435880 0.1219117 0.1183718
##  [5,]         NA         NA         NA         NA 0.1213037 0.1177814
##  [6,]         NA         NA         NA         NA        NA 0.1171940
##  [7,]         NA         NA         NA         NA        NA        NA
##  [8,]         NA         NA         NA         NA        NA        NA
##  [9,]         NA         NA         NA         NA        NA        NA
## [10,]         NA         NA         NA         NA        NA        NA
## [11,]         NA         NA         NA         NA        NA        NA
## [12,]         NA         NA         NA         NA        NA        NA
## [13,]         NA         NA         NA         NA        NA        NA
## [14,]         NA         NA         NA         NA        NA        NA
##            [,7]      [,8]      [,9]     [,10]     [,11]     [,12]
##  [1,] 0.1324130 0.1301396 0.1344598 0.1589184 0.1528407 0.1652098
##  [2,] 0.1317526 0.1294905 0.1337892 0.1581258 0.1520784 0.1643858
##  [3,] 0.1310954 0.1288447 0.1331219 0.1573371 0.1513199 0.1635659
##  [4,] 0.1304416 0.1282021 0.1324579 0.1565524 0.1505652 0.1627501
##  [5,] 0.1297910 0.1275627 0.1317973 0.1557716 0.1498143 0.1619384
##  [6,] 0.1291437 0.1269265 0.1311400 0.1549947 0.1490671 0.1611307
##  [7,] 0.1284996 0.1262934 0.1304859 0.1542217 0.1483236 0.1603271
##  [8,]        NA 0.1256635 0.1298351 0.1534525 0.1475838 0.1595274
##  [9,]        NA        NA 0.1291875 0.1526871 0.1468478 0.1587318
## [10,]        NA        NA        NA 0.1519256 0.1461154 0.1579401
## [11,]        NA        NA        NA        NA 0.1453866 0.1571524
## [12,]        NA        NA        NA        NA        NA 0.1563686
## [13,]        NA        NA        NA        NA        NA        NA
## [14,]        NA        NA        NA        NA        NA        NA
##           [,13]     [,14]
##  [1,] 0.1608803 0.1435318
##  [2,] 0.1600779 0.1428159
##  [3,] 0.1592796 0.1421036
##  [4,] 0.1584851 0.1413949
##  [5,] 0.1576947 0.1406897
##  [6,] 0.1569082 0.1399880
##  [7,] 0.1561256 0.1392898
##  [8,] 0.1553469 0.1385951
##  [9,] 0.1545721 0.1379038
## [10,] 0.1538012 0.1372160
## [11,] 0.1530341 0.1365317
## [12,] 0.1522709 0.1358507
## [13,] 0.1515114 0.1351731
## [14,]        NA 0.1344990
```

<br /><br />
## <a name="6.2"></a>Lesson 6.2: Pricing using Calibrated BDT Model

### Pricing a forward-start swaption


```r
    forward.start.swap.lattice <- buildInterestRateSwapPriceLattice( short.rate.lattice=o$bdt,
                                                                     n=9,   # technically expires at t=10
                                                                     forward.start=2,
                                                                     K=11.65/100 )
    forward.start.swap.lattice 
```

```
##               [,1]          [,2]         [,3]       [,4]       [,5]
##  [1,] 0.0009495206  0.0023548143  0.003985117 0.03107983 0.05601786
##  [2,]           NA -0.0003171433  0.001099440 0.02838377 0.05352607
##  [3,]           NA            NA -0.001783970 0.02569051 0.05103758
##  [4,]           NA            NA           NA 0.02300009 0.04855244
##  [5,]           NA            NA           NA         NA 0.04607066
##  [6,]           NA            NA           NA         NA         NA
##  [7,]           NA            NA           NA         NA         NA
##  [8,]           NA            NA           NA         NA         NA
##  [9,]           NA            NA           NA         NA         NA
## [10,]           NA            NA           NA         NA         NA
##             [,6]       [,7]       [,8]       [,9]      [,10]
##  [1,] 0.05680504 0.06092997 0.05386226 0.04780411 0.03660172
##  [2,] 0.05458715 0.05901009 0.05230756 0.04666041 0.03594238
##  [3,] 0.05237310 0.05709434 0.05075695 0.04552029 0.03528543
##  [4,] 0.05016293 0.05518274 0.04921046 0.04438375 0.03463087
##  [5,] 0.04795667 0.05327530 0.04766807 0.04325077 0.03397869
##  [6,] 0.04575432 0.05137202 0.04612979 0.04212135 0.03332889
##  [7,]         NA 0.04947294 0.04459563 0.04099550 0.03268146
##  [8,]         NA         NA 0.04306559 0.03987322 0.03203640
##  [9,]         NA         NA         NA 0.03875449 0.03139371
## [10,]         NA         NA         NA         NA 0.03075337
```

```r
    swaption.price.lattice <- buildOptionPriceLattice( forward.start.swap.lattice,
                                                       K=0,
                                                       n=2,
                                                       short.rate.lattice=o$bdt )
    swaption.price.lattice
```

```
##             [,1]         [,2]        [,3]
## [1,] 0.001334663 0.0023548143 0.003985117
## [2,]          NA 0.0005093718 0.001099440
## [3,]          NA           NA 0.000000000
```

```r
    swaption.price.lattice[1,1] * 1e6
```

```
## [1] 1334.663
```
**Note**: answer is slightly off from lecture notes, due to diffs in calibration

### Now change b (volatility) to 0.01:


```r
    b <- rep(1.0/100,n+1)

    o <- calibrateBDTShortRateLattice(a=a,
                                      b=b,
                                      s=s,
                                      q=q,
                                      n=n,
                                      optim.rounds=1)
    o
```

```
## $par
##  [1] 0.07299996 0.07901313 0.08976069 0.09364855 0.12009703 0.11573303
##  [7] 0.12658456 0.12347876 0.12663376 0.14855930 0.14179059 0.15211842
## [13] 0.14706940 0.13019601
## 
## $value
## [1] 1.937905e-08
## 
## $counts
## function gradient 
##      115      115 
## 
## $convergence
## [1] 0
## 
## $message
## [1] "CONVERGENCE: REL_REDUCTION_OF_F <= FACTR*EPSMCH"
## 
## $bdt
##             [,1]       [,2]       [,3]       [,4]      [,5]      [,6]
##  [1,] 0.07299996 0.07980722 0.09157398 0.09650057 0.1249983 0.1216668
##  [2,]         NA 0.07901313 0.09066280 0.09554038 0.1237545 0.1204562
##  [3,]         NA         NA 0.08976069 0.09458973 0.1225232 0.1192576
##  [4,]         NA         NA         NA 0.09364855 0.1213040 0.1180710
##  [5,]         NA         NA         NA         NA 0.1200970 0.1168962
##  [6,]         NA         NA         NA         NA        NA 0.1157330
##  [7,]         NA         NA         NA         NA        NA        NA
##  [8,]         NA         NA         NA         NA        NA        NA
##  [9,]         NA         NA         NA         NA        NA        NA
## [10,]         NA         NA         NA         NA        NA        NA
## [11,]         NA         NA         NA         NA        NA        NA
## [12,]         NA         NA         NA         NA        NA        NA
## [13,]         NA         NA         NA         NA        NA        NA
## [14,]         NA         NA         NA         NA        NA        NA
##            [,7]      [,8]      [,9]     [,10]     [,11]     [,12]
##  [1,] 0.1344121 0.1324320 0.1371807 0.1625498 0.1567028 0.1698065
##  [2,] 0.1330747 0.1311143 0.1358157 0.1609324 0.1551436 0.1681169
##  [3,] 0.1317506 0.1298097 0.1344644 0.1593311 0.1535999 0.1664441
##  [4,] 0.1304396 0.1285180 0.1331264 0.1577457 0.1520716 0.1647879
##  [5,] 0.1291417 0.1272393 0.1318018 0.1561761 0.1505584 0.1631483
##  [6,] 0.1278568 0.1259732 0.1304903 0.1546221 0.1490603 0.1615249
##  [7,] 0.1265846 0.1247197 0.1291919 0.1530836 0.1475772 0.1599177
##  [8,]        NA 0.1234788 0.1279065 0.1515604 0.1461088 0.1583265
##  [9,]        NA        NA 0.1266338 0.1500523 0.1446549 0.1567511
## [10,]        NA        NA        NA 0.1485593 0.1432156 0.1551914
## [11,]        NA        NA        NA        NA 0.1417906 0.1536472
## [12,]        NA        NA        NA        NA        NA 0.1521184
## [13,]        NA        NA        NA        NA        NA        NA
## [14,]        NA        NA        NA        NA        NA        NA
##           [,13]     [,14]
##  [1,] 0.1658203 0.1482709
##  [2,] 0.1641703 0.1467956
##  [3,] 0.1625368 0.1453350
##  [4,] 0.1609196 0.1438888
##  [5,] 0.1593184 0.1424571
##  [6,] 0.1577331 0.1410397
##  [7,] 0.1561637 0.1396363
##  [8,] 0.1546098 0.1382469
##  [9,] 0.1530714 0.1368713
## [10,] 0.1515483 0.1355094
## [11,] 0.1500404 0.1341611
## [12,] 0.1485475 0.1328261
## [13,] 0.1470694 0.1315045
## [14,]        NA 0.1301960
```

```r
    forward.start.swap.lattice <- buildInterestRateSwapPriceLattice( short.rate.lattice=o$bdt,
                                                                     n=9,   # technically expires at t=10
                                                                     forward.start=2,
                                                                     K=11.65/100 )
    forward.start.swap.lattice 
```

```
##               [,1]         [,2]         [,3]       [,4]       [,5]
##  [1,] 0.0009533597  0.003694839  0.006876445 0.03513094 0.06101718
##  [2,]           NA -0.001648929  0.001102983 0.02973339 0.05602387
##  [3,]           NA           NA -0.004661416 0.02434696 0.05104364
##  [4,]           NA           NA           NA 0.01897199 0.04607677
##  [5,]           NA           NA           NA         NA 0.04112352
##  [6,]           NA           NA           NA         NA         NA
##  [7,]           NA           NA           NA         NA         NA
##  [8,]           NA           NA           NA         NA         NA
##  [9,]           NA           NA           NA         NA         NA
## [10,]           NA           NA           NA         NA         NA
##             [,6]       [,7]       [,8]       [,9]      [,10]
##  [1,] 0.06237145 0.06672334 0.05934663 0.05243030 0.03961101
##  [2,] 0.05792043 0.06286306 0.05621267 0.05011779 0.03827301
##  [3,] 0.05348467 0.05901918 0.05309504 0.04781958 0.03694464
##  [4,] 0.04906436 0.05519182 0.04999379 0.04553568 0.03562587
##  [5,] 0.04465971 0.05138108 0.04690897 0.04326606 0.03431666
##  [6,] 0.04027089 0.04758710 0.04384061 0.04101070 0.03301697
##  [7,]         NA 0.04380997 0.04078874 0.03876959 0.03172676
##  [8,]         NA         NA 0.03775340 0.03654271 0.03044599
##  [9,]         NA         NA         NA 0.03433005 0.02917463
## [10,]         NA         NA         NA         NA 0.02791262
```

```r
    swaption.price.lattice <- buildOptionPriceLattice( forward.start.swap.lattice,
                                                       K=0,
                                                       n=2,
                                                       short.rate.lattice=o$bdt )
    swaption.price.lattice
```

```
##             [,1]         [,2]        [,3]
## [1,] 0.001959901 0.0036948393 0.006876445
## [2,]          NA 0.0005111073 0.001102983
## [3,]          NA           NA 0.000000000
```

```r
    swaption.price.lattice[1,1] * 1e6
```

```
## [1] 1959.901
```

**Note**: answer is slightly off from lecture notes, due to diffs in calibration


<br /><br />
## <a name="6.3"></a>Lesson 6.3: Pricing Defaultable Bonds, Calibrating Hazard Rates


### Pricing a Defaultable Bond



```r
    delta = 1/2             # coupon payments every 6 mos.
    h <- rep(0.02,10+1)     # fixed hazard rate

    bond.1yr <- buildDefaultableBondSchedule( face.value = 100,
                                              hazard.rate.vec = h,
                                              r = 0.05,
                                              recovery.rate = 0.10,
                                              coupon.rate = 0.05,
                                              delta = delta,
                                              n = 1/delta )
    
    data.frame(bond.1yr)
```

```
##   hazard.rate survival.probability default.probability discount.rate
## 1        0.02               1.0000              0.0000     1.0000000
## 2        0.02               0.9800              0.0200     0.9756098
## 3        0.02               0.9604              0.0196     0.9518144
##   coupon.payment principal.payment recovery.payment all.payment
## 1       0.000000           0.00000        0.0000000     0.00000
## 2       4.780488           0.00000        0.1951220     4.97561
## 3       4.570613          91.41225        0.1865556    96.16942
```

```r
    bond.1yr.price <- sum(bond.1yr$all.payment)
    bond.1yr.price
```

```
## [1] 101.145
```

```r
    bond.5yr <- buildDefaultableBondSchedule( face.value = 100,
                                              hazard.rate.vec = h,
                                              r = 0.05,
                                              recovery.rate = 0.20,
                                              coupon.rate = 0.10,
                                              delta = delta,
                                              n = 5 / delta )
    data.frame(bond.5yr)
```

```
##    hazard.rate survival.probability default.probability discount.rate
## 1         0.02            1.0000000          0.00000000     1.0000000
## 2         0.02            0.9800000          0.02000000     0.9756098
## 3         0.02            0.9604000          0.01960000     0.9518144
## 4         0.02            0.9411920          0.01920800     0.9285994
## 5         0.02            0.9223682          0.01882384     0.9059506
## 6         0.02            0.9039208          0.01844736     0.8838543
## 7         0.02            0.8858424          0.01807842     0.8622969
## 8         0.02            0.8681255          0.01771685     0.8412652
## 9         0.02            0.8507630          0.01736251     0.8207466
## 10        0.02            0.8337478          0.01701526     0.8007284
## 11        0.02            0.8170728          0.01667496     0.7811984
##    coupon.payment principal.payment recovery.payment all.payment
## 1        0.000000            0.0000        0.0000000    0.000000
## 2        9.560976            0.0000        0.3902439    9.951220
## 3        9.141225            0.0000        0.3731112    9.514337
## 4        8.739903            0.0000        0.3567307    9.096634
## 5        8.356200            0.0000        0.3410694    8.697270
## 6        7.989343            0.0000        0.3260956    8.315438
## 7        7.638591            0.0000        0.3117792    7.950370
## 8        7.303238            0.0000        0.2980914    7.601330
## 9        6.982608            0.0000        0.2850044    7.267613
## 10       6.676055            0.0000        0.2724920    6.948547
## 11       6.382960           63.8296        0.2605290   70.473086
```

```r
    bond.5yr.price <- sum(bond.5yr$all.payment)
    bond.5yr.price
```

```
## [1] 145.8158
```


### Calibrating Hazard Rates


```r
    # these are just random values
    market.prices <- c(101.2179, 
                       92.5828, 
                       107.3675, 
                       104.0419, 
                       145.9155)

    #
    # compute 1-5 yr bond prices using the given hazard rates
    # 
    computeBondPrices <- function(h) {

        delta = 1/2     # coupon payments every 6 mos.

        bond.1yr <- computeDefaultableBondPricing( face.value = 100,
                                                   hazard.rate.vec = h,
                                                   r = 0.05,
                                                   recovery.rate = 0.10,
                                                   coupon.rate = 0.05,
                                                   delta = delta,
                                                   n = 1/delta )

        bond.2yr <- computeDefaultableBondPricing( face.value = 100,
                                                   hazard.rate.vec = h,
                                                   r = 0.05,
                                                   recovery.rate = 0.25,
                                                   coupon.rate = 0.02,
                                                   delta = delta,
                                                   n = 2 / delta )

        bond.3yr <- computeDefaultableBondPricing( face.value = 100,
                                                   hazard.rate.vec = h,
                                                   r = 0.05,
                                                   recovery.rate = 0.50,
                                                   coupon.rate = 0.05,
                                                   delta = delta,
                                                   n = 3 / delta )

        bond.4yr <- computeDefaultableBondPricing( face.value = 100,
                                                   hazard.rate.vec = h,
                                                   r = 0.05,
                                                   recovery.rate = 0.10,
                                                   coupon.rate = 0.05,
                                                   delta = delta,
                                                   n = 4 / delta )

        bond.5yr <- computeDefaultableBondPricing( face.value = 100,
                                                   hazard.rate.vec = h,
                                                   r = 0.05,
                                                   recovery.rate = 0.20,
                                                   coupon.rate = 0.10,
                                                   delta = delta,
                                                   n = 5 / delta )

        return( list(bond.1yr=bond.1yr, 
                     bond.2yr=bond.2yr, 
                     bond.3yr=bond.3yr, 
                     bond.4yr=bond.4yr, 
                     bond.5yr=bond.5yr) )
    }

    # 
    # define objective function (to be minimized)
    # 
    objectiveFn <- function(h) {

        bonds <- computeBondPrices(h)

        model.prices <- c(bonds$bond.1yr$price, 
                          bonds$bond.2yr$price, 
                          bonds$bond.3yr$price, 
                          bonds$bond.4yr$price, 
                          bonds$bond.5yr$price )

        sq.diffs <- (market.prices - model.prices)^2

        sum(sq.diffs)       # to minimize
    }

    o <- optim(par=h,
               fn=objectiveFn,
               method = "L-BFGS-B")
    o
```

```
## $par
##  [1] 0.01957532 0.01962483 0.02014341 0.02016492 0.02010088 0.02012203
##  [7] 0.02008368 0.02010715 0.01943048 0.01948716 0.02000000
## 
## $value
## [1] 2.994097e-11
## 
## $counts
## function gradient 
##       23       23 
## 
## $convergence
## [1] 0
## 
## $message
## [1] "CONVERGENCE: REL_REDUCTION_OF_F <= FACTR*EPSMCH"
```

```r
    bonds <- computeBondPrices(o$par)

    rbind(model.prices=c(bonds$bond.1yr$price, bonds$bond.2yr$price, bonds$bond.3yr$price, bonds$bond.4yr$price, bonds$bond.5yr$price), 
          market.prices)
```

```
##                   [,1]    [,2]     [,3]     [,4]     [,5]
## model.prices  101.2179 92.5828 107.3675 104.0419 145.9155
## market.prices 101.2179 92.5828 107.3675 104.0419 145.9155
```

### Calibration using R constrOptim():


```r
    # 
    # using a constraint: allow positive values only.
    # 
    # ui %*% theta - ci >= 0
    # kxp     px1    kx1
    # 
    ui <- matrix(1,nrow=1,ncol=length(h))
    ci <- c(0)
    ui %*% h - ci   
```

```
##      [,1]
## [1,] 0.22
```

```r
    o1 <- constrOptim( theta=h,
                       f=objectiveFn,
                       grad=NULL,
                       ui=ui,
                       ci=ci)
    o1
```

```
## $par
##  [1] 0.01952914 0.01967337 0.02010897 0.02019854 0.01995501 0.02028295
##  [7] 0.02007349 0.02011269 0.01898095 0.01999905 0.02161238
## 
## $value
## [1] 2.202676e-13
## 
## $counts
## function gradient 
##      864       NA 
## 
## $convergence
## [1] 0
## 
## $message
## NULL
## 
## $outer.iterations
## [1] 3
## 
## $barrier.value
## [1] 5.538853e-05
```

```r
    bonds <- computeBondPrices(o1$par)

    rbind(model.prices=c(bonds$bond.1yr$price, bonds$bond.2yr$price, bonds$bond.3yr$price, bonds$bond.4yr$price, bonds$bond.5yr$price), 
          market.prices)
```

```
##                   [,1]    [,2]     [,3]     [,4]     [,5]
## model.prices  101.2179 92.5828 107.3675 104.0419 145.9155
## market.prices 101.2179 92.5828 107.3675 104.0419 145.9155
```

### Calibration using R constrOptim(): Non-decreasing hazard rates


```r
    # 
    # using a constraint: pair-wise compare hazard rates
    # to ensure non-decreasing
    # 
    # ui %*% theta - ci >= 0
    # kxp     px1    kx1
    # 
    ui <- matrix(c(
                   -1,  1,  0,  0,  0,  0,  0,  0,  0,  0,  0,
                    0, -1,  1,  0,  0,  0,  0,  0,  0,  0,  0,
                    0,  0, -1,  1,  0,  0,  0,  0,  0,  0,  0,
                    0,  0,  0, -1,  1,  0,  0,  0,  0,  0,  0,
                    0,  0,  0,  0, -1,  1,  0,  0,  0,  0,  0,
                    0,  0,  0,  0,  0, -1,  1,  0,  0,  0,  0,
                    0,  0,  0,  0,  0,  0, -1,  1,  0,  0,  0,
                    0,  0,  0,  0,  0,  0,  0, -1,  1,  0,  0,
                    0,  0,  0,  0,  0,  0,  0,  0, -1,  1,  0,
                    0,  0,  0,  0,  0,  0,  0,  0,  0, -1,  1 ),
                 nrow = 10,
                 ncol = 11,
                 byrow=T)
    ci <- rep(0,10)
    h1 <- h + cumsum(rep(0.0001,length(h))) - 0.0005
    ui %*% h1 - ci   
```

```
##        [,1]
##  [1,] 1e-04
##  [2,] 1e-04
##  [3,] 1e-04
##  [4,] 1e-04
##  [5,] 1e-04
##  [6,] 1e-04
##  [7,] 1e-04
##  [8,] 1e-04
##  [9,] 1e-04
## [10,] 1e-04
```

```r
    o2 <- constrOptim( theta=h1,
                       f=objectiveFn,
                       grad=NULL,
                       ui=ui,
                       ci=ci)
    o2
```

```
## $par
##  [1] 0.01953676 0.01986096 0.01990686 0.01997758 0.02003733 0.02007377
##  [7] 0.02007377 0.02007377 0.02007377 0.02007379 0.02107375
## 
## $value
## [1] 0.003323694
## 
## $counts
## function gradient 
##     5518       NA 
## 
## $convergence
## [1] 1
## 
## $message
## NULL
## 
## $outer.iterations
## [1] 12
## 
## $barrier.value
## [1] 1.313746e-06
```

```r
    bonds <- computeBondPrices(o2$par)

    rbind(model.prices=c(bonds$bond.1yr$price, bonds$bond.2yr$price, bonds$bond.3yr$price, bonds$bond.4yr$price, bonds$bond.5yr$price), 
          market.prices)
```

```
##                   [,1]     [,2]     [,3]     [,4]     [,5]
## model.prices  101.2006 92.59752 107.3832 104.0717 145.8746
## market.prices 101.2179 92.58280 107.3675 104.0419 145.9155
```

<br /><br />
## <a name="6.5"></a>Lesson 6.5: Pricing Credit Default Swaps


```r
    # copied from spreadsheet
    h <- c(0.010070286520099,
           0.010070286520099,
           0.010070286520099,
           0.010070286520099,
           0.009721551669331,
           0.009721551669331,
           0.009721551669331,
           0.009721551669331,
           0.009721551669331)      # for time periods t=0 thru n

    cds <- buildCDSSchedule( N = 1e6,                 # principal
                             hazard.rate.vec = h,
                             n = 8,
                             delta = 1/4,
                             r = 0.01,
                             recovery.rate = 0.45 )
    data.frame(cds)
```

```
##   hazard.rate.vec survival.probability default.probability discount.rate
## 1     0.010070287            1.0000000         0.000000000     1.0000000
## 2     0.010070287            0.9899297         0.010070287     0.9975062
## 3     0.010070287            0.9799608         0.009968876     0.9950187
## 4     0.010070287            0.9700924         0.009868486     0.9925373
## 5     0.009721552            0.9603232         0.009769108     0.9900622
## 6     0.009721552            0.9509874         0.009335832     0.9875932
## 7     0.009721552            0.9417423         0.009245073     0.9851304
## 8     0.009721552            0.9325871         0.009155197     0.9826737
## 9     0.009721552            0.9235209         0.009066194     0.9802231
##        S.par premium.payment accrued.interest protection.payment
## 1 0.02188895           0.000          0.00000              0.000
## 2 0.02188895        5403.622         27.48479           5524.845
## 3 0.02188895        5335.867         27.14016           5455.570
## 4 0.02188895        5268.961         26.79985           5387.163
## 5 0.02188895        5202.893         26.46381           5319.613
## 6 0.02188895        5139.465         25.22703           5071.002
## 7 0.02188895        5076.809         24.91949           5009.181
## 8 0.02188895        5014.917         24.61569           4948.114
## 9 0.02188895        4953.780         24.31560           4887.791
##   premium.leg protection.leg
## 1       0.000          0.000
## 2    5431.107       5524.845
## 3   10794.114      10980.415
## 4   16089.875      16367.578
## 5   21319.232      21687.191
## 6   26483.923      26758.194
## 7   31585.652      31767.375
## 8   36625.185      36715.489
## 9   41603.280      41603.280
```



<br /><br />
## <a name="7.1"></a>Lesson 7.1: Mortgage mathematics



```r
    mort <- buildMortgageSchedule(mortgage.balance = 20000,
                                  monthly.mortgage.rate = 0.05/12,
                                  n = 18)

    data.frame(mort)
```

```
##    begin.balance monthly.payment mortgage.interest principal.payment
## 1      20000.000        1155.611         83.333333          1072.277
## 2      18927.723        1155.611         78.865511          1076.745
## 3      17850.977        1155.611         74.379073          1081.232
## 4      16769.746        1155.611         69.873941          1085.737
## 5      15684.009        1155.611         65.350038          1090.261
## 6      14593.748        1155.611         60.807285          1094.803
## 7      13498.945        1155.611         56.245604          1099.365
## 8      12399.580        1155.611         51.664916          1103.946
## 9      11295.634        1155.611         47.065142          1108.546
## 10     10187.089        1155.611         42.446203          1113.164
## 11      9073.924        1155.611         37.808017          1117.803
## 12      7956.121        1155.611         33.150506          1122.460
## 13      6833.661        1155.611         28.473589          1127.137
## 14      5706.524        1155.611         23.777184          1131.834
## 15      4574.691        1155.611         19.061211          1136.549
## 16      3438.141        1155.611         14.325588          1141.285
## 17      2296.856        1155.611          9.570234          1146.040
## 18      1150.816        1155.611          4.795065          1150.816
##    end.balance
## 1    18927.723
## 2    17850.977
## 3    16769.746
## 4    15684.009
## 5    14593.748
## 6    13498.945
## 7    12399.580
## 8    11295.634
## 9    10187.089
## 10    9073.924
## 11    7956.121
## 12    6833.661
## 13    5706.524
## 14    4574.691
## 15    3438.141
## 16    2296.856
## 17    1150.816
## 18       0.000
```

```r
    computeMortgagePresentValue(monthly.payment = mort$monthly.payment[1],
                                monthly.market.rate = 0.05/12,
                                n = 18)
```

```
## [1] 20000
```

<br /><br />
## <a name="7.2"></a>Lesson 7.2: MBS Pass-Thru Mathematics


```r
    cpr.rate <- buildCPRSchedule( psa.multiplier = 100,
                                  seasoning = 0,
                                  n = 30 )

    mbs <- buildMBSPassThruSchedule( mortgage.balance = 400,
                                     monthly.mortgage.rate = 8.125/100/12,
                                     monthly.passthru.rate = 7.500/100/12,
                                     cpr.rate = cpr.rate[4:20],
                                     n = 17) 
    data.frame(mbs)
```

```
##    cpr.rate     smm.rate begin.balance monthly.payment mortgage.interest
## 1     0.008 0.0006691237     400.00000        24.98903         2.7083333
## 2     0.010 0.0008371774     377.46656        24.97231         2.5557632
## 3     0.012 0.0010055425     354.75277        24.95140         2.4019719
## 4     0.014 0.0011742204     331.86930        24.92631         2.2470317
## 5     0.016 0.0013432122     308.82696        24.89705         2.0910159
## 6     0.018 0.0015125192     285.63674        24.86360         1.9339988
## 7     0.020 0.0016821426     262.30979        24.82600         1.7760559
## 8     0.022 0.0018520835     238.85738        24.78424         1.6172635
## 9     0.024 0.0020223433     215.29093        24.73833         1.4576990
## 10    0.026 0.0021929233     191.62199        24.68830         1.2974405
## 11    0.028 0.0023638246     167.86220        24.63416         1.1365670
## 12    0.030 0.0025350486     144.02335        24.57593         0.9751581
## 13    0.032 0.0027065965     120.11730        24.51363         0.8132942
## 14    0.034 0.0028784696      96.15600        24.44728         0.6510563
## 15    0.036 0.0030506693      72.15149        24.37691         0.4885257
## 16    0.038 0.0032231967      48.11587        24.30255         0.3257845
## 17    0.040 0.0033960532      24.06130        24.22422         0.1629151
##    passthru.interest principal.payment principal.prepayment
## 1          2.5000000          22.28070         2.527409e-01
## 2          2.3591660          22.41655         2.972398e-01
## 3          2.2172048          22.54943         3.340446e-01
## 4          2.0741831          22.67928         3.630572e-01
## 5          1.9301685          22.80603         3.841868e-01
## 6          1.7852296          22.92960         3.973496e-01
## 7          1.6394362          23.04994         4.024692e-01
## 8          1.4928586          23.16697         3.994766e-01
## 9          1.3455683          23.28063         3.883107e-01
## 10         1.1976374          23.39086         3.689179e-01
## 11         1.0491388          23.49760         3.412526e-01
## 12         0.9001460          23.60078         3.052771e-01
## 13         0.7507331          23.70034         2.609618e-01
## 14         0.6009750          23.79623         2.082854e-01
## 15         0.4509468          23.88839         1.472348e-01
## 16         0.3007242          23.97676         7.780508e-02
## 17         0.1503831          24.06130         7.118471e-16
##    total.principal  end.balance
## 1         22.53344 3.774666e+02
## 2         22.71379 3.547528e+02
## 3         22.88348 3.318693e+02
## 4         23.04234 3.088270e+02
## 5         23.19022 2.856367e+02
## 6         23.32695 2.623098e+02
## 7         23.45241 2.388574e+02
## 8         23.56645 2.152909e+02
## 9         23.66894 1.916220e+02
## 10        23.75978 1.678622e+02
## 11        23.83885 1.440234e+02
## 12        23.90605 1.201173e+02
## 13        23.96130 9.615600e+01
## 14        24.00451 7.215149e+01
## 15        24.03562 4.811587e+01
## 16        24.05457 2.406130e+01
## 17        24.06130 2.096101e-13
```

```r
    avg.life <- 1 / 12 / 400 * sum( 1:17 * mbs$total.principal )
    avg.life
```

```
## [1] 0.75815
```

<br /><br />
## <a name="7.4"></a>Lesson 7.4: MBS Pass-Thru Sequential CMO with Tranches 



```r
    tranches <- buildMBSTrancheSchedule( mbs=mbs,
                                         tranche.begin.balances = c(194.5, 36, 96.5, 73),
                                         n=17 )

    data.frame(tranches[1])
```

```
##    begin.balance total.principal passthru.interest end.balance
## 1      194.50000        22.53344        1.21562500   171.96656
## 2      171.96656        22.71379        1.07479101   149.25277
## 3      149.25277        22.88348        0.93282984   126.36930
## 4      126.36930        23.04234        0.78980811   103.32696
## 5      103.32696        23.19022        0.64579349    80.13674
## 6       80.13674        23.32695        0.50085464    56.80979
## 7       56.80979        23.45241        0.35506118    33.35738
## 8       33.35738        23.56645        0.20848362     9.79093
## 9        9.79093         9.79093        0.06119331     0.00000
## 10       0.00000         0.00000        0.00000000     0.00000
## 11       0.00000         0.00000        0.00000000     0.00000
## 12       0.00000         0.00000        0.00000000     0.00000
## 13       0.00000         0.00000        0.00000000     0.00000
## 14       0.00000         0.00000        0.00000000     0.00000
## 15       0.00000         0.00000        0.00000000     0.00000
## 16       0.00000         0.00000        0.00000000     0.00000
## 17       0.00000         0.00000        0.00000000     0.00000
```

```r
    data.frame(tranches[2])
```

```
##    begin.balance total.principal passthru.interest end.balance
## 1       36.00000         0.00000         0.2250000    36.00000
## 2       36.00000         0.00000         0.2250000    36.00000
## 3       36.00000         0.00000         0.2250000    36.00000
## 4       36.00000         0.00000         0.2250000    36.00000
## 5       36.00000         0.00000         0.2250000    36.00000
## 6       36.00000         0.00000         0.2250000    36.00000
## 7       36.00000         0.00000         0.2250000    36.00000
## 8       36.00000         0.00000         0.2250000    36.00000
## 9       36.00000        13.87801         0.2250000    22.12199
## 10      22.12199        22.12199         0.1382624     0.00000
## 11       0.00000         0.00000         0.0000000     0.00000
## 12       0.00000         0.00000         0.0000000     0.00000
## 13       0.00000         0.00000         0.0000000     0.00000
## 14       0.00000         0.00000         0.0000000     0.00000
## 15       0.00000         0.00000         0.0000000     0.00000
## 16       0.00000         0.00000         0.0000000     0.00000
## 17       0.00000         0.00000         0.0000000     0.00000
```

```r
    data.frame(tranches[3])
```

```
##    begin.balance total.principal passthru.interest end.balance
## 1       96.50000        0.000000         0.6031250    96.50000
## 2       96.50000        0.000000         0.6031250    96.50000
## 3       96.50000        0.000000         0.6031250    96.50000
## 4       96.50000        0.000000         0.6031250    96.50000
## 5       96.50000        0.000000         0.6031250    96.50000
## 6       96.50000        0.000000         0.6031250    96.50000
## 7       96.50000        0.000000         0.6031250    96.50000
## 8       96.50000        0.000000         0.6031250    96.50000
## 9       96.50000        0.000000         0.6031250    96.50000
## 10      96.50000        1.637796         0.6031250    94.86220
## 11      94.86220       23.838850         0.5928888    71.02335
## 12      71.02335       23.906052         0.4438960    47.11730
## 13      47.11730       23.961300         0.2944831    23.15600
## 14      23.15600       23.156002         0.1447250     0.00000
## 15       0.00000        0.000000         0.0000000     0.00000
## 16       0.00000        0.000000         0.0000000     0.00000
## 17       0.00000        0.000000         0.0000000     0.00000
```

```r
    data.frame(tranches[4])
```

```
##    begin.balance total.principal passthru.interest  end.balance
## 1       73.00000       0.0000000         0.4562500 7.300000e+01
## 2       73.00000       0.0000000         0.4562500 7.300000e+01
## 3       73.00000       0.0000000         0.4562500 7.300000e+01
## 4       73.00000       0.0000000         0.4562500 7.300000e+01
## 5       73.00000       0.0000000         0.4562500 7.300000e+01
## 6       73.00000       0.0000000         0.4562500 7.300000e+01
## 7       73.00000       0.0000000         0.4562500 7.300000e+01
## 8       73.00000       0.0000000         0.4562500 7.300000e+01
## 9       73.00000       0.0000000         0.4562500 7.300000e+01
## 10      73.00000       0.0000000         0.4562500 7.300000e+01
## 11      73.00000       0.0000000         0.4562500 7.300000e+01
## 12      73.00000       0.0000000         0.4562500 7.300000e+01
## 13      73.00000       0.0000000         0.4562500 7.300000e+01
## 14      73.00000       0.8485105         0.4562500 7.215149e+01
## 15      72.15149      24.0356219         0.4509468 4.811587e+01
## 16      48.11587      24.0545675         0.3007242 2.406130e+01
## 17      24.06130      24.0613000         0.1503831 3.232969e-13
```
