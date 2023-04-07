    (dt <- data.table(iris))

    ##      Sepal.Length Sepal.Width Petal.Length Petal.Width   Species
    ##   1:          5.1         3.5          1.4         0.2    setosa
    ##   2:          4.9         3.0          1.4         0.2    setosa
    ##   3:          4.7         3.2          1.3         0.2    setosa
    ##   4:          4.6         3.1          1.5         0.2    setosa
    ##   5:          5.0         3.6          1.4         0.2    setosa
    ##  ---                                                            
    ## 146:          6.7         3.0          5.2         2.3 virginica
    ## 147:          6.3         2.5          5.0         1.9 virginica
    ## 148:          6.5         3.0          5.2         2.0 virginica
    ## 149:          6.2         3.4          5.4         2.3 virginica
    ## 150:          5.9         3.0          5.1         1.8 virginica

Add columns of summary stats to the original data one at a time

    dt[, mean_slength_by_species := mean(Sepal.Length), by = .(Species)]
    dt[, mean_plength_by_species := mean(Petal.Length), by = .(Species)]
    dt

    ##      Sepal.Length Sepal.Width Petal.Length Petal.Width   Species
    ##   1:          5.1         3.5          1.4         0.2    setosa
    ##   2:          4.9         3.0          1.4         0.2    setosa
    ##   3:          4.7         3.2          1.3         0.2    setosa
    ##   4:          4.6         3.1          1.5         0.2    setosa
    ##   5:          5.0         3.6          1.4         0.2    setosa
    ##  ---                                                            
    ## 146:          6.7         3.0          5.2         2.3 virginica
    ## 147:          6.3         2.5          5.0         1.9 virginica
    ## 148:          6.5         3.0          5.2         2.0 virginica
    ## 149:          6.2         3.4          5.4         2.3 virginica
    ## 150:          5.9         3.0          5.1         1.8 virginica
    ##      mean_slength_by_species mean_plength_by_species
    ##   1:                   5.006                   1.462
    ##   2:                   5.006                   1.462
    ##   3:                   5.006                   1.462
    ##   4:                   5.006                   1.462
    ##   5:                   5.006                   1.462
    ##  ---                                                
    ## 146:                   6.588                   5.552
    ## 147:                   6.588                   5.552
    ## 148:                   6.588                   5.552
    ## 149:                   6.588                   5.552
    ## 150:                   6.588                   5.552

Create a new table with summary stats

    dt[, lapply(.SD, mean), .SDcols = c("Sepal.Length","Petal.Length"), by = .(Species)]

    ##       Species Sepal.Length Petal.Length
    ## 1:     setosa        5.006        1.462
    ## 2: versicolor        5.936        4.260
    ## 3:  virginica        6.588        5.552

Create new columns in the original data with summary stats

    dt[, paste0("species_mean_",c("Sepal.Length","Petal.Length")) := lapply(.SD, mean), .SDcols = c("Sepal.Length","Petal.Length"), by = .(Species)]
    dt

    ##      Sepal.Length Sepal.Width Petal.Length Petal.Width   Species
    ##   1:          5.1         3.5          1.4         0.2    setosa
    ##   2:          4.9         3.0          1.4         0.2    setosa
    ##   3:          4.7         3.2          1.3         0.2    setosa
    ##   4:          4.6         3.1          1.5         0.2    setosa
    ##   5:          5.0         3.6          1.4         0.2    setosa
    ##  ---                                                            
    ## 146:          6.7         3.0          5.2         2.3 virginica
    ## 147:          6.3         2.5          5.0         1.9 virginica
    ## 148:          6.5         3.0          5.2         2.0 virginica
    ## 149:          6.2         3.4          5.4         2.3 virginica
    ## 150:          5.9         3.0          5.1         1.8 virginica
    ##      mean_slength_by_species mean_plength_by_species species_mean_Sepal.Length
    ##   1:                   5.006                   1.462                     5.006
    ##   2:                   5.006                   1.462                     5.006
    ##   3:                   5.006                   1.462                     5.006
    ##   4:                   5.006                   1.462                     5.006
    ##   5:                   5.006                   1.462                     5.006
    ##  ---                                                                          
    ## 146:                   6.588                   5.552                     6.588
    ## 147:                   6.588                   5.552                     6.588
    ## 148:                   6.588                   5.552                     6.588
    ## 149:                   6.588                   5.552                     6.588
    ## 150:                   6.588                   5.552                     6.588
    ##      species_mean_Petal.Length
    ##   1:                     1.462
    ##   2:                     1.462
    ##   3:                     1.462
    ##   4:                     1.462
    ##   5:                     1.462
    ##  ---                          
    ## 146:                     5.552
    ## 147:                     5.552
    ## 148:                     5.552
    ## 149:                     5.552
    ## 150:                     5.552
