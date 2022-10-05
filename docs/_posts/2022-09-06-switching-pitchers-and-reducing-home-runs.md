Here is a plot that has been on my mind for a while:

![](/assets/plots1-1.png)

It shows the mean number of home runs relative to the last batter a
starting pitcher faces, with a few caveats. The first caveat is that I
limit to managers who managed at least 50 games that season. This will
come back later on, but the idea is to get rid of managers who only
managed one or two games that season.

The second caveat has to do with the dotted line part of the figure. The
solid lines include *all* at bats for the given “batters since starting
pitcher final batter”. But the dotted line for the final batter faced by
the starter *excludes* final batters where the at bat was the last at
bat of the inning. In other words, the dashed line only includes
situations where the pitcher was pulled *mid-inning*. (If you want to
know the percent of at-bats that were the final at bat of the inning as
well as the final batter faced by the starter…it’s very close to 0%
since the final at bat of an inning is never a home run unless it’s a
walk off and starters rarely make it to the final innings of a game any
more).

The idea here is that starting pitchers who are pulled mid-inning give
up a home run on the last batter they face more frequently than on the
second-to-last batter they face. Does giving up a home run cause the
manager to pull the pitcher? It looks like there is a “ramping up” - as
pitchers get closer to the end of the line, they get increasingly likely
to give up a home run. So maybe managers “shorten the leash” as a
pitcher is giving up more home runs, and then once there is a another
home run after the leash has been shortened, the manager caves and pulls
the pitcher.

Incidentally, the figure is even more striking when looking at any hit
and not just home runs:

![](/assets/plots_hits-1.png) And for walks/hbp:
![](/assets/plots_walks-1.png) The BB/HBP figure is interesting to me
for a few reasons: the obvious ramping up before the pitcher is taken
out and the subsequent *higher* baseline walk rate of the relievers.
Maybe the strategy is to pull pitchers when their expected walk
percentage is higher than the predicted walk rate of the reliever?

![](/assets/plots_hits_or_walks-1.png)

### What about number of times through the lineup?

A proxy for “shortened leash” is to use starter’s number of times
through the lineup. The conventional wisdom is that the third time
through the lineup is more difficult for pitchers.

![](/assets/plots_times_thru-1.png) Splitting by times through lineup
seems to suggest two things: (1) there might be slightly more of a
ramping up effect in the second time through the lineup and (2) there
seems to be some forgiveness at during the first time through the lineup
and the start of the third time through.

## But is this the *best* strategy?

Managers are human. The reason I am interested in the above figures is
that I keep wondering the extent to which managers give in to human
biases when deciding to pull the pitcher. If the last batter hit a home
run, does that mean the next batter will? As I hinted at above the
strategy to pull the pitcher should ignore sunk costs in a sense. The
past batter outcome matters much less than the predicted outcome of the
next batter. So in the end managers need to be good at predicting what
will happen on the next batter. Seems like a perfect problem for machine
learning.

## Time series dependence of hits

My first thought here is that different pitchers probably have different
“time” dependence. Meaning that some pitchers might be better at
bouncing back from giving up a hit than others.

So first, as a baseline, how often do pitchers get outs in general? Here
is a plot showing the distribution of starter pitchers’ share of at bats
that end in outs. ![](/assets/baseline_outs-1.png) So the mean pitcher
gets an out in roughly 63% of at bats. But the question now is whether
the outcome of the previous batter predicts the outcome of the current
batter. In other words, given that the last batter got a out, what is
the probability that the current batter will also get out? Is that
conditional probability greater than if the previous batter did not get
out? To look at this question, I started by splitting my overall mean by
the result of the previous batter.

If the distribution of the starting pitcher share of at bats resulting
in an out conditional on the previous at bat ending in an out is greater
than conditional on the previous at bat not ending in an out, then this
is evidence for serial correlation, since it would suggest that when the
previous at bat was an out pitchers are more likely to get an out on the
current at bat.

Looking only at the differences:

    plot_dt = dcast(dt[starting_pitcher_dummy == 1 & pitch_seq == 1 & !is.na(last_batter_result_out) ,
       .(mean_out_current = mean(result_out)),
       by = .( times_through_lineup, last_batter_result_out, matchup.pitcher.fullName)],
       matchup.pitcher.fullName + times_through_lineup ~ last_batter_result_out,
       value.var = "mean_out_current"
    )[, .(matchup.pitcher.fullName, times_through_lineup, last_batter_result_out = `1`,last_batter_result_nonout=`0`,
          last_out_minus_last_nonout = `1` - `0`)]
    sum_plot_dt_1= plot_dt[times_through_lineup < 4, .(mean = mean(last_out_minus_last_nonout, na.rm=T)), by = .(times_through_lineup)]


    ggdraw(align_legend(
    ggplot(plot_dt[times_through_lineup < 4]) + 
      geom_density(aes(x=100*last_out_minus_last_nonout, color = as.factor(times_through_lineup))) + 
      theme_bw() + 
      theme(text = element_text(size = 20), 
            axis.text = element_text(size = 15),
            legend.position = c(0.77,0.75),
            legend.background = element_blank(),
            legend.direction = "horizontal",
            legend.title = element_text(size = 20),
            strip.text = element_text(size = 10)) + 
      xlab("Percentage Point Difference in\nShare of at bats resulting in an out\n(excluding sac fly and sac bunt)") + 
      ylab("Density") + 
      scale_x_continuous(breaks = seq(-90,90,30)) + 
      geom_vline(data = sum_plot_dt_1,
                 aes(xintercept = mean, color = as.factor(times_through_lineup))) + 
      scale_color_discrete(name="Times Through Lineup", guide = guide_legend(title.position = "top")) + 
      geom_text(aes(x=-60,y=0.025,label=paste0("Difference in\nShare of ABs resulting in an out averages:\n",
                                            "First time through lineup: ",
                                            100*round(sum_plot_dt_1[times_through_lineup ==1]$mean,3),
                                            "pp\nSecond time through lineup: ",
                                            100*round(sum_plot_dt_1[times_through_lineup ==2]$mean,3),
                                            "pp\nThird time through lineup: ",
                                            100*round(sum_plot_dt_1[times_through_lineup ==3]$mean,3),
                                            "pp"
                                            ))) 
    )) 

    ## Warning: Removed 22 rows containing non-finite values (stat_density).

![](/assets/diff-1.png) The first time through the lineup, the average
pitcher is actually slightly *less* likely to get an out on the current
batter when the previous at bat was an out vs when the previous at bat
was not an out. The difference is small - less than one percentage
point. The second and third times through the lineup the magnitude of
the average differences decreases, but the sign flips, meaning the
average pitcher is slightly more likely to get an out on the current
batter when the previous at bat was an out vs when it was not an out.

But as is visible from the plot, the distributions are relatively spread
out, implying there is wide variation in pitcher “streakiness”. If a
pitcher has a high percentage of outs after getting an out last AB, but
a low percentage of outs after not getting an out last AB, that suggests
they are “streaky”. If they have high percentages of outs regarldess of
what happened on last AB, they are just a good pitcher.

So the question is: are the streaky pitchers equally likely to be taken
out after a hit as the non-streaky pitchers? The idea here is to try to
proxy for what will happen on the next at bat. If a pitcher is not
streaky it might make sense to leave him in if the last at bat was not
an out.

    ## Warning: Removed 22 rows containing missing values (geom_point).

![](/assets/pitcher_ranks-1.png) This plot gives an approximation of
streakiness. The pitchers in the top right get a higher percentage of
outs regardless of what happened on the last AB. They are consistently
good. The pitchers in the bottom left quadrant on the other hand, are
consistently bad. The players in the bottom right are the streaky
players. They get are more likely to get an out if they got on about on
the last AB than if they didn’t get an out on the last AB. The players
in the top left are “reverse” streaky. They are more likely to get an
out on the current AB if they did *not* get an out on the last AB than
if they did get an out on the last AB.

Splitting the pitchers into these four groups gives a straightforward
way to test for differences in final AB behavior. If the streaky
pitchers (bottom right of the above figure) give up a hit, the manager
might be more justified in pulling them since they are less likely to
get an out on the next AB. This means the share of hits or walks on
streaky pitcher’s final AB should be higher than the other types of
players.

On the other hand, the “reverse” streaky players should have the lowest
share of hits/walks on their final AB. In fact we would expect them to
have the highest share of *outs* on their final AB, since getting an out
for this group actually might predict that the next batter is likely to
not get out.

    ## Warning in `[.data.table`(dt, , `:=`(quantile_diff, NULL)): Column
    ## 'quantile_diff' does not exist to remove

    ## Warning in `[.data.table`(dt, , `:=`(quantile_out_out, NULL)): Column
    ## 'quantile_out_out' does not exist to remove

    ## Warning in `[.data.table`(dt, , `:=`(quantile_nonout_out, NULL)): Column
    ## 'quantile_nonout_out' does not exist to remove

    ## `geom_smooth()` using formula 'y ~ x'

    ## Warning: Removed 3 rows containing non-finite values (stat_smooth).

    ## Warning in qt((1 - level)/2, df): NaNs produced

    ## Warning in qt((1 - level)/2, df): NaNs produced

    ## Warning in qt((1 - level)/2, df): NaNs produced

    ## Warning in qt((1 - level)/2, df): NaNs produced

    ## Warning: Removed 3 rows containing missing values (geom_point).

    ## Warning in max(ids, na.rm = TRUE): no non-missing arguments to max; returning
    ## -Inf

    ## Warning in max(ids, na.rm = TRUE): no non-missing arguments to max; returning
    ## -Inf

    ## Warning in max(ids, na.rm = TRUE): no non-missing arguments to max; returning
    ## -Inf

    ## Warning in max(ids, na.rm = TRUE): no non-missing arguments to max; returning
    ## -Inf

![](/assets/unnamed-chunk-1-1.png)

    ##     batters_since_starter_max_batter times_through_lineup quantile_out_out
    ##  1:                                0                    1                1
    ##  2:                                0                    1                2
    ##  3:                                0                    1                2
    ##  4:                                0                    1                1
    ##  5:                                0                    2                1
    ##  6:                                0                    2                2
    ##  7:                                0                    2                1
    ##  8:                                0                    2                2
    ##  9:                                0                    3                2
    ## 10:                                0                    3                1
    ## 11:                                0                    3                1
    ## 12:                                0                    3                2
    ##     quantile_nonout_out        V1        V2
    ##  1:                   1 0.8214286 0.3149704
    ##  2:                   2 0.5263158 0.2294157
    ##  3:                   1 0.8333333 0.3806935
    ##  4:                   2 0.7000000 0.0000000
    ##  5:                   2 0.7727273 0.2908034
    ##  6:                   1 0.8333333 0.3541688
    ##  7:                   1 0.8095238 0.3309438
    ##  8:                   2 0.6964286 0.2877364
    ##  9:                   2 0.7507599 0.2883111
    ## 10:                   2 0.8010899 0.2658308
    ## 11:                   1 0.8379121 0.2485084
    ## 12:                   1 0.8145897 0.2883111

![](/assets/unnamed-chunk-1-2.png)![](/assets/unnamed-chunk-1-3.png)![](/assets/unnamed-chunk-1-4.png)
My first conclusion from this figure is that all types of pitchers see a
spike in hits or walks on their last batter. The streaky pitchers (third
row) seem to have the biggest spike. This makes sense - managers seems
to be doing a good job pulling these pitchers to prevent a hit or walk
on the next AB. The “bad” pitchers (top row) have a clear ramping up
which seems to suggest a short leash strategy by the managers. The good
pitchers (bottom row) have the smallest spike on their last AB - again
this makes sense since managers seem to rightfully trust that they will
come back on the next batter.

One surprising result is that the reverse streaky pitchers (row 2) also
have a spike (though unsurprisingly, it is lower than the streaky or bad
pitchers).

We can throw this data into a regression to add controls and measure the
spike magnitude. Just regress the indicator for hit or walk on a full
set of “batters since pitcher final AB” indicators.
`Non-Out ~ \Sum \beta_{\tau} 1(batter since = \tau) + \epsilon`.

    for (X in seq(-15,15)){
      print(X)
      dt[, paste0("bs_",X) := ifelse(batters_since_starter_max_batter == X, 1, 0)]
    }

    ## [1] -15
    ## [1] -14
    ## [1] -13
    ## [1] -12
    ## [1] -11
    ## [1] -10
    ## [1] -9
    ## [1] -8
    ## [1] -7
    ## [1] -6
    ## [1] -5
    ## [1] -4
    ## [1] -3
    ## [1] -2
    ## [1] -1
    ## [1] 0
    ## [1] 1
    ## [1] 2
    ## [1] 3
    ## [1] 4
    ## [1] 5
    ## [1] 6
    ## [1] 7
    ## [1] 8
    ## [1] 9
    ## [1] 10
    ## [1] 11
    ## [1] 12
    ## [1] 13
    ## [1] 14
    ## [1] 15

    dt[, quantiles_pasted := paste0(quantile_out_out,"-",quantile_nonout_out)]
    dt[, teams_pasted := paste0(home_team,"-",away_team)]

    reg <- lm(result_hit_or_walk ~ 
                  as.factor(quantiles_pasted):(`bs_-15` + `bs_-14` + `bs_-13` + `bs_-12` + `bs_-11` + `bs_-10` + `bs_-9` + `bs_-8` + `bs_-7` + `bs_-6` + 
                                                                   `bs_-5` + `bs_-4` + `bs_-3` + `bs_-2` + #`bs_-1` + 
                 bs_0 + bs_1 + bs_2 + bs_3 + bs_4 + bs_5 + bs_6 + bs_7 + bs_8 + bs_9 + bs_10 + bs_11 + bs_12 + bs_13 + bs_14 + bs_15) +
                 as.factor(about.inning) + as.factor(times_through_lineup) + about.isTopInning + home_team + away_team +teams_pasted+ result.awayScore + result.homeScore + pitchNumber 
                 ,
               data = dt[pitch_seq == 1 & batters_since_starter_max_batter %between% c(-15,15) & 
                ((count.outs.end < 3 & batters_since_starter_max_batter == 0) |
                   (batters_since_starter_max_batter != 0) ) &
                  times_through_lineup < 4 &
                  !is.na(quantile_diff) &
                ((home_manager_total_games > 50 & about.halfInning == "top") |
                (away_manager_total_games > 50 & about.halfInning == "bottom")),])
    reg_dt <- data.table(summary(reg)$coefficients)
    reg_dt[, coefs := row.names(summary(reg)$coefficients)]
    reg_dt[, bs := as.numeric(gsub("`","",gsub(".*?:bs_","", coefs)))]

    ## Warning in eval(jsub, SDenv, parent.frame()): NAs introduced by coercion

    reg_dt[!is.na(bs), quantiles := gsub("as.factor.quantiles_pasted.","",gsub(":bs_.*","", coefs))]
    reg_dt <- rbindlist(list(reg_dt, data.table(bs=rep(-1,4), quantiles = c("1-1","1-2","2-1","2-2"), Estimate = rep(0,4), `Std. Error` = rep(0,4))), fill=T)

    ggplot(reg_dt[!is.na(quantiles) & bs == 0]) + 
      geom_bar(aes(x=quantiles,y=Estimate, fill = quantiles), stat = "identity")

![](/assets/reg-1.png)

All this taken together seems to suggest that managers are good, but not
perfect at predicting what will happen on the next AB (especially when
looking at the reverse streaky pitchers). Should streaky players be
pulled at the same frequnecy as bad pitchers? Do managers have access to
a more accurate prediction algorithm than just pitcher streakiness? One
more step before I show any actual machine learning predictions: lets
see if certain managers are better than others at making predictions.

### Coding things I learned on this post

-   Caching things has unexpected effects on memory usage
-   Might be better to get the final dataset as small as possible so we
    don’t need to load the whole thing every knit
