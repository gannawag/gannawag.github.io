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

Looking only at the differences: ![](/assets/diff-1.png) The first time
through the lineup, the average pitcher is actually slightly *less*
likely to get an out on the current batter when the previous at bat was
an out vs when the previous at bat was not an out. The difference is
small - less than one percentage point. The second and third times
through the lineup the magnitude of the average differences decreases,
but the sign flips, meaning the average pitcher is slightly more likely
to get an out on the current batter when the previous at bat was an out
vs when it was not an out.

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

![](/assets/pitcher_ranks-1.png)![](/assets/pitcher_ranks-2.png) This
plot gives an approximation of streakiness. The pitchers in the top
right get a higher percentage of outs regardless of what happened on the
last AB. They are consistently good. The pitchers in the bottom left
quadrant on the other hand, are consistently bad. The players in the
bottom right are the streaky players. They get are more likely to get an
out if they got on about on the last AB than if they didn’t get an out
on the last AB. The players in the top left are “reverse” streaky. They
are more likely to get an out on the current AB if they did *not* get an
out on the last AB than if they did get an out on the last AB.

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

![](/assets/unnamed-chunk-1-1.png)![](/assets/unnamed-chunk-1-2.png)![](/assets/unnamed-chunk-1-3.png)
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
$Non-Out ~ \Sum \beta\_{\tau} 1(batter since = \tau) + \epsilon$.

![](/assets/reg-1.png)

All this taken together seems to suggest that managers are good, but not
perfect at predicting what will happen on the next AB (especially when
looking at the reverse streaky pitchers). Should streaky players be
pulled at the same frequency as bad pitchers? Do managers have access to
a more accurate prediction algorithm than just pitcher streakiness? In
part 2 I will use machine learning to predict the outcome of the next AB
and see if managers are acting based off what they expect to happen next
instead of what happened last.

## Links

Video on how I got the data for this post:
<https://youtu.be/hihO-vgAjS8>

Contact me if you have any suggestions or ideas for future posts:
<https://www.linkedin.com/in/grant-gannaway-321ba326/>

### Coding things I learned on this post

-   Caching things has unexpected effects on memory usage
-   Might be better to get the final dataset as small as possible so we
    don’t need to load the whole thing every knit
