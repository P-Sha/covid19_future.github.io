COVID-19 Data
================

## U.S. Death Data

``` r
covid_us_deaths_df = 
  GET("https://data.cdc.gov/resource/r8kw-7aab.json") %>% 
  content("text") %>% 
  jsonlite::fromJSON() %>% 
  janitor::clean_names() %>% 
  mutate(data_as_of = as.Date(data_as_of),
         start_week = as.Date(start_week),
         end_week = as.Date(end_week)) %>% 
  select(-group, -footnote)
```
