Data Wrangling II
================

## Restaurant inspections

``` r
api_url = "https://data.cityofnewyork.us/resource/43nn-pn8j.csv"

rest_inspections = 
  GET(api_url) %>% 
  content("parsed")
```

    ## Parsed with column specification:
    ## cols(
    ##   .default = col_character(),
    ##   camis = col_double(),
    ##   inspection_date = col_datetime(format = ""),
    ##   score = col_double(),
    ##   grade_date = col_datetime(format = ""),
    ##   record_date = col_datetime(format = ""),
    ##   latitude = col_double(),
    ##   longitude = col_double(),
    ##   community_board = col_double(),
    ##   bin = col_double(),
    ##   bbl = col_double()
    ## )

    ## See spec(...) for full column specifications.

``` r
rest_inspections = 
  GET(api_url, query = list("$limit" = 50000)) %>% 
  content("parsed")
```

    ## Parsed with column specification:
    ## cols(
    ##   .default = col_character(),
    ##   camis = col_double(),
    ##   inspection_date = col_datetime(format = ""),
    ##   score = col_double(),
    ##   grade_date = col_datetime(format = ""),
    ##   record_date = col_datetime(format = ""),
    ##   latitude = col_double(),
    ##   longitude = col_double(),
    ##   community_board = col_double(),
    ##   bin = col_double(),
    ##   bbl = col_double()
    ## )
    ## See spec(...) for full column specifications.

``` r
rest_inspections %>% 
  count(boro)
```

    ## # A tibble: 6 x 2
    ##   boro              n
    ##   <chr>         <int>
    ## 1 0                24
    ## 2 Bronx          4501
    ## 3 Brooklyn      12484
    ## 4 Manhattan     19962
    ## 5 Queens        11404
    ## 6 Staten Island  1625

``` r
rest_inspections %>% 
  count(boro, grade) %>% 
  pivot_wider(names_from = grade, values_from = n)
```

    ## # A tibble: 6 x 8
    ##   boro              A     B     Z  `NA`     C     N     P
    ##   <chr>         <int> <int> <int> <int> <int> <int> <int>
    ## 1 0                12     1     1    10    NA    NA    NA
    ## 2 Bronx          1726   423    23  2144   138    16    31
    ## 3 Brooklyn       4872   846    40  6245   309    77    95
    ## 4 Manhattan      8053  1191    57  9942   491   112   116
    ## 5 Queens         4722   706    46  5503   287    71    69
    ## 6 Staten Island   675    97     5   803    28     8     9

``` r
rest_inspections =
  rest_inspections %>% 
  filter(grade %in% c("A", "B", "C", boro != "0"))
```

Let’s look at pizza places.

``` r
rest_inspections %>% 
  filter(str_detect(dba, "Pizza"))
```

    ## # A tibble: 5 x 26
    ##    camis dba   boro  building street zipcode phone cuisine_descrip…
    ##    <dbl> <chr> <chr> <chr>    <chr>  <chr>   <chr> <chr>           
    ## 1 5.01e7 Gino… Quee… 1038     BEACH… 11691   7184… Pizza           
    ## 2 5.01e7 Jets… Manh… 112      9 AVE… 10011   3122… Pizza           
    ## 3 4.12e7 Luig… Broo… 4704     5 AVE… 11220   7184… Pizza           
    ## 4 5.01e7 Kenn… Bronx 131      WEST … 10468   7184… Chicken         
    ## 5 5.00e7 Radi… Manh… 142      WEST … 10019   2128… Pizza/Italian   
    ## # … with 18 more variables: inspection_date <dttm>, action <chr>,
    ## #   violation_code <chr>, violation_description <chr>, critical_flag <chr>,
    ## #   score <dbl>, grade <chr>, grade_date <dttm>, record_date <dttm>,
    ## #   inspection_type <chr>, latitude <dbl>, longitude <dbl>,
    ## #   community_board <dbl>, council_district <chr>, census_tract <chr>,
    ## #   bin <dbl>, bbl <dbl>, nta <chr>

``` r
## only a few - probably likely related to string issues - only looking for exactly spelled "Pizza"

rest_inspections %>% 
  filter(str_detect(dba, "PIZZA"))
```

    ## # A tibble: 1,187 x 26
    ##     camis dba   boro  building street zipcode phone cuisine_descrip…
    ##     <dbl> <chr> <chr> <chr>    <chr>  <chr>   <chr> <chr>           
    ##  1 4.14e7 SOFI… Broo… 2822     CONEY… 11235   7185… Pizza           
    ##  2 5.01e7 ROUN… Stat… 1957     VICTO… 10314   7185… Pizza/Italian   
    ##  3 5.01e7 BRAV… Manh… 360      7 AVE… 10001   2122… American        
    ##  4 4.13e7 JOHN… Broo… 5806     5 AVE… 11220   7184… Pizza/Italian   
    ##  5 5.00e7 J & … Broo… 5507     5 AVE… 11220   7184… Pizza           
    ##  6 5.00e7 NY P… Quee… 91-14    SUTPH… 11435   7186… American        
    ##  7 5.01e7 BIG … Stat… 4069     HYLAN… 10308   7183… Pizza/Italian   
    ##  8 4.15e7 PREV… Manh… 122      EAST … 10017   2125… Pizza           
    ##  9 5.01e7 F & … Manh… 153      AVENU… 10009   3479… Pizza           
    ## 10 5.00e7 JUMB… Manh… 3594     BROAD… 10031   2122… Pizza           
    ## # … with 1,177 more rows, and 18 more variables: inspection_date <dttm>,
    ## #   action <chr>, violation_code <chr>, violation_description <chr>,
    ## #   critical_flag <chr>, score <dbl>, grade <chr>, grade_date <dttm>,
    ## #   record_date <dttm>, inspection_type <chr>, latitude <dbl>, longitude <dbl>,
    ## #   community_board <dbl>, council_district <chr>, census_tract <chr>,
    ## #   bin <dbl>, bbl <dbl>, nta <chr>

``` r
## solution - do some changes to dataset?

rest_inspections %>% 
  mutate(dba = str_to_upper(dba)) %>% 
  filter(str_detect(dba, "PIZZ"))
```

    ## # A tibble: 1,557 x 26
    ##     camis dba   boro  building street zipcode phone cuisine_descrip…
    ##     <dbl> <chr> <chr> <chr>    <chr>  <chr>   <chr> <chr>           
    ##  1 4.14e7 SOFI… Broo… 2822     CONEY… 11235   7185… Pizza           
    ##  2 5.01e7 ROUN… Stat… 1957     VICTO… 10314   7185… Pizza/Italian   
    ##  3 5.01e7 BRAV… Manh… 360      7 AVE… 10001   2122… American        
    ##  4 4.13e7 JOHN… Broo… 5806     5 AVE… 11220   7184… Pizza/Italian   
    ##  5 4.09e7 THE … Quee… 60-48    MYRTL… 11385   7183… Pizza/Italian   
    ##  6 5.00e7 J & … Broo… 5507     5 AVE… 11220   7184… Pizza           
    ##  7 5.01e7 J & … Quee… 4013     82ND … 11373   7184… Spanish         
    ##  8 5.00e7 NY P… Quee… 91-14    SUTPH… 11435   7186… American        
    ##  9 5.01e7 BIG … Stat… 4069     HYLAN… 10308   7183… Pizza/Italian   
    ## 10 5.00e7 MAMA… Stat… 2146     FORES… 10303   7188… Pizza/Italian   
    ## # … with 1,547 more rows, and 18 more variables: inspection_date <dttm>,
    ## #   action <chr>, violation_code <chr>, violation_description <chr>,
    ## #   critical_flag <chr>, score <dbl>, grade <chr>, grade_date <dttm>,
    ## #   record_date <dttm>, inspection_type <chr>, latitude <dbl>, longitude <dbl>,
    ## #   community_board <dbl>, council_district <chr>, census_tract <chr>,
    ## #   bin <dbl>, bbl <dbl>, nta <chr>

``` r
rest_inspections %>% 
  mutate(dba = str_to_upper(dba)) %>% 
  filter(str_detect(dba, "PIZZ")) %>% 
  count(boro, grade) %>% 
  pivot_wider(names_from = grade, values_from = n)
```

    ## # A tibble: 6 x 4
    ##   boro              A     B     C
    ##   <chr>         <int> <int> <int>
    ## 1 0                 2    NA    NA
    ## 2 Bronx           171    47    17
    ## 3 Brooklyn        349    56    21
    ## 4 Manhattan       356    60    18
    ## 5 Queens          312    36    18
    ## 6 Staten Island    73    18     3

``` r
rest_inspections %>% 
  mutate(dba = str_to_upper(dba)) %>% 
  filter(str_detect(dba, "PIZZ")) %>% 
  mutate(boro = fct_relevel(boro, "Manhattan")) %>% 
  ggplot(aes(x = boro)) +
  geom_bar() +
  facet_wrap(. ~ grade)
```

<img src="dwii_examples_files/figure-gfm/unnamed-chunk-6-1.png" width="90%" />

``` r
rest_inspections %>% 
  mutate(dba = str_to_upper(dba)) %>% 
  filter(str_detect(dba, "PIZZ")) %>% 
  mutate(boro = fct_infreq(boro)) %>% 
  ggplot(aes(x = boro)) +
  geom_bar() +
  facet_wrap(. ~ grade)
```

<img src="dwii_examples_files/figure-gfm/unnamed-chunk-6-2.png" width="90%" />

``` r
rest_inspections %>% 
  mutate(dba = str_to_upper(dba)) %>% 
  filter(str_detect(dba, "PIZZ")) %>% 
  mutate(boro = fct_infreq(boro),
         boro = str_replace(boro, "Brooklyn", "HipsterVille")
         ) %>% 
  ggplot(aes(x = boro)) +
  geom_bar() +
  facet_wrap(. ~ grade)
```

<img src="dwii_examples_files/figure-gfm/unnamed-chunk-6-3.png" width="90%" />

``` r
## key point here is str_repace after the factor command - so it changed backed to character. so becareful.  might need to use record or fct_recode
```

## Napoleon Dynamite

Get some Napoleon Dynamite Amazon reviews. need rvest package to scrape
data

``` r
nap_dyn_url = "https://www.amazon.com/product-reviews/B00005JNBQ/ref=cm_cr_arp_d_viewopt_rvwer?ie=UTF8&reviewerType=avp_only_reviews&sortBy=recent&pageNumber=5"

napoleon_html = read_html(nap_dyn_url)

review_titles =
  napoleon_html %>% 
  html_nodes(".a-text-bold span") %>% 
  html_text()

review_text =
  napoleon_html %>% 
  html_nodes(".review-text-content span") %>%
  html_text()

nap_df =
  tibble(
    titles = review_titles,
    text = review_text
  )
```
