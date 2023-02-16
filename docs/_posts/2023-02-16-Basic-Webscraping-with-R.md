When I first started scraping data from websites I used the amazing
beautiful soup library in python. Recently I discovered the `rvest`
package in R that makes webscraping in R a breeze. I show some basic
code below for scraping data from a website.

One caveat that should always be made about scraping the web: be careful
not to overwhelm the sites’ servers. People just like you worked hard to
make their website and the data is available because of them. If your
scraping code makes a bazillion calls per second on one website, you
will crash the site and ruin it for the rest of us. Don’t wreck the
internet. I have found the simplest way to scrape ethically is build in
rests between scraping calls using something like `Sys.sleep(2)`. That
way the code acts a little more at human speed allowing the website to
“catch up”.

The following code scrapes the sports card website tcdb.com. This is an
amazing website that I really don’t want to crash because I use it all
the time.

The first step is to get the html from the landing page for one year.

    library(rvest)
    library(data.table)

    Sys.sleep(3)
    YEAR <- 2011
    print(YEAR)

    year_url <- paste0("https://www.tcdb.com/ViewAll.cfm/sp/Baseball/year/",YEAR)
    year_page_html <- read_html(year_url)

Next, get all the links that match a particular brand (Topps in this
case) from that landing page into one list that I can loop over.

    all_year_links <- year_page_html %>%
      html_elements("a") %>%
      html_attr("href")

    year_topps_links <- all_year_links[grepl("viewset.*?-topps$", tolower(all_year_links)) |
                                         grepl("viewset.*?-topps-update$", tolower(all_year_links)) ]

Loop over the links to that brand and then repeat the process: get the
links that are on that page for a particular subset of cards (“Rookies”
in this case). Note that I had to add some words to the URL to get to
the right link.

    for (TOPPS_LINK in paste0("https://www.tcdb.com",gsub("ViewSet","Rookies",year_topps_links))){
      Sys.sleep(3)
      print(TOPPS_LINK)
      topps_page_html <- read_html(TOPPS_LINK)

      all_topps_links <- topps_page_html %>%
        html_elements("a") %>%
        html_attr("href")

Within that same loop, get the links for the individual cards. The key
value here is that the program found the links to the cards so I didn’t
need to loop over them.

      year_topps_card_links <- paste0("https://www.tcdb.com",
                                      unique(all_topps_links[grepl("ViewCard",all_topps_links)]))


      for (CARD_LINK in year_topps_card_links){
        Sys.sleep(3)
        print(CARD_LINK)
        year_topps_card_page_html <- read_html(CARD_LINK)

        all_price_links <- year_topps_card_page_html %>%
          html_elements("a") %>%
          html_attr("href")

        price_link <- paste0("https://www.tcdb.com",
                             unique(all_price_links[grepl("Price\\.cfm",all_price_links)]))

You can see the process I’m going through here. It’s just drilling down
to the individual sites I want. If the URLs are easier to loop over,
that would be an easier approach. But in this case, I didn’t know the
set IDs or the card IDs, so I used this drill-down method to loop over
what I know (years) and then find links using the `rvest` pacakge’s
ability to find links on HTML pages.

Once I get to the level I am interested in, I add an if statement to
make sure the page will have what I want, then use `html_table` to
extract the table info.

        if (price_link == "https://www.tcdb.com"){
          print("invalid price link")
        } else {

          price_page_html <- read_html(price_link)

          price_table <- price_page_html %>%
            html_nodes("table") %>%
            html_table()

Finally, I use the `data.table` package to convert the data to a
data.table and save it to a list so that I can keep looping and adding
to the list, then outside the loop I use `rbindlist` to combine all the
data into one table.

          price_dt <- as.data.table(price_table[[4]])
          price_dt[, X1 := NULL]
          names(price_dt) <- as.character(price_dt[1,])
          price_dt <- price_dt[-1,]

          price_dt <- price_dt[Date != ""]

          price_dt[, price := as.numeric(gsub("\\+.*","",gsub("[\\$|S&H]","",Price)))]
          price_dt[grepl("S&H",Price), sh := as.numeric(gsub(".*?\\+","",gsub("[\\$|S&H]","",Price)))]
          price_dt[, tot_price := ifelse(!is.na(sh), price + sh, price)]

          rc_price_list[[price_link]] <- price_dt[,.(date_sold = as.Date(Date, format = "%m-%d-%Y"),
                                                     price, sh, tot_price,
                                                     name = gsub("-"," ",gsub("^-","",gsub("\\d+?-","", gsub(".*?Topps","", price_link)))))]

          Sys.sleep(3)
        }
      }
    }

This process takes a lot of iterating and a lot of reading the page HTML
to find the right text to add to URL links.
