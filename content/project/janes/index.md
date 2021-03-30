---
date: "2021-01-07T00:00:00Z"
external_link: "https://github.com/cgpeltier/janes"
image:
  caption: 
  focal_point: Smart
links:
summary: janes is an R package to interact with the Janes API.
tags:
- R packages
title: "{janes}"
url_code: ""
url_pdf: ""
url_slides: ""
url_video: ""
---


{janes} is an R package that wraps the Janes API, allowing users to easily download analysis-ready tidy data frames. It is intended for both internal Janes use and customer use, works with all API endpoints, and supports parallel processing to dramatically speed up download speeds. This is part of Janes' initial SDK development for its connected data products. 

{janes} has one primary function, `get_janes`, which allows users to select a country, post date, and API endpoint (among other arguments) to filter their search. 

Because the Janes API can return deeply nested JSON depending on the endpoint, {janes} has to be flexible to work across endpoints with their varying data structures. Key to this is the ability to `unnest_wider` for multiple list columns recursively until all layers of the nested list columns are flattened. The following function allows for that, with thanks to [several](https://stackoverflow.com/questions/60820415/unnest-wider-multiple-columns) [Stackoverflow](https://stackoverflow.com/questions/63291143/unnest-wider-only-if-columns-exists-in-r) users:


```
unnest_all2 <- function(data){

  list_cols <- data %>%
    select(where(~ is.list(.) && !is.null(unlist(.)))) %>%
    names()

  data_non_list <- data %>%
    select(!where(is.list))


  if(length(list_cols) != 0){

    map_dfc(list_cols, ~
              data %>%
              select(.x) %>%
              unnest_wider(c(!!.x), names_sep= "_", names_repair = 'unique')) %>%
      bind_cols(data_non_list, .)


  } else {

    data %>% janitor::clean_names()
  }

}
```

