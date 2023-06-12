---
output: 
  md_document:
    variant: markdown+backtick_code_blocks
    preserve_yaml: TRUE
knit: (function(inputFile, encoding) {
      out_dir <- "_portfolio";
      rmarkdown::render(inputFile,
                        encoding=encoding,
                        output_dir=file.path(dirname(inputFile), out_dir))})
title: Journal of Discourses Scraper
---

Python Google Colab code to scrape Journal of Discourses for text
analysis.

``` python
# Import requests and BeautifulSoup libraries
import requests
from bs4 import BeautifulSoup
import re

from google.colab import drive
drive.mount('/content/drive')

import pandas as pd
```

``` python
# Define the base url for the journal of discourses
base_url = "https://josephsmithfoundation.org/journalofdiscourses/"

# Create an empty list to store the links to each volume
volume_links = []

# Loop through the volume numbers from 1 to 26
for i in range(1, 27):
    # Append the volume number to the base url and add it to the list
    volume_link = base_url + "topics/volumes/volume-" + str(i) + "/?print=print-search"
    volume_links.append(volume_link)

volume_links
```

``` python
volume_text = []

# Loop through each volume link
for volume_link in volume_links:
    print(volume_link)

    # Get the html content of the discourse page
    discourse_page = requests.get(volume_link)
    discourse_page
    # Parse the html content using BeautifulSoup
    discourse_soup = BeautifulSoup(discourse_page.content, "html.parser")

    for (title, text) in zip(discourse_soup.find_all("h1", class_="entry-title"), discourse_soup.find_all("div", class_="entry-content")):
      text = str(text).strip().replace(',','')
      text = re.sub('\n','',text)
      text = re.sub('</div>','\n',text)

      title = str(title).strip().replace(',','')
      title = re.sub('.*?"entry-title">','',title)
      title = re.sub('</h1>','',title)

      heading = re.search(r'<em>(.*?)</em>',text).group(1)

      text = re.sub('<em>.*?</em>','',text)
      text = re.sub('<.*?>','',text)

      row = title + ',' + heading + ',' + ',' + text
      print(row)
      volume_text.append(row)
```

``` python
df=pd.DataFrame( list(reader(volume_text)))
df
```

``` python
filename = 'JofD.csv'

df.to_csv('/content/drive/MyDrive/' + filename)
```
