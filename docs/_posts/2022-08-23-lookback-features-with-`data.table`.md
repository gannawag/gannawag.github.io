## What is a “lookback feature”?

When using time series data, one of the most common features to be used
for prediction is *past* values. For example, the temperature today is a
strong predictor of the temperature tomorrow. So to predict temperature,
it is useful to **look back** at past values.

Looking back can take two forms: rolling window or date-time. In the
temperature example, using the mean of the past month temperatures
(rolling window) is a different feature than the specific temperature
from yesterday (date-time).

While these features are often very useful, they aren’t always easy to
access. Data is not often set up to include these features, but instead
the lookback features need to be created.

The goal of this post is to provide a tutorial on how to generate
lookback features in R using the `data.table` package.

## Steps outline

The `data.table` package’s comparative advantage is merge speed. So we
want to exploit that speed in the lookback feature generation. I will
generate the features in five steps:

1.  Add new columns to the original data table equal to the start and
    end of the lookback window, respectively.
2.  Create a new data table that has only the merge key(s), original
    date, window start date, and window end date.
3.  Use the new data table to expand the original data table so each
    original date has an observation for each date in the lookback
    window. Store this exapnded data in a third data table.
4.  Aggregate stats for each original date in the expanded data table
    (ie. take the mean of all values in the lookback period).
5.  Merge the lookback features back on to the original data table.

## Example with baseball data

Given a data set of game-level information for each batter, we need to
get information from previous games. This data set has batter name and
id, game date and id, and game level stats such as mean hit launch
speed. When the batter did not have any batted balls in a game, the mean
launch speed is `NA`. So on August 24, CC Sabathia hit the ball with a
mean launch speed of 96.8 mph.

    ##     game_date   batter_name batter_game_mean_launch_speed
    ## 1: 2019-05-01   CC Sabathia                            NA
    ## 2: 2019-08-24   CC Sabathia                          96.8
    ## 3: 2019-03-20 Ichiro Suzuki                            NA
    ## 4: 2019-03-21 Ichiro Suzuki                            NA
    ## 5: 2019-03-21 Albert Pujols                          94.3
    ## 6: 2019-03-28 Albert Pujols                          87.8

We want our lookback function to be flexible in the sense that we can
easily adjust the window of interest. To do that, set the number of
calendar days to look back with variables `N_start` and `N_end`.

    N_start = 3
    N_end = 1

The first step is to make a new data table that has the original game
date as well as the lookback window for each given game date. In this
case the window is the game date minus `N` to the day before the game
date.

If we want lookback features to be at the batter level, we also need to
include the batter ID.

    game_dt[, paste0("game_date_minus_",N_start) := game_date - N_start]
    game_dt[, paste0("game_date_minus_",N_end) := game_date - N_end]

    #make a data table that has the date range
    (gd <- game_dt[,.(gd = game_date, 
                      start = get(paste0("game_date_minus_",N_start)), 
                      end = get(paste0("game_date_minus_",N_end)), 
                      batter)])

    ##                gd      start        end batter
    ##     1: 2019-05-01 2019-04-28 2019-04-30 282332
    ##     2: 2019-08-24 2019-08-21 2019-08-23 282332
    ##     3: 2019-03-20 2019-03-17 2019-03-19 400085
    ##     4: 2019-03-21 2019-03-18 2019-03-20 400085
    ##     5: 2019-03-21 2019-03-18 2019-03-20 405395
    ##    ---                                        
    ## 52330: 2019-03-20 2019-03-17 2019-03-19 680575
    ## 52331: 2019-03-21 2019-03-18 2019-03-20 680757
    ## 52332: 2019-03-20 2019-03-17 2019-03-19 680776
    ## 52333: 2019-03-21 2019-03-18 2019-03-20 680837
    ## 52334: 2019-03-21 2019-03-18 2019-03-20 682177

Now comes the `data.table` heavy lifting.

First, look at the data for one player, Starlin Castro:

      game_dt[batter == 516770, .(game_date, batter_name, batter_game_mean_launch_speed)]

    ##       game_date    batter_name batter_game_mean_launch_speed
    ##   1: 2019-03-20 Starlin Castro                            NA
    ##   2: 2019-03-28 Starlin Castro                      88.75000
    ##   3: 2019-03-29 Starlin Castro                      86.80000
    ##   4: 2019-03-30 Starlin Castro                      94.07500
    ##   5: 2019-03-31 Starlin Castro                      82.73333
    ##  ---                                                        
    ## 159: 2019-09-25 Starlin Castro                      83.80000
    ## 160: 2019-09-26 Starlin Castro                      72.23333
    ## 161: 2019-09-27 Starlin Castro                      91.50000
    ## 162: 2019-09-28 Starlin Castro                      86.05000
    ## 163: 2019-09-29 Starlin Castro                      86.75000

On March 28 he had a mean game launch speed of 88.75 mph, and had 1 hit.
On March 29, he had a game mean launch speed of 86.8 mph but had 0 hits.
So, if we were to take March 30 as the game date in question, and the
lookback window was 3 days, the mean launch speed in the lookback window
was (88.75 + 86.8) / 2 = 87.75. The mean number of hits in the lookback
period was 0.5. If we take March 31 to be the game date in question, the
lookback mean launch speed was (88.75 + 86.8 + 94.075) / 3 = 89.875 mph,
and the mean number of hits was 0.66. These are the values we expect to
find for Starlin Castro when we implement our lookback function.

The next step is to expand the original data set, so that each original
game date has observations for each of the games in the lookback window.
So for example, March 30 has two observations: one for March 28 and one
for March 29. March 31 has three observations: March 28, 29, and 30. The
variable `gd` here is the now the original game date.

    #get the mean of list of vars for given batter if game dates are in date range
      sum_dt = #unique(
        game_dt[gd,
                .(game_date, gd, batter,batter_name, start, end, game_pk, .SD),
                on = .(batter, game_date >= start, game_date <= end),
                .SDcols = names(game_dt)[grepl("^batter_game_", names(game_dt))
                                               & !grepl("_lb",names(game_dt))]
        ] #only fills in vars for rows in date range
      # )
      
      sum_dt[batter == 516770,.(gd, .SD.batter_game_mean_launch_speed)]

    ##              gd .SD.batter_game_mean_launch_speed
    ##   1: 2019-03-20                                NA
    ##   2: 2019-03-28                                NA
    ##   3: 2019-03-29                          88.75000
    ##   4: 2019-03-30                          88.75000
    ##   5: 2019-03-30                          86.80000
    ##  ---                                             
    ## 409: 2019-09-28                          72.23333
    ## 410: 2019-09-28                          91.50000
    ## 411: 2019-09-29                          72.23333
    ## 412: 2019-09-29                          91.50000
    ## 413: 2019-09-29                          86.05000

The key command here is the `on` function from `data.table`. I passed
three arguments to `on`: the batter id on which to merge, and then two
conditions. The conditions allow merging in a range - less than the end
of the lookback window and more than the start of the lookback window.

I also used `data.table`’s `.SD`, which allows me to efficiently add all
the columns to the aggregation.

Now I can easily manipulate the new expanded data table to get the
aggregated values of the features in the lookback window.

      #remove extra columns
      sum_dt[,c("game_date") := NULL]
      #fix names
      (names(sum_dt) = gsub(".SD.","", names(sum_dt)))

    ##  [1] "gd"                              "batter"                         
    ##  [3] "batter_name"                     "start"                          
    ##  [5] "end"                             "game_pk"                        
    ##  [7] "batter_game_mean_launch_speed"   "batter_game_total_hits"         
    ##  [9] "batter_game_total_at_bats"       "batter_game_total_so"           
    ## [11] "batter_game_total_rbi"           "batter_game_mean_pitch_count_ab"

      #means for each actual game date
      (sum_dt <- sum_dt[, lapply(.SD, mean, na.rm=T), by = .(batter,batter_name, gd), 
                        .SDcols = names(sum_dt) %like% "^batter_game_"]
        )

    ##        batter   batter_name         gd batter_game_mean_launch_speed
    ##     1: 282332          <NA> 2019-05-01                           NaN
    ##     2: 282332          <NA> 2019-08-24                           NaN
    ##     3: 400085          <NA> 2019-03-20                           NaN
    ##     4: 400085 Ichiro Suzuki 2019-03-21                           NaN
    ##     5: 405395          <NA> 2019-03-21                           NaN
    ##    ---                                                              
    ## 46852: 680575          <NA> 2019-03-20                           NaN
    ## 46853: 680757          <NA> 2019-03-21                           NaN
    ## 46854: 680776          <NA> 2019-03-20                           NaN
    ## 46855: 680837          <NA> 2019-03-21                           NaN
    ## 46856: 682177          <NA> 2019-03-21                           NaN
    ##        batter_game_total_hits batter_game_total_at_bats batter_game_total_so
    ##     1:                    NaN                       NaN                  NaN
    ##     2:                    NaN                       NaN                  NaN
    ##     3:                    NaN                       NaN                  NaN
    ##     4:                      0                         2                    0
    ##     5:                    NaN                       NaN                  NaN
    ##    ---                                                                      
    ## 46852:                    NaN                       NaN                  NaN
    ## 46853:                    NaN                       NaN                  NaN
    ## 46854:                    NaN                       NaN                  NaN
    ## 46855:                    NaN                       NaN                  NaN
    ## 46856:                    NaN                       NaN                  NaN
    ##        batter_game_total_rbi batter_game_mean_pitch_count_ab
    ##     1:                   NaN                             NaN
    ##     2:                   NaN                             NaN
    ##     3:                   NaN                             NaN
    ##     4:                     0                             5.5
    ##     5:                   NaN                             NaN
    ##    ---                                                      
    ## 46852:                   NaN                             NaN
    ## 46853:                   NaN                             NaN
    ## 46854:                   NaN                             NaN
    ## 46855:                   NaN                             NaN
    ## 46856:                   NaN                             NaN

      #add identifier to names
      names(sum_dt)[grepl("^batter_game_", names(sum_dt))] <- 
        paste0(names(sum_dt)[grepl("^batter_game_", names(sum_dt))], "_lb_days",N_start)
      
      #delete rows out of date range
      sum_dt <- sum_dt[!is.na(batter_name)]

The final step is to merge the lookback features back on to the master
data table so we can use them for predictions.

      #merge back on to master
      setkey(game_dt, batter,batter_name, game_date)
      setkey(sum_dt, batter,batter_name, gd)
      for (X in names(sum_dt)[!names(sum_dt) %in% c("batter","batter_name","gd")]){
        print(X)
        game_dt[sum_dt, (X) := get(X)]
        
      }

    ## [1] "batter_game_mean_launch_speed_lb_days3"
    ## [1] "batter_game_total_hits_lb_days3"
    ## [1] "batter_game_total_at_bats_lb_days3"
    ## [1] "batter_game_total_so_lb_days3"
    ## [1] "batter_game_total_rbi_lb_days3"
    ## [1] "batter_game_mean_pitch_count_ab_lb_days3"

Spot check Starlin Castro to be sure the method worked as expected:

    game_dt[batter == 516770, .(game_date, batter_name, batter_game_mean_launch_speed, batter_game_mean_launch_speed_lb_days3)]

    ##       game_date    batter_name batter_game_mean_launch_speed
    ##   1: 2019-03-20 Starlin Castro                            NA
    ##   2: 2019-03-28 Starlin Castro                      88.75000
    ##   3: 2019-03-29 Starlin Castro                      86.80000
    ##   4: 2019-03-30 Starlin Castro                      94.07500
    ##   5: 2019-03-31 Starlin Castro                      82.73333
    ##  ---                                                        
    ## 159: 2019-09-25 Starlin Castro                      83.80000
    ## 160: 2019-09-26 Starlin Castro                      72.23333
    ## 161: 2019-09-27 Starlin Castro                      91.50000
    ## 162: 2019-09-28 Starlin Castro                      86.05000
    ## 163: 2019-09-29 Starlin Castro                      86.75000
    ##      batter_game_mean_launch_speed_lb_days3
    ##   1:                                     NA
    ##   2:                                     NA
    ##   3:                               88.75000
    ##   4:                               87.77500
    ##   5:                               89.87500
    ##  ---                                       
    ## 159:                               80.24167
    ## 160:                               79.57500
    ## 161:                               76.99444
    ## 162:                               82.51111
    ## 163:                               83.26111

What about double headers?
