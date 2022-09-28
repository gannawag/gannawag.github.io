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

### What about number of times through the lineup?

A proxy for “shortened leash” is to use starter’s number of times
through the lineup. The conventional wisdom is that the third time
through the lineup is more difficult for pitchers.

![](/assets/plots_times_thru-1.png) Splitting by times through lineup
seems to suggest two things: (1) there might be slightly more of a
ramping up effect in the third time through the lineup and (2) the
percent of home runs on the final batter is *lower* on the third time
through.

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

### Coding things I learned on this post

-   Caching things has unexpected effects on memory usage
-   Might be better to get the final dataset as small as possible so we
    don’t need to load the whole thing every knit
