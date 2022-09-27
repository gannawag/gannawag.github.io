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
to give up a home run.

![](/assets/plots2-1.png)

### Coding things I learned on this post

-   Caching things has unexpected effects on memory usage
-   Might be better to get the final dataset as small as possible so we
    don’t need to load the whole thing every knit
