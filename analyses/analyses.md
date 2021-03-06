Data Visualization Experiment
================
Reid McIlroy-Young & Erin M. Ochoa
2018/05/04

Results
=======

``` r
path <- '../data/'
phr.f <- 'pizza_hom_rain_final.csv'
hrp.f <- 'hom_rain_pizza_final.csv'
rph.f <- 'rain_pizza_hom_final.csv'

phr.names <- c('ResponseId','Duration..in.seconds.','Q6','Q10','Q9')
hrp.names <- c('ResponseId','Duration..in.seconds.','Q10','Q9','Q6')
rph.names <- c('ResponseId','Duration..in.seconds.','Q9','Q6','Q10')

phr <- read.csv(paste(path,phr.f,sep=''))
hrp <- read.csv(paste(path,hrp.f,sep=''))
rph <- read.csv(paste(path,rph.f,sep=''))

phr <- phr[(nrow(phr) - 44):nrow(phr),phr.names]
hrp <- hrp[3:nrow(hrp), hrp.names]
rph <- rph[3:nrow(rph), rph.names]

names(phr) <- c('rID','duration','pizza.1','homicide.2','rain.3')
names(hrp) <- c('rID','duration','homicide.1','rain.2','pizza.3')
names(rph) <- c('rID','duration','rain.1','pizza.2','homicide.3')

phr$duration <- as.numeric(phr$duration)
hrp$duration <- as.numeric(hrp$duration)
rph$duration <- as.numeric(rph$duration)

phr$pizza.1 <- as.character(phr$pizza.1)
phr$homicide.2 <- as.character(phr$homicide.2)
phr$rain.3 <- as.character(phr$rain.3)

hrp$homicide.1 <- as.character(hrp$homicide.1)
hrp$rain.2 <- as.character(hrp$rain.2)
hrp$pizza.3 <- as.character(hrp$pizza.3)

rph$rain.1 <- as.character(rph$rain.1)
rph$pizza.2 <- as.character(rph$pizza.2)
rph$homicide.3 <- as.character(rph$homicide.3)
```

``` r
ggplot(phr) + geom_histogram(aes(duration),binwidth = 5)
```

![](analyses_files/figure-markdown_github/duration-1.png)

``` r
ggplot(hrp) + geom_histogram(aes(duration),binwidth = 5)
```

![](analyses_files/figure-markdown_github/duration-2.png)

``` r
ggplot(rph) + geom_histogram(aes(duration),binwidth = 5)
```

![](analyses_files/figure-markdown_github/duration-3.png)

``` r
# drop people guessing randomly
phr <- phr[phr$duration >= 12, ]
hrp <- hrp[hrp$duration >= 12, ]
rph <- rph[rph$duration >= 12, ]
```

``` r
# Make some bar charts
max.y <- 42

ggplot(phr) + geom_bar(aes(pizza.1)) + scale_y_continuous(limits = c(0,max.y))
```

![](analyses_files/figure-markdown_github/basic%20viz-1.png)

``` r
ggplot(phr) + geom_bar(aes(homicide.2)) + scale_y_continuous(limits = c(0,max.y))
```

![](analyses_files/figure-markdown_github/basic%20viz-2.png)

``` r
ggplot(phr) + geom_bar(aes(rain.3)) + scale_y_continuous(limits = c(0,max.y))
```

![](analyses_files/figure-markdown_github/basic%20viz-3.png)

``` r
ggplot(hrp) + geom_bar(aes(homicide.1)) + scale_y_continuous(limits = c(0,max.y))
```

![](analyses_files/figure-markdown_github/basic%20viz-4.png)

``` r
ggplot(hrp) + geom_bar(aes(rain.2)) + scale_y_continuous(limits = c(0,max.y))
```

![](analyses_files/figure-markdown_github/basic%20viz-5.png)

``` r
ggplot(hrp) + geom_bar(aes(pizza.3)) + scale_y_continuous(limits = c(0,42))
```

![](analyses_files/figure-markdown_github/basic%20viz-6.png)

``` r
ggplot(rph) + geom_bar(aes(rain.1)) + scale_y_continuous(limits = c(0,max.y))
```

![](analyses_files/figure-markdown_github/basic%20viz-7.png)

``` r
ggplot(rph) + geom_bar(aes(pizza.2)) + scale_y_continuous(limits = c(0,max.y))
```

![](analyses_files/figure-markdown_github/basic%20viz-8.png)

``` r
ggplot(rph) + geom_bar(aes(homicide.3)) + scale_y_continuous(limits = c(0,max.y))
```

![](analyses_files/figure-markdown_github/basic%20viz-9.png)

``` r
right.wrong <- function(df,var,right){
  varname <- paste(var,'right',sep='.')
  new_col <- as.data.frame(df[[var]] == right)
  names(new_col) <- varname
  n.sure <- 'Not Sure'
  c.state <- 'Correct State'
  i.state <- 'Incorrect State'
  df[[var]][df[[var]] == 'I am not sure'] <- n.sure
  df[[var]][df[[var]] == right] <- c.state
  df[[var]][df[[var]] != c.state & df[[var]] != n.sure] <- i.state
  
  df[[var]] <- factor(df[[var]],levels=c(n.sure,i.state,c.state))
  
  new.df <- cbind(df,new_col)
  
  new.df
}
reds <- c('black','#FC9272','#DE2D26')
greens <- c('black','#A1D99B','#31A354')
blues <- c('black','#9ECAE1','#3182BD')
```

``` r
phr <- right.wrong(phr,'pizza.1','North Dakota')
phr <- right.wrong(phr,'homicide.2','Utah')
phr <- right.wrong(phr,'rain.3','Virginia')

hrp <- right.wrong(hrp,'homicide.1','Utah')
hrp <- right.wrong(hrp,'rain.2','Virginia')
hrp <- right.wrong(hrp,'pizza.3','North Dakota')

rph <- right.wrong(rph,'rain.1','Virginia')
rph <- right.wrong(rph,'pizza.2','North Dakota')
rph <- right.wrong(rph,'homicide.3','Utah')
```

``` r
ggplot(phr) + geom_bar(aes(x=pizza.1.right,y= (..count..)/sum(..count..),fill = pizza.1, alpha=pizza.1)) + 
       scale_x_discrete(labels=c('Incorrect','Correct')) +
       scale_alpha_manual(values=c(.4,1,1)) + 
       theme(legend.position='bottom',legend.title=element_blank(),
            plot.title=element_text(hjust=0.5),
            plot.subtitle = element_text(hjust = 0.5),
            axis.title.x=element_blank(),
            panel.background=element_blank(),
            panel.grid.major.y=element_line(colour='grey50',size=.25),
            panel.grid.major.x=element_blank(),
            axis.ticks=element_blank()) +
       labs(y='Proportion of Responses',
           title='Pizza Consumption when Asked First') +
       scale_fill_manual(values=reds) + 
       scale_y_continuous(limits=c(0,1))
```

![](analyses_files/figure-markdown_github/vis_numbers_phr-1.png)

``` r
ggplot(phr) + geom_bar(aes(x=homicide.2.right,y= (..count..)/sum(..count..),fill = homicide.2, alpha=homicide.2)) + 
       scale_x_discrete(labels=c('Incorrect','Correct')) +
       scale_alpha_manual(values=c(.4,1,1)) + 
       theme(legend.position='bottom',legend.title=element_blank(),
            plot.title=element_text(hjust=0.5),
            plot.subtitle = element_text(hjust = 0.5),
            axis.title.x=element_blank(),
            panel.background=element_blank(),
            panel.grid.major.y=element_line(colour='grey50',size=.25),
            panel.grid.major.x=element_blank(),
            axis.ticks=element_blank()) +
       labs(y='Proportion of Responses',
           title='Homicide Rate when Asked Second') +
       scale_fill_manual(values=greens) + 
       scale_y_continuous(limits=c(0,1))
```

![](analyses_files/figure-markdown_github/vis_numbers_phr-2.png)

``` r
ggplot(phr) + geom_bar(aes(x=factor(rain.3.right),y= (..count..)/sum(..count..),fill = rain.3, alpha=rain.3)) + 
       scale_x_discrete(labels=c('Incorrect','Correct')) +
       scale_alpha_manual(values=c(.4,1,1)) + 
       theme(legend.position='bottom',legend.title=element_blank(),
            plot.title=element_text(hjust=0.5),
            plot.subtitle = element_text(hjust = 0.5),
            axis.title.x=element_blank(),
            panel.background=element_blank(),
            panel.grid.major.y=element_line(colour='grey50',size=.25),
            panel.grid.major.x=element_blank(),
            axis.ticks=element_blank()) +
       labs(y='Proportion of Responses',
           title='Rainfall when Asked Third') +
       scale_fill_manual(values=blues) + 
       scale_y_continuous(limits=c(0,1))
```

![](analyses_files/figure-markdown_github/vis_numbers_phr-3.png)

``` r
ggplot(hrp) + geom_bar(aes(x=homicide.1.right,y= (..count..)/sum(..count..),fill = homicide.1, alpha=homicide.1)) + 
       scale_x_discrete(labels=c('Incorrect','Correct')) +
       scale_alpha_manual(values=c(.4,1,1)) + 
       theme(legend.position='bottom',legend.title=element_blank(),
            plot.title=element_text(hjust=0.5),
            plot.subtitle = element_text(hjust = 0.5),
            axis.title.x=element_blank(),
            panel.background=element_blank(),
            panel.grid.major.y=element_line(colour='grey50',size=.25),
            panel.grid.major.x=element_blank(),
            axis.ticks=element_blank()) +
       labs(y='Proportion of Responses',
           title='Homicide when Asked First') +
       scale_fill_manual(values=greens) + 
       scale_y_continuous(limits=c(0,1))
```

![](analyses_files/figure-markdown_github/vis_numbers_hrp-1.png)

``` r
ggplot(hrp) + geom_bar(aes(x=rain.2.right,y= (..count..)/sum(..count..),fill = rain.2, alpha=rain.2)) + 
       scale_x_discrete(labels=c('Incorrect','Correct')) +
       scale_alpha_manual(values=c(.4,1,1)) + 
       theme(legend.position='bottom',legend.title=element_blank(),
            plot.title=element_text(hjust=0.5),
            plot.subtitle = element_text(hjust = 0.5),
            axis.title.x=element_blank(),
            panel.background=element_blank(),
            panel.grid.major.y=element_line(colour='grey50',size=.25),
            panel.grid.major.x=element_blank(),
            axis.ticks=element_blank()) +
       labs(y='Proportion of Responses',
           title='Rainfall when Asked Second') +
       scale_fill_manual(values=blues) + 
       scale_y_continuous(limits=c(0,1))
```

![](analyses_files/figure-markdown_github/vis_numbers_hrp-2.png)

``` r
ggplot(hrp) + geom_bar(aes(x=factor(pizza.3.right),y= (..count..)/sum(..count..),fill = pizza.3, alpha=pizza.3)) + 
       scale_x_discrete(labels=c('Incorrect','Correct')) +
       scale_alpha_manual(values=c(.4,1,1)) + 
       theme(legend.position='bottom',legend.title=element_blank(),
            plot.title=element_text(hjust=0.5),
            plot.subtitle = element_text(hjust = 0.5),
            axis.title.x=element_blank(),
            panel.background=element_blank(),
            panel.grid.major.y=element_line(colour='grey50',size=.25),
            panel.grid.major.x=element_blank(),
            axis.ticks=element_blank()) +
       labs(y='Proportion of Responses',
           title='Pizza Consumption when Asked Third') +
       scale_fill_manual(values=reds) + 
       scale_y_continuous(limits=c(0,1))
```

![](analyses_files/figure-markdown_github/vis_numbers_hrp-3.png)

``` r
ggplot(rph) + geom_bar(aes(x=rain.1.right,y= (..count..)/sum(..count..),fill = rain.1, alpha=rain.1)) + 
       scale_x_discrete(labels=c('Incorrect','Correct')) +
       scale_alpha_manual(values=c(.4,1,1)) + 
       theme(legend.position='bottom',legend.title=element_blank(),
            plot.title=element_text(hjust=0.5),
            plot.subtitle = element_text(hjust = 0.5),
            axis.title.x=element_blank(),
            panel.background=element_blank(),
            panel.grid.major.y=element_line(colour='grey50',size=.25),
            panel.grid.major.x=element_blank(),
            axis.ticks=element_blank()) +
       labs(y='Proportion of Responses',
           title='Rainfall when Asked First') +
       scale_fill_manual(values=blues) + 
       scale_y_continuous(limits=c(0,1))
```

![](analyses_files/figure-markdown_github/vis_numbers_rph-1.png)

``` r
ggplot(rph) + geom_bar(aes(x=pizza.2.right,y= (..count..)/sum(..count..),fill = pizza.2, alpha=pizza.2)) + 
       scale_x_discrete(labels=c('Incorrect','Correct')) +
       scale_alpha_manual(values=c(.4,1,1)) + 
       theme(legend.position='bottom',legend.title=element_blank(),
            plot.title=element_text(hjust=0.5),
            plot.subtitle = element_text(hjust = 0.5),
            axis.title.x=element_blank(),
            panel.background=element_blank(),
            panel.grid.major.y=element_line(colour='grey50',size=.25),
            panel.grid.major.x=element_blank(),
            axis.ticks=element_blank()) +
       labs(y='Proportion of Responses',
           title='Pizza Consumption when Asked Second') +
       scale_fill_manual(values=reds) + 
       scale_y_continuous(limits=c(0,1))
```

![](analyses_files/figure-markdown_github/vis_numbers_rph-2.png)

``` r
ggplot(rph) + geom_bar(aes(x=factor(homicide.3.right),y= (..count..)/sum(..count..),fill = homicide.3, alpha=homicide.3)) + 
       scale_x_discrete(labels=c('Incorrect','Correct')) +
       scale_alpha_manual(values=c(.4,1,1)) + 
       theme(legend.position='bottom',legend.title=element_blank(),
            plot.title=element_text(hjust=0.5),
            plot.subtitle = element_text(hjust = 0.5),
            axis.title.x=element_blank(),
            panel.background=element_blank(),
            panel.grid.major.y=element_line(colour='grey50',size=.25),
            panel.grid.major.x=element_blank(),
            axis.ticks=element_blank()) +
       labs(y='Proportion of Responses',
           title='Homicide Rate when Asked Third') +
       scale_fill_manual(values=greens) + 
       scale_y_continuous(limits=c(0,1))
```

![](analyses_files/figure-markdown_github/vis_numbers_rph-3.png)

``` r
results <- as.data.frame(as.matrix(summary(phr$pizza.1.right))[2:3, ])
names(results) <- 'pizza.1'

results <- cbind(results,as.data.frame(as.matrix(summary(phr$homicide.2.right))[2:3, ])[, 1])
results <- cbind(results,as.data.frame(as.matrix(summary(phr$rain.3.right))[2:3, ])[, 1])

results <- cbind(results,as.data.frame(as.matrix(summary(hrp$homicide.1.right))[2:3, ])[, 1])
results <- cbind(results,as.data.frame(as.matrix(summary(hrp$rain.2.right))[2:3, ])[, 1])
results <- cbind(results,as.data.frame(as.matrix(summary(hrp$pizza.3.right))[2:3, ])[, 1])

results <- cbind(results,as.data.frame(as.matrix(summary(rph$rain.1.right))[2:3, ])[, 1])
results <- cbind(results,as.data.frame(as.matrix(summary(rph$pizza.2.right))[2:3, ])[, 1])
results <- cbind(results,as.data.frame(as.matrix(summary(rph$homicide.3.right))[2:3, ])[, 1])

names(results) <- c('pizza.1','homicide.2','rain.3','homicide.1','rain.2','pizza.3','rain.1','pizza.2','homicide.3')
#Names are correct, but there are column headers with clumsy descriptions that I can't seem to get rid of
```

``` r
idx <- sapply(results, is.factor)
results[idx] <- lapply(results[idx], function(x) as.numeric(as.character(x)))
pizza.df <- cbind.data.frame(results$pizza.1,results$pizza.2,results$pizza.3)
rain.df <- cbind.data.frame(results$rain.1,results$rain.2,results$rain.3)
homicide.df <- cbind.data.frame(results$homicide.1,results$homicide.2,results$homicide.3)
fisher.test(pizza.df)
```

    ## 
    ##  Fisher's Exact Test for Count Data
    ## 
    ## data:  pizza.df
    ## p-value = 0.0002174
    ## alternative hypothesis: two.sided

``` r
fisher.test(rain.df)
```

    ## 
    ##  Fisher's Exact Test for Count Data
    ## 
    ## data:  rain.df
    ## p-value = 0.005158
    ## alternative hypothesis: two.sided

``` r
fisher.test(homicide.df)
```

    ## 
    ##  Fisher's Exact Test for Count Data
    ## 
    ## data:  homicide.df
    ## p-value = 0.005765
    ## alternative hypothesis: two.sided
