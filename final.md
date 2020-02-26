final
================
xj
2020/2/26

# data input & tidy

``` r
train_df <- 
    read_csv("pml-training.csv") %>% 
    select_if(~sum(is.na(.)) < 100) %>% 
    select(-1) %>% 
    mutate(classe = as.factor(classe) )
# train_df[, colSums(is.na(train_df)) < 100]

test_df <- read_csv("pml-testing.csv")


nzv <- nearZeroVar(train_df)
train_new <- train_df[,-nzv]
```

Since there are “NA” columns, so I remove them and then use
`nearZeroVar()` to remove some variable that has or almost has no
variance.

# Train model with decision tree

``` r
trControl <- trainControl(method = "repeatedcv", number = 10,repeats = 5)
set.seed(10086)
# fit decision tree model
tree.fit <- train(classe~.,data = train_new,
                  method = "rpart",
                  trControl = trControl)
# result 
print(tree.fit)
```

    ## CART 
    ## 
    ## 19622 samples
    ##    57 predictor
    ##     5 classes: 'A', 'B', 'C', 'D', 'E' 
    ## 
    ## No pre-processing
    ## Resampling: Cross-Validated (10 fold, repeated 5 times) 
    ## Summary of sample sizes: 17659, 17660, 17661, 17660, 17660, 17661, ... 
    ## Resampling results across tuning parameters:
    ## 
    ##   cp          Accuracy   Kappa     
    ##   0.04023643  0.5048782  0.36486652
    ##   0.04567251  0.4242283  0.22967822
    ##   0.11515454  0.3271340  0.06514272
    ## 
    ## Accuracy was used to select the optimal model using the largest value.
    ## The final value used for the model was cp = 0.04023643.

``` r
# plot
fancyRpartPlot(tree.fit$finalModel)
```

![](final_files/figure-gfm/unnamed-chunk-2-1.png)<!-- -->

I use 10-fold cv and repeat it for 5 times.

# Prediction

``` r
pred <- predict(tree.fit,test_df)
pred
```

    ##  [1] B A C A C C C C A A C C C A C C C B B C
    ## Levels: A B C D E
