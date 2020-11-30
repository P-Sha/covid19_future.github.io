COVID-19 Data
================

## U.S. Data

#### Deaths

``` r
covid_us_deaths_df = 
  GET("https://data.cdc.gov/resource/r8kw-7aab.json", query = list("$limit" = 10000)) %>% 
  content("text") %>% 
  jsonlite::fromJSON() %>% 
  as_tibble() %>% 
  janitor::clean_names() %>% 
  mutate(data_as_of = as.Date(data_as_of),
         start_week = as.Date(start_week),
         end_week = as.Date(end_week)) %>% 
  select(-group, -footnote)

save(covid_us_deaths_df, file = "covid_us_deaths_df.rda")
```

#### Cases

``` r
covid_us_cases_df = 
  GET("https://data.cdc.gov/resource/vbim-akqf.json", query = list("$limit" = 1000)) %>% 
  content("text") %>% 
  jsonlite::fromJSON() %>% 
  as_tibble() %>% 
  janitor::clean_names() %>% 
  mutate(cdc_report_dt = as.Date(cdc_report_dt)) %>% 
  arrange(cdc_report_dt) %>% 
  mutate(case_id = row_number()) %>% 
  relocate(case_id) %>% 
  group_by(cdc_report_dt) %>% 
  summarize(cumulative_cases = max(case_id))
```

    ## `summarise()` ungrouping output (override with `.groups` argument)

``` r
save(covid_us_cases_df, file = "covid_us_cases_df.rda")
```