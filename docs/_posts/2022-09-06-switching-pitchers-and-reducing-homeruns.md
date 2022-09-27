Here is a plot that has been on my mind for a while:

    ggplot(dt[pitch_seq == 1 & batters_since_starter_max_batter %between% c(-10,10) & 
                ((count.outs.end < 3 & batters_since_starter_max_batter == 0) | 
                   (batters_since_starter_max_batter != 0) ) &
                ((home_manager_total_games > 50 & about.halfInning == "top") |
                (away_manager_total_games > 50 & about.halfInning == "bottom"))
              ,
              .(mean(hr, na.rm=T), sd(hr, na.rm=T)),
              by = .(batters_since_starter_max_batter)]) + 
      geom_line(aes(x=batters_since_starter_max_batter, y=V1)) + 
      theme_bw() + 
      theme(text = element_text(size = 23)) + 
      ylab("Share of at-bats\nResulting in HR") + 
      xlab('Batters "since" Starting Pitcher Final Batter\n(Starting Pitcher Final Batter = 0)') + 
      scale_y_continuous(labels = scales::label_percent(accuracy = 1))

![](/assets/plots1-1.png)

It shows the mean number of home runs relative to the last batter a
starting pitcher faces.
