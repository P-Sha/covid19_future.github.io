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

``` r
get_all_cases = function(url) {
  
  all_cases = vector("list", length = 0)
  
  loop_index = 1
  chunk_size = 99999
  DO_NEXT = TRUE
  
  while (DO_NEXT) {
    message("Getting data, page ", loop_index)
    
    all_cases[[loop_index]] = 
      GET(url,
         query = list(`$order` = "cdc_report_dt",
                       `$limit` = chunk_size,
                       `$offset` = as.integer((loop_index - 1) * chunk_size)
                       )
          ) %>%
      content("text") %>%
      fromJSON() %>%
      as_tibble()
    
    DO_NEXT = dim(all_cases[[loop_index]])[1] == chunk_size
    loop_index = loop_index + 1
  }
  
  all_cases
  
}

url = "https://data.cdc.gov/resource/vbim-akqf.json"

covid_us_cases_df = 
  get_all_cases(url) %>%
  bind_rows() %>% 
  mutate(cdc_report_dt = as.Date(cdc_report_dt)) %>% 
  arrange(cdc_report_dt) %>% 
  mutate(case_id = row_number()) %>% 
  relocate(case_id) %>% 
  group_by(cdc_report_dt) %>% 
  summarize(cumulative_cases = max(case_id))
```

    ## Getting data, page 1

    ## Getting data, page 2

    ## Getting data, page 3

    ## Getting data, page 4

    ## Getting data, page 5

    ## Getting data, page 6

    ## Getting data, page 7

    ## Getting data, page 8

    ## Getting data, page 9

    ## Getting data, page 10

    ## Getting data, page 11

    ## Getting data, page 12

    ## Getting data, page 13

    ## Getting data, page 14

    ## Getting data, page 15

    ## Getting data, page 16

    ## Getting data, page 17

    ## Getting data, page 18

    ## Getting data, page 19

    ## Getting data, page 20

    ## Getting data, page 21

    ## Getting data, page 22

    ## Getting data, page 23

    ## Getting data, page 24

    ## Getting data, page 25

    ## Getting data, page 26

    ## Getting data, page 27

    ## Getting data, page 28

    ## Getting data, page 29

    ## Getting data, page 30

    ## Getting data, page 31

    ## Getting data, page 32

    ## Getting data, page 33

    ## Getting data, page 34

    ## Getting data, page 35

    ## Getting data, page 36

    ## Getting data, page 37

    ## Getting data, page 38

    ## Getting data, page 39

    ## Getting data, page 40

    ## Getting data, page 41

    ## Getting data, page 42

    ## Getting data, page 43

    ## Getting data, page 44

    ## Getting data, page 45

    ## Getting data, page 46

    ## Getting data, page 47

    ## Getting data, page 48

    ## Getting data, page 49

    ## Getting data, page 50

    ## Getting data, page 51

    ## Getting data, page 52

    ## Getting data, page 53

    ## Getting data, page 54

    ## Getting data, page 55

    ## Getting data, page 56

    ## Getting data, page 57

    ## Getting data, page 58

    ## Getting data, page 59

    ## Getting data, page 60

    ## Getting data, page 61

    ## Getting data, page 62

    ## Getting data, page 63

    ## Getting data, page 64

    ## Getting data, page 65

    ## Getting data, page 66

    ## Getting data, page 67

    ## Getting data, page 68

    ## Getting data, page 69

    ## Getting data, page 70

    ## Getting data, page 71

    ## Getting data, page 72

    ## Getting data, page 73

    ## Getting data, page 74

    ## Getting data, page 75

    ## Getting data, page 76

    ## Getting data, page 77

    ## Getting data, page 78

    ## Getting data, page 79

    ## Getting data, page 80

    ## Getting data, page 81

    ## Getting data, page 82

    ## Getting data, page 83

    ## Getting data, page 84

    ## Getting data, page 85

    ## `summarise()` ungrouping output (override with `.groups` argument)

``` r
save(covid_us_cases_df, file = "./covid_us_cases_df.rda")

covid_us_cases_df %>% 
  ggplot(aes(x = cdc_report_dt, y = cumulative_cases)) +
  geom_point()
```

<img src="covid_data_files/figure-gfm/us cases-1.png" width="90%" />
