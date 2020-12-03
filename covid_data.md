COVID-19 Data
================

## U.S. Data

#### Deaths

``` r
covid_us_deaths_df = 
  GET("https://data.cdc.gov/resource/r8kw-7aab.json", query = list("$limit" = 10000)) %>% 
  content("text") %>% 
  fromJSON() %>% 
  as_tibble() %>% 
  janitor::clean_names() %>% 
  mutate(data_as_of = as.Date(data_as_of),
         start_week = as.Date(start_week),
         end_week = as.Date(end_week)) %>% 
  select(-group, -footnote)

save(covid_us_deaths_df, file = "covid_us_deaths_df.rda")
```

#### Cases

\#\#\#\`\`\`{r us cases} get\_all\_cases = function(url) {

all\_cases = vector(“list”, length = 0)

loop\_index = 1 chunk\_size = 99999 DO\_NEXT = TRUE

while (DO\_NEXT) { message(“Getting data, page”, loop\_index)

# all\_cases\[\[loop\_index\]\] =

# GET(url,

# query = list(`$order` = “cdc\_report\_dt”,

# `$limit` = chunk\_size,

# `$offset` = as.integer((loop\_index - 1) \* chunk\_size)

``` 
                   )
      ) %>%
  content("text") %>%
  fromJSON() %>%
  as_tibble()
```

# DO\_NEXT = dim(all\_cases\[\[loop\_index\]\])\[1\] == chunk\_size

# loop\_index = loop\_index + 1

}

all\_cases

}

url = “<https://data.cdc.gov/resource/vbim-akqf.json>”

covid\_us\_cases\_df = get\_all\_cases(url) %\>% bind\_rows() %\>%
mutate(cdc\_report\_dt = as.Date(cdc\_report\_dt)) %\>%
arrange(cdc\_report\_dt) %\>% mutate(case\_id = row\_number()) %\>%
relocate(case\_id) %\>% group\_by(cdc\_report\_dt) %\>%
summarize(cumulative\_cases = max(case\_id))

save(covid\_us\_cases\_df, file = “covid\_us\_cases\_df.rda”)

covid\_us\_cases\_df %\>% ggplot(aes(x = cdc\_report\_dt, y =
cumulative\_cases)) + geom\_point() \#\#\#\`\`\`
