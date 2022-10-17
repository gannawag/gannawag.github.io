In game 6 of the 2020 world series, the world witnessed one of the most
dominant pitching performances of world series history…for the first
four innings.

![](/assets/br_snell_tweet.png) Blake Snell was dominating for the Tampa
Bay Rays, until the 5th inning, when Manager Kevin Cash decided to pull
him out of the game.

![](/assets/br_pbp_snell_1.png)

Check out the baseball reference win probability over the course of the
game. Here is the last batter Snell faced:
![](/assets/br_wp_snell_1.png)

Snell gave up a hit, but the win probability was still in favor of the
Rays beating the Dodgers. It wasn’t until the next batter, the dodger’s
all star Mookie Betts, got a double off of replacement pitcher Nick
Anderson that the win probability shifted to the dodger’s favor
![](/assets/br_wp_snell_2.png) The Dodgers went on to win the game and
the World Series.

The decision to pull Snell was widely criticized and was seen as a major
turning point in the game. Here is how Manager Kevin Cash defended the
decision: ![](/assets/cash_snell_tweet.png)

Now I actually like this defense because it seems like a rational
attempt to predict the outcome of the next batter, and more
specifically, the difference in predicted outcomes if Snell stays in
versus if he comes out. This is what I think all managers should base
their pitching decisions on. Though I admit using only one or two
predictor variables (in this case number of times through the lineup and
quality of next batter) leaves room for improvement.

Side note reminder that machine learning algorithms are able make
predictions based on many more predictors than just one - but we’ll get
to that later.

Now even though I like Cash’s defense as only being about the next
batter, part of me wonders how much he was giving in to what behavioral
economists call “availability bias”.

![](/assets/wiki_availability.png)

Availability bias (or availability heuristic or recency bias) is the
idea that humans treat available information as more relevant than it
really is.

In this case Blake Snell had just given up a hit (though it was only his
second of the game) and the recency of that hit must have been weighing
on Kevin Cash’s mind (even if he didn’t admit it).
![](/assets/br_pbp_snell_1.png)

After that game, I couldn’t stop seeing possible cases of availability
bias in the MLB, and like any good data scientist, I decided to take
this question to the data.

I started with this figure ![](/assets/conditional_outs_17oct2022-1.png)
This figure shows the percent of at bats that were a starting pitcher’s
last at bat of the game, split by whether the pitcher got an out on the
previous batter or not. This is a figure about the probability of
getting pulled, conditional on result of the last AB. So among all the
at bats that ended in an out, 2.7% of them were the final at bat for the
pitcher that game. On the other hand, if the pitcher did not get an out,
only 2.4% of those at bats were the pitcher’s final at bat that game.
Now this seems contrary to intuition: shouldn’t a pitcher get pulled
*less* frequently if he got an out than if he didn’t? The problem with
the above figure is that it includes end-of-inning substitutions. And
100% of those substitutions come after a pitcher got an out (otherwise
the inning wouldn’t be over). These end-of-inning substitutions are the
most frequent kind of substitution, and not what happened to Blake Snell
in 2020. He was pulled *mid-inning* after **not** getting an out.

Look at what happens if I limit to mid-inning switches
![](/assets/conditional_outs_17oct2022_2-1.png) You can see that while
there is less than a 1 percentage point difference for substitutions at
the end of innings between the two different results of the previous at
bats, there is a 2 percentage point difference for substitutions
mid-inning. Maybe those end of inning switches were planned a little
more in advance and have nothing to do with recency bias?

Finally, keeping in mind Kevin Cash’s reasoning for pulling Snell, if I
split by number of times through the lineup:
![](/assets/conditional_outs_17oct2022_3-1.png)

Now you can see a relatively big difference in the probability of being
pulled after getting an out versus after not getting an out for the
third time through the lineup, and this is exactly where Blake Snell was
sitting in game 6 of the 2020 world series. In other words, getting
pulled after giving up a hit or a walk the third time through the lineup
is a lot more common than getting pulled after getting an out. So it
*maybe* should not have been as big of a surprise for Snell to get taken
out since he had just given up a hit.

![](/assets/17oct2022_zoom_outs-1.png) But here’s the thing: just
because Blake did not get an out on the last AB (and he was starting his
3rd time through the lineup), we can’t be sure he wouldn’t have gotten
an out on his NEXT batter. This might be Snell’s argument - just because
he just gave up a hit, it was just one of two hits over his whole
outing.

This version of the plot shows the probability of getting an out
conditional on the outcome of the last AB:
![](/assets/17oct2022_zoom_outs_2-1.png) If a pitcher got an out last
AB, on average the current AB will result in an out 49.9% of the time
(versus 51.3% of the time if the last AB did not end in an out). And for
completeness here is the version with all three times through the
lineup: ![](/assets/17oct2022_zoom_outs_3-1.png)

But surely these means mask a lot of heterogeneity - maybe it’s just the
case that when Blake Snell gives up a hit, he actually becomes more
likely than the average pitcher to give up another. We can test that
theory by looking at individual pitcher means.

![](/assets/17oct2022_scatters-1.png)

This plot shows the percent of times an individual pitcher gets an out
after getting an out on the last batter vs after not getting an out on
the last batter - excluding the first AB of an inning. Each point is a
pitcher, so the pitchers in the top right quadrant are just more likely
to get outs than the average pitchers, regardless of what happened on
the past AB. The pitchers on the bottom right quadrant are a little more
streaky than the average pitcher - they are more likely to get an out
after getting an out on the last batter, but less likely to get an out
after giving up a hit on the last batter. The pitchers in the top left
are “anti-streaky” they get outs after giving up hits and give up hits
after getting outs. And finally the pitchers in the bottom left quadrant
are less likely than the average pitcher to get outs regardless of what
happened on the last AB. I boldly labeled them “bad” here…but there’s a
lot going on here- for example this is the 3rd time through the lineup
and the cutoff between bad and good is pretty sharp - if you are
slightly below average this labels you as bad vs if you are slightly
above average this labels you as good. This X shows Blake Snell. It
looks like he is…about average. After getting an out, he is actually a
little less likely than the average pitcher to get another out. But
after NOT getting an out, he is about as likely as the average pitcher
to get an out on the next batter.

Now compare that plot to this one that shows individual pitcher means
for getting pulled mid inning conditional on the result of the previous
AB: ![](/assets/17oct2022_scatters_2-1.png)

I kept the colors from the last figure so you can see where the pitchers
in the top right quadrant of the outs figure end up in this pulls
figure. In this figure the top right quadrant are the guys getting
pulled most often regardless of what happened in the last AB. The bottom
right are the pitchers that are pulled after getting an out but get to
stay in after giving up a hit (their manager thinks they are the
anti-streaky pitchers). The top left quadrant are pitchers who are
pulled if they give up a hit but left in if they get an out (managers
think they are streaky). And the bottom left quadrant are the pitchers
that managers leave in regardless of what happened on the last AB. What
you might have expected was that the colors here would be in the same
quadrants - that the streaky pitchers should be the ones you take out
after giving up a hit and the anti-streaky hitters are the ones you
leave in after a hit. But there seems to be a bit of a mix:
![](/assets/17oct2022_scatters_3-1.png)

If you look at the breakdown of pitchers by the two sets of quadrants,
you can see there are patterns in the right direction -for example the
good pitchers are left in the most regardless of what happened on the
last batter, and the streaky pitchers are pulled most after giving up a
hit (and the anti-streaky pitchers are pulled after getting an out). But
why isn’t this a perfect match? Why are there so many good pitchers
pulled no matter what happened on the last AB and why are there so many
ant-streaky pitchers pulled after giving up a hit?

I have 2 theories: 1- managers aren’t that great at data analytics and
they give in to the recency or availability bias too much 2- using only
the result of the previous AB is not an accurate enough predictor of
what will happen on the next AB (so managers might actually be really
good at predicting the outcome of the next AB and they are using more
input variables than just the result of the past AB)

I suspect it’s probably a blend of the two…but who knows? This is where
machine learning comes in. Imagine if we could build a model that
predicts the outcome of the next AB using all the available data up to
that point. Sort of like an AI manager. How would it stack up against
the real managers?

There are some super interesting snags in this prediction problem. For
instance we only observe the outcome of the next AB for the given
pitcher if the manager chooses to leave him in. In part 2 of this post,
I will show you exactly how I dealt with that and how my AI manager does
compared to real managers, and I’ll show you whether my AI manager would
have left Blake Snell in game 6 of the 2020 world series.

Stay tuned for more!

<!-- Here is a plot that has been on my mind for a while: -->
<!-- ```{r prob_of_last_ab, echo=F} -->
<!-- dt[, start_last_ab_dummy := ifelse(batters_since_starter_max_batter == 0, 1, 0)] -->
<!-- dt[pitch_seq==1, start_last_ab_dummy_last := shift(start_last_ab_dummy), by = .(game_date, game_pk, about.halfInning)] -->
<!-- dt[, last_batter_result_out_string := ifelse(last_batter_result_out == 1, "Out", "Non-Out")] -->
<!-- dt[is.na(last_batter_result_out_string), last_batter_result_out_string := "First AB of Inning"] -->
<!-- dt[,last_batter_result_out_string := factor(last_batter_result_out_string, levels = c("Non-Out","Out", "First AB of Inning")) ] -->
<!-- dt[, result_out_string := ifelse(result_out == 1, "Out", "Non-Out")] -->
<!-- dt[pitch_seq==1, next_batter_batside := shift(matchup.batSide.code, type="lead"), by = .(game_date, game_pk, about.halfInning)] -->
<!-- dt[pitch_seq==1, pitcher_hand_next_batter_side := paste0(matchup.pitchHand.code, "-",next_batter_batside)] -->
<!-- dt[, total_pitcher_games := length(unique(game_pk)), by = matchup.pitcher.fullName] -->
<!-- ggplot(dt[pitch_seq==1 & !is.na(last_batter_result_out), .(mean = mean(start_last_ab_dummy_last), -->
<!--                                                            sd = sd(start_last_ab_dummy)), by = .(last_batter_result_out_string)]) +  -->
<!--   geom_bar(aes(x=last_batter_result_out_string, y=mean, fill=as.factor(last_batter_result_out_string)), stat="identity") +  -->
<!--     geom_text(aes(fill=last_batter_result_out_string, y=mean, x=last_batter_result_out_string, label=scales::label_percent(accuracy = 0.1)(mean)),  -->
<!--             position = position_dodge(width = 0.8), vjust = -0.2) +  -->
<!--   # geom_errorbar(aes(x=last_batter_result_out, ymin=mean-sd,ymax=mean+sd, fill=as.factor(last_batter_result_out)),width=0.25) +  -->
<!--   theme_bw() +  -->
<!--   theme(legend.position = "none", -->
<!--         text = element_text(size = 19)) +  -->
<!--   xlab("Result of Previous At Bat") + ylab("Percent of all At Bats\nthat were Starters' Final At Bats") +  -->
<!--   scale_y_continuous(labels=scales::label_percent()) +  -->
<!--     scale_fill_brewer(name="Result of\nPrevious At Bat",palette = "Set1")  -->
<!-- #split by end or mid inning -->
<!-- ggplot(dt[pitch_seq==1 #& !is.na(last_batter_result_out_string)    & last_batter_result_out_string != "First AB of Inning" -->
<!--           ,  -->
<!--    .(mean = mean(start_last_ab_dummy)), by = .(result_out_string, mid_inning_dummy)]) +  -->
<!--   geom_bar(aes(fill=result_out_string, y=mean, x=mid_inning_dummy), stat="identity", position="dodge") +  -->
<!--     geom_text(aes(fill=result_out_string, y=mean, x=mid_inning_dummy, label=scales::label_percent(accuracy = 0.1)(mean)),  -->
<!--             position = position_dodge(width = 0.8), vjust = -0.2) +  -->
<!--   # geom_errorbar(aes(x=last_batter_result_out, ymin=mean-sd,ymax=mean+sd, fill=as.factor(last_batter_result_out)),width=0.25) +  -->
<!--   theme_bw() +  -->
<!--   theme(legend.position = c(0.8,0.8), -->
<!--         text = element_text(size = 19)) +  -->
<!--   xlab("Previous AB was Mid-Inning or End of Inning") + ylab("Percent of all ABs\nthat were Starters' Final At Bats") +  -->
<!--   scale_y_continuous(labels=scales::label_percent()) +  -->
<!--   scale_fill_brewer(name="Result of\nPrevious At Bat",palette = "Set1")   -->
<!-- ggplot(dt[pitch_seq==1  & next_batter_times_through_lineup < 4 & starting_pitcher_dummy == 1 & total_pitcher_games > 5,  -->
<!--    .(mean = mean(start_last_ab_dummy)),  -->
<!--    by = .(last_batter_result_out_string,next_batter_times_through_lineup, mid_inning_dummy)]) +  -->
<!--   geom_bar(aes(fill=last_batter_result_out_string, y=mean, x=mid_inning_dummy), stat="identity", position="dodge") +  -->
<!--   geom_text(aes(fill=last_batter_result_out_string, y=mean, x=mid_inning_dummy, label=scales::label_percent(accuracy = 0.1)(mean)),  -->
<!--             position = position_dodge(width = 0.8), vjust = -0.2, hjust=0.4) +  -->
<!--   # geom_errorbar(aes(x=last_batter_result_out, ymin=mean-sd,ymax=mean+sd, fill=as.factor(last_batter_result_out)),width=0.25) +  -->
<!--   theme_bw() +  -->
<!--   theme(legend.position = c(0.3,0.7), -->
<!--         text = element_text(size = 19), -->
<!--         axis.text.x = element_text(size = 10)) +  -->
<!--   xlab("At Bat was Mid-Inning or End of Inning") + ylab("Percent of all At Bats\nthat were Starters' Final At Bats") +  -->
<!--   scale_y_continuous(labels=scales::label_percent()) +  -->
<!--   scale_fill_brewer(name="Result of\nPrevious At Bat",palette = "Set1") +  -->
<!--   facet_wrap(~paste0("Times thru Lineup: ", next_batter_times_through_lineup)) -->
<!-- # dt[pitch_seq==1  & next_batter_times_through_lineup < 4 & starting_pitcher_dummy == 1,  -->
<!-- #    .(mean = mean(start_last_ab_dummy), -->
<!-- #      sum = sum(start_last_ab_dummy), -->
<!-- #      .N), by = .(last_batter_result_out_string,next_batter_times_through_lineup, mid_inning_dummy)] -->
<!-- #  -->
<!-- #  -->
<!-- # ggplot(dt[pitch_seq==1  & next_batter_times_through_lineup ==3 & starting_pitcher_dummy == 1,  -->
<!-- #    .(mean = mean(start_last_ab_dummy)),  -->
<!-- #    by = .(last_batter_result_out_string,next_batter_times_through_lineup, mid_inning_dummy, pitcher_hand_next_batter_side)]) +  -->
<!-- #   geom_bar(aes(fill=last_batter_result_out_string, y=mean, x=mid_inning_dummy), stat="identity", position="dodge") +  -->
<!-- #   geom_text(aes(fill=last_batter_result_out_string, y=mean, x=mid_inning_dummy, label=scales::label_percent(accuracy = 0.1)(mean)),  -->
<!-- #             position = position_dodge(width = 0.8), vjust = -0.2) +  -->
<!-- #   # geom_errorbar(aes(x=last_batter_result_out, ymin=mean-sd,ymax=mean+sd, fill=as.factor(last_batter_result_out)),width=0.25) +  -->
<!-- #   theme_bw() +  -->
<!-- #   theme(legend.position = c(0.3,0.7), -->
<!-- #         text = element_text(size = 19), -->
<!-- #         axis.text.x = element_text(size = 10)) +  -->
<!-- #   xlab("At Bat was Mid-Inning or End of Inning") + ylab("Percent of all At Bats\nthat were Starters' Final At Bats") +  -->
<!-- #   scale_y_continuous(labels=scales::label_percent()) +  -->
<!-- #   scale_fill_discrete(name="Result of\nPrevious At Bat") +  -->
<!-- #   facet_grid(pitcher_hand_next_batter_side~paste0("Times thru Lineup: ", next_batter_times_through_lineup)) -->
<!-- ggplot(dt[pitch_seq==1  & next_batter_times_through_lineup == 3 & starting_pitcher_dummy == 1 & total_pitcher_games > 5 & mid_inning_dummy == "Mid-Inning",  -->
<!--    .(mean = mean(start_last_ab_dummy)),  -->
<!--    by = .(last_batter_result_out_string,next_batter_times_through_lineup, mid_inning_dummy)]) +  -->
<!--   geom_bar(aes(fill=last_batter_result_out_string, y=mean, x=mid_inning_dummy), stat="identity", position="dodge") +  -->
<!--   geom_text(aes(fill=last_batter_result_out_string, y=mean, x=mid_inning_dummy, label=scales::label_percent(accuracy = 0.1)(mean)),  -->
<!--             position = position_dodge(width = 0.8), vjust = -0.2, hjust=0.4, size = 5) +  -->
<!--   # geom_errorbar(aes(x=last_batter_result_out, ymin=mean-sd,ymax=mean+sd, fill=as.factor(last_batter_result_out)),width=0.25) +  -->
<!--   theme_bw() +  -->
<!--   theme(legend.position = c(0.8,0.7), -->
<!--         text = element_text(size = 19), -->
<!--         axis.text.x = element_text(size = 15)) +  -->
<!--   xlab("At Bat was Mid-Inning or End of Inning") + ylab("Percent of all At Bats\nthat were Starters' Final At Bats") +  -->
<!--   scale_y_continuous(labels=scales::label_percent()) +  -->
<!--   scale_fill_brewer(name="Result of\nPrevious At Bat",palette = "Set1") +  -->
<!--   facet_wrap(~paste0("Times thru Lineup: ", next_batter_times_through_lineup)) -->
<!-- ggplot(dt[pitch_seq==1  & next_batter_times_through_lineup == 3 & starting_pitcher_dummy == 1 & total_pitcher_games > 5 & mid_inning_dummy == "Mid-Inning",  -->
<!--    .(mean = mean(result_out)),  -->
<!--    by = .(last_batter_result_out_string,next_batter_times_through_lineup, mid_inning_dummy)]) +  -->
<!--   geom_bar(aes(fill=last_batter_result_out_string, y=mean, x=mid_inning_dummy), stat="identity", position="dodge") +  -->
<!--   geom_text(aes(fill=last_batter_result_out_string, y=mean, x=mid_inning_dummy, label=scales::label_percent(accuracy = 0.1)(mean)),  -->
<!--             position = position_dodge(width = 0.8), vjust = -0.2, hjust=0.4, size = 5) +  -->
<!--   # geom_errorbar(aes(x=last_batter_result_out, ymin=mean-sd,ymax=mean+sd, fill=as.factor(last_batter_result_out)),width=0.25) +  -->
<!--   theme_bw() +  -->
<!--   theme(legend.position = c(0.8,0.3), -->
<!--         text = element_text(size = 19), -->
<!--         axis.text.x = element_text(size = 15)) +  -->
<!--   xlab("At Bat was Mid-Inning or End of Inning") + ylab("Percent of all At Bats\nthat Ended in Outs") +  -->
<!--   scale_y_continuous(labels=scales::label_percent()) +  -->
<!--   scale_fill_brewer(name="Result of\nPrevious At Bat",palette = "Set1") +  -->
<!--   facet_wrap(~paste0("Times thru Lineup: ", next_batter_times_through_lineup)) -->
<!-- # ggplot(dt[pitch_seq==1  & next_batter_times_through_lineup == 3 & starting_pitcher_dummy == 1 & total_pitcher_games > 5 & mid_inning_dummy == "Mid-Inning",  -->
<!-- #    .(mean = mean(result_hit_or_walk)),  -->
<!-- #    by = .(last_batter_result_out_string,next_batter_times_through_lineup, mid_inning_dummy)]) +  -->
<!-- #   geom_bar(aes(fill=last_batter_result_out_string, y=mean, x=mid_inning_dummy), stat="identity", position="dodge") +  -->
<!-- #   geom_text(aes(fill=last_batter_result_out_string, y=mean, x=mid_inning_dummy, label=scales::label_percent(accuracy = 0.1)(mean)),  -->
<!-- #             position = position_dodge(width = 0.8), vjust = -0.2, hjust=0.4, size = 5) +  -->
<!-- #   # geom_errorbar(aes(x=last_batter_result_out, ymin=mean-sd,ymax=mean+sd, fill=as.factor(last_batter_result_out)),width=0.25) +  -->
<!-- #   theme_bw() +  -->
<!-- #   theme(legend.position = c(0.8,0.3), -->
<!-- #         text = element_text(size = 19), -->
<!-- #         axis.text.x = element_text(size = 15)) +  -->
<!-- #   xlab("At Bat was Mid-Inning or End of Inning") + ylab("Percent of all At Bats\nthat Ended in Hits or Walks") +  -->
<!-- #   scale_y_continuous(labels=scales::label_percent()) +  -->
<!-- #   scale_fill_brewer(name="Result of\nPrevious At Bat",palette = "Set1") +  -->
<!-- #   facet_wrap(~paste0("Times thru Lineup: ", next_batter_times_through_lineup)) -->
<!-- ``` -->
<!-- It shows the mean number of home runs relative to the last batter a starting pitcher faces, with a few caveats. The first caveat is that I limit to managers who managed at least 50 games that season. This will come back later on, but the idea is to get rid of managers who only managed one or two games that season.  -->
<!-- The second caveat has to do with the dotted line part of the figure. The solid lines include *all* at bats for the given "batters since starting pitcher final batter". But the dotted line for the final batter faced by the starter *excludes* final batters where the at bat was the last at bat of the inning. In other words, the dashed line only includes situations where the pitcher was pulled *mid-inning*. (If you want to know the percent of at-bats that were the final at bat of the inning as well as the final batter faced by the starter...it's very close to 0% since the final at bat of an inning is never a home run unless it's a walk off and starters rarely make it to the final innings of a game any more).  -->
<!-- The idea here is that starting pitchers who are pulled mid-inning give up a home run on the last batter they face more frequently than on the second-to-last batter they face. Does giving up a home run cause the manager to pull the pitcher? It looks like there is a "ramping up" - as pitchers get closer to the end of the line, they get increasingly likely to give up a home run. So maybe managers "shorten the leash" as a pitcher is giving up more home runs, and then once there is a another home run after the leash has been shortened, the manager caves and pulls the pitcher.  -->
<!-- Incidentally, the figure is even more striking when looking at any hit and not just home runs:  -->
<!-- And for walks/hbp:  -->
<!-- The BB/HBP figure is interesting to me for a few reasons: the obvious ramping up before the pitcher is taken out and the subsequent *higher* baseline walk rate of the relievers. Maybe the strategy is to pull pitchers when their expected walk percentage is higher than the predicted walk rate of the reliever?  -->
<!-- ### What about number of times through the lineup?  -->
<!-- A proxy for "shortened leash" is to use starter's number of times through the lineup. The conventional wisdom is that the third time through the lineup is more difficult for pitchers.  -->
<!-- Splitting by times through lineup seems to suggest two things: (1) there might be slightly more of a ramping up effect in the second time through the lineup and (2) there seems to be some forgiveness at during the first time through the lineup and the start of the third time through.  -->
<!-- ## But is this the *best* strategy?  -->
<!-- Managers are human. The reason I am interested in the above figures is that I keep wondering the extent to which managers give in to human biases when deciding to pull the pitcher. If the last batter hit a home run, does that mean the next batter will? As I hinted at above the strategy to pull the pitcher should ignore sunk costs in a sense. The past batter outcome matters much less than the predicted outcome of the next batter. So in the end managers need to be good at predicting what will happen on the next batter. Seems like a perfect problem for machine learning.  -->
<!-- ## Time series dependence of hits -->
<!-- My first thought here is that different pitchers probably have different "time" dependence. Meaning that some pitchers might be better at bouncing back from giving up a hit than others.  -->
<!-- So first, as a baseline, how often do pitchers get outs in general? Here is a plot showing the distribution of starter pitchers' share of at bats that end in outs.  -->
<!-- So the mean pitcher gets an out in roughly 63% of at bats. But the question now is whether the outcome of the previous batter predicts the outcome of the current batter. In other words, given that the last batter got a out, what is the probability that the current batter will also get out? Is that conditional probability greater than if the previous batter did not get out? To look at this question, I started by splitting my overall mean by the result of the previous batter.   -->
<!-- If the distribution of the starting pitcher share of at bats resulting in an out conditional on the previous at bat ending in an out is greater than conditional on the previous at bat not ending in an out, then this is evidence for serial correlation, since it would suggest that when the previous at bat was an out pitchers are more likely to get an out on the current at bat.  -->
<!-- Looking only at the differences:  -->
<!-- The first time through the lineup, the average pitcher is actually slightly *less* likely to get an out on the current batter when the previous at bat was an out vs when the previous at bat was not an out. The difference is small - less than one percentage point. The second and third times through the lineup the magnitude of the average differences decreases, but the sign flips, meaning the average pitcher is slightly more likely to get an out on the current batter when the previous at bat was an out vs when it was not an out.  -->
<!-- But as is visible from the plot, the distributions are relatively spread out, implying there is wide variation in pitcher "streakiness". If a pitcher has a high percentage of outs after getting an out last AB, but a low percentage of outs after not getting an out last AB, that suggests they are "streaky". If they have high percentages of outs regarldess of what happened on last AB, they are just a good pitcher. -->
<!-- So the question is: are the streaky pitchers equally likely to be taken out after a hit as the non-streaky pitchers? The idea here is to try to proxy for what will happen on the next at bat. If a pitcher is not streaky it might make sense to leave him in if the last at bat was not an out.  -->
<!-- This plot gives an approximation of streakiness. The pitchers in the top right get a higher percentage of outs regardless of what happened on the last AB. They are consistently good. The pitchers in the bottom left quadrant on the other hand, are consistently bad. The players in the bottom right are the streaky players. They get are more likely to get an out if they got on about on the last AB than if they didn't get an out on the last AB. The players in the top left are "reverse" streaky. They are more likely to get an out on the current AB if they did *not* get an out on the last AB than if they did get an out on the last AB.  -->
<!-- Splitting the pitchers into these four groups gives a straightforward way to test for differences in final AB behavior. If the streaky pitchers (bottom right of the above figure) give up a hit, the manager might be more justified in pulling them since they are less likely to get an out on the next AB. This means the share of hits or walks on streaky pitcher's final AB should be higher than the other types of players.  -->
<!-- On the other hand, the "reverse" streaky players should have the lowest share of hits/walks on their final AB. In fact we would expect them to have the highest share of *outs* on their final AB, since getting an out for this group actually might predict that the next batter is likely to not get out.  -->
<!-- My first conclusion from this figure is that all types of pitchers see a spike in hits or walks on their last batter. The streaky pitchers (third row) seem to have the biggest spike. This makes sense - managers seems to be doing a good job pulling these pitchers to prevent a hit or walk on the next AB. The "bad" pitchers (top row) have a clear ramping up which seems to suggest a short leash strategy by the managers. The good pitchers (bottom row) have the smallest spike on their last AB - again this makes sense since managers seem to rightfully trust that they will come back on the next batter.  -->
<!-- One surprising result is that the reverse streaky pitchers (row 2) also have a spike (though unsurprisingly, it is lower than the streaky or bad pitchers).  -->
<!-- We can throw this data into a regression to add controls and measure the spike magnitude. Just regress the indicator for hit or walk on a full set of "batters since pitcher final AB" indicators. \[1(Non-Out) = \sum \beta_{\tau} 1(batter since = \tau) + \epsilon\].  -->
<!-- All this taken together seems to suggest that managers are good, but not perfect at predicting what will happen on the next AB (especially when looking at the reverse streaky pitchers). Should streaky players be pulled at the same frequency as bad pitchers? Do managers have access to a more accurate prediction algorithm than just pitcher streakiness? In part 2 I will use machine learning to predict the outcome of the next AB and see if managers are acting based off what they expect to happen next instead of what happened last.  -->
<!-- ## Links -->
<!-- Video on how I got the data for this post: https://youtu.be/hihO-vgAjS8  -->
<!-- Contact me if you have any suggestions or ideas for future posts: https://www.linkedin.com/in/grant-gannaway-321ba326/ -->
<!-- ### Coding things I learned on this post -->
<!-- - Caching things has unexpected effects on memory usage -->
<!-- - Might be better to get the final dataset as small as possible so we don't need to load the whole thing every knit -->
