Here is a plot that has been on my mind for a while:

![](/assets/plots1-1.png)

It shows the mean number of home runs relative to the last batter a
starting pitcher faces, with a few caveats. The first caveat is that I
limit to *mid-inning* **pitching changes** but I also INCLUDE all other
at bats that were not pitching changes. So the at bat that is 5 batters
before the starting pitcher’s final batter is inlcuded regardless of
whether it was the final out of the inning or not, but if a pitching
change happens in between innings, I don’t include that starting
pitcher’s final batter faced. The idea here is that I am most interested
in those mid-inning pitching changes. I want to know why managers pulled
their starter mid inning, and I don’t want to cut out the earlier
at-bats that may have ended an inning.

The second caveat is that I limit to managers who managed at least 50
games that season. This will come back later on, but the idea is to get
rid of managers who only managed one or two games that season.

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
