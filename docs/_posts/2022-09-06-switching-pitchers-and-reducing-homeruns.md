    #mean home runs by batters since
    ggplot(dt[pitch_seq == 1 & batters_since_starter_max_batter %between% c(-10,10) & 
                ((count.outs.end < 3 & batters_since_starter_max_batter == 0) | 
                   (batters_since_starter_max_batter != 0) ),
              .(mean(hit, na.rm=T)),
              by = .(batters_since_starter_max_batter)]) + 
      geom_line(aes(x=batters_since_starter_max_batter, y=V1)) 

![](/assets/unnamed-chunk-1-1.png)

    ggplot(dt[pitch_seq == 1 & batters_since_starter_max_batter %between% c(-10,10) & 
                ((count.outs.end < 3 & batters_since_starter_max_batter == 0) | 
                   (batters_since_starter_max_batter != 0) ) &
                about.halfInning == "top"
                ,
              .(mean(hit, na.rm=T)),
              by = .(batters_since_starter_max_batter, home_manager_full_name)]) + 
      geom_point(aes(x=batters_since_starter_max_batter, y=V1, color = home_manager_full_name)) 

![](/assets/unnamed-chunk-1-2.png)

    ggplot() + 
      geom_point(data = dt[pitch_seq == 1 & batters_since_starter_max_batter %between% c(-10,10) & 
                ((count.outs.end < 3 & batters_since_starter_max_batter == 0) | 
                   (batters_since_starter_max_batter != 0) ) &
                about.halfInning == "top" &
                  batters_since_starter_max_batter != 0 &
                  !(home_manager_full_name %in% c("","Rod Barajas"))
                ,
              .(mean(hit, na.rm=T)),
              by = .(batters_since_starter_max_batter, home_manager_full_name)],
              aes(x=batters_since_starter_max_batter, y=V1, color = home_manager_full_name)) +
      geom_text(data = dt[pitch_seq == 1 & batters_since_starter_max_batter %between% c(-10,10) & 
                ((count.outs.end < 3 & batters_since_starter_max_batter == 0) | 
                   (batters_since_starter_max_batter != 0) ) &
                about.halfInning == "top" &
                  batters_since_starter_max_batter == 0 &
                  !(home_manager_full_name %in% c("","Rod Barajas"))
                ,
              .(mean(hit, na.rm=T)),
              by = .(batters_since_starter_max_batter, home_manager_full_name)],
              aes(x=batters_since_starter_max_batter, y=V1, 
                  color = home_manager_full_name, label=home_manager_full_name))

![](/assets/unnamed-chunk-1-3.png)
